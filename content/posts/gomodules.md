---
title: "Introduction to Go Modules"
date: 2019-03-21T20:54:45-08:00
draft: true
tags: ["golang"]
---
# Table of Contents
1. [Preface](#preface)
2. [Why Go Modules](#whygomodules)
3. [Philosophy](#philosophy)
4. [Example](#example)

## Preface
All the content for this post was gathered by reading the [7 part blog series by Russ Cox](https://research.swtch.com/vgo) and other notes. There is also an [introductory post](https://blog.golang.org/using-go-modules) on the Go blog. I would encourange you to read them as well. 

## Why Go Modules
Before jumping into the workings of Go modules, we should first understand why we need it in the first place. The current state of Go development has some drawbacks:

* All code needs to be in the $GOPATH/src directory. Such a structure is not compatible with many developer workflows. Why can't I work in a different directory ?
* It depends on the existence of tools such as [git](https://git-scm.com/), [bzr](http://bazaar.canonical.com/en/), [fossil](https://www.fossil-scm.org/index.html/doc/trunk/www/index.wiki) etc on the host because `go get` essentially just clones the repo based on where it is stored.
* No guarantee that builds of the same codebase will be identical.
    
    >`go get` will look first locally. If not present it will fetch the latest. `go get -u` will always fetch the latest. Both these options depend on the current state of $GOPATH.

## Philosophy
In order to understand Go modules, you have to understand a few key concepts.

### Import Compatibility Rule
>If an old package and a new package have the same import path, the new package must be backwards-compatible with the old package.

### Minimal Version Selection
>Defaults to using the oldest allowed version of every package involved in the build. These exclusions and replacements do not apply when the module is being built as a dependency of some other module. This gives users full control over how their own programs build, but not over how other people's programs build.

### Builds should be Reproducible / Verifiable / Verified
>*Reproducible* is one that, when repeated, produces the same result.
>*Verifiable* is one that records enough information to be precise about exactly how to repeat it.
>*Verified* is one that checks that it is using the expected source code.

## Example
Let's explain with an example. We will first use go modules to create two versions of a library and push that module to github. Then we will create an executable, that uses this newly created library.

### Module Creation

The library we create will print out messages in different colors. All code for this is available on [github](https://github.com/jimmyislive/gomodexample). Here are the steps to create this library:

* Create directory structure and populate `main.go`. I created the following directory structure *outside* my $GOPATH:

    {{< highlight bash >}}
    gomodexample
        cmd
            gomodexample
                main.go
    {{< / highlight >}}

    The contents of main.go is:

    {{< highlight go "linenos=table" >}}
    package gomodexample

    import "github.com/fatih/color"

    // PrintInRed prints the given message in red
    func PrintInRed(msg string) {
            color.Red(msg)
    }

    // PrintInGreen prints the given message in green
    func PrintInGreen(msg string) {
            color.Green(msg)
    }

    // PrintInYellow prints the given message in yellow
    func PrintInYellow(msg string) {
            color.Yellow(msg)
    }

    // PrintInBlue prints the given message in blue
    func PrintInBlue(msg string) {
            color.Blue(msg)
    }
    {{< / highlight >}}

* In the root of the directory structure initialize the module

    {{< highlight bash >}}
    go mod init github.com/jimmyislive/gomodexample
    {{< / highlight >}}

    This creates two files in the root: `go.mod` and `go.sum`. (These files are always checked into source control)

    If you `cat go.mod`, you won't see anything interesting:
    {{< highlight bash >}}
    module github.com/jimmyislive/gomodexample
    {{< / highlight >}}
* Do a `go build ./...` in the root dir. `go.mod` now contains much more info i.e using info in this file, builds are now *Reproducible*:
    {{< highlight bash >}}
    jjohn$ go list -m all
    github.com/jimmyislive/gomodexample
    github.com/fatih/color v1.7.0
    github.com/mattn/go-colorable v0.1.1
    github.com/mattn/go-isatty v0.0.7
    golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
    {{< / highlight >}}

    As you can see, the `go.mod` file now contains the exact dependencies and versions which are required for this program to work.

    The `go.sum` contains cryptographic checksums for the different versions of dependencies currently being used. i.e. builds are *verified*

* Tag the code and push to github
    {{< highlight bash >}}
    git tag v1.0.0
    git push origin v1.0.0
    {{< / highlight >}}
* We will now add a new function and tag that version of the library v1.1.0:

    {{< highlight go "linenos=table" >}}

    // PrintInCyan prints the given message in cyan
    func PrintInCyan(msg string) {
            color.Cyan(msg)
    }
    {{< / highlight >}}
* Thus, both v1.0.0 and v1.1.0 are now available on [github](https://github.com/jimmyislive/gomodexample).

We have now packaged our library `gomodexample` as a module and it can be used by anybody.

### Module Use
Let's try using the library we just created. Following similar steps as above:

* Create the executable
    {{< highlight bash >}}
    mkdir -p ~/gomoduse/cmd/gomoduse
    touch gomoduse/cmd/gomoduse/main.go
    {{< / highlight >}}

    Contents would be like:
    {{< highlight go "linenos=table" >}}
    package main

    import "github.com/jimmyislive/gomodexample/cmd/gomodexample"

    func main() {
        gomodexample.PrintInRed("I hope i'm in red")
        gomodexample.PrintInGreen("I hope i'm in green")
        gomodexample.PrintInBlue("I hope i'm in blue")
        gomodexample.PrintInYellow("I hope i'm in yellow")
        gomodexample.PrintInCyan("I hope i'm in cyan")
    }
    {{< / highlight >}}

* Again we are using modules. In this executable we want to use the latest i.e. v1.1.0 version of our library

    {{< highlight bash >}}
    go mod init github.com/jimmyislive/gomoduse
    go build ./...
    go run cmd/gomoduse/main.go
    jjohn$ go run cmd/gomoduse/main.go
    {{< / highlight >}}
    Here is the output:
    {{< figure src="/images/gomodules.png">}}


    If you peek in go.mod you will see that the version of gomodexample is pinned to the latest i.e. v1.1.0

    {{< highlight bash >}}
    jjohn$ go list -m all
    github.com/jimmyislive/gomoduse
    github.com/fatih/color v1.7.0
    github.com/jimmyislive/gomodexample v1.1.0
    github.com/mattn/go-colorable v0.1.1
    github.com/mattn/go-isatty v0.0.7
    golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
    {{< / highlight >}}

* Now let's experiment and downgrade the version of gomodexample to v1.0.0. First we confirm to see what versions are out there in the wild:

    {{< highlight bash >}}
    jjohn$ go list -m -versions github.com/jimmyislive/gomodexample
    github.com/jimmyislive/gomodexample v1.0.0 v1.1.0
    {{< / highlight >}}

    Downgrade the gomodexample library:

    {{< highlight bash >}}
    jjohn$ go get github.com/jimmyislive/gomodexample@v1.0.0
    go: finding github.com/jimmyislive/gomodexample v1.1.0
    go: downloading github.com/jimmyislive/gomodexample v1.0.0
    go: downloading github.com/jimmyislive/gomodexample v1.1.0
    {{< / highlight >}}

    Verifying that we now have v1.0.0:
    {{< highlight bash >}}
    jjohn$ go list -m all
    github.com/jimmyislive/gomoduse
    github.com/fatih/color v1.7.0
    github.com/jimmyislive/gomodexample v1.0.0
    github.com/mattn/go-colorable v0.1.1
    github.com/mattn/go-isatty v0.0.7
    golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
    {{< / highlight >}}

    Now when we build and run the executable, we should get an error as the old v1.0.0 version does not contain the `PrintInCyan` function.

    {{< highlight bash >}}
    jjohn$ go build ./...
    # github.com/jimmyislive/gomoduse/cmd/gomoduse
    cmd/gomoduse/main.go:10:5: undefined: gomodexample.PrintInCyan
    {{< / highlight >}}

    Thus the `go.mod` controls which version of dependencies are used thereby giving us reliable and verifiable builds.

Hopefully this tutorial has helped you understand Go modules better. Starting in Go 1.13 (August 2019) module mode will be enabled by default. The new features seem very promising and I for one am eager to start using Go modules more often.




    







