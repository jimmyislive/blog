---
title: "Init function in modules"
date: 2019-03-11T20:54:45-08:00
draft: false
tags: ["tech"]
---
A Go package can have an `init` function. This function is called at the time when the package is imported. You can put in any initialization you want here. e.g.

{{< highlight go "linenos=table" >}}
package main

func init() {
    fmt.Print("Hello")
}

func init() {
    fmt.Print(" World")
}

func main() {
    fmt.Println(" People")
}
{{< / highlight >}}

The above will print out `Hello World People`.

The `init` function can be repeated as many times as we want and they will be executed in the order listed. This also means that if package `A` imports package `B`, the init functions of `B` will happen before any init function of `A` get executed.

The `init` function is [niladic](https://golang.org/doc/effective_go.html#init) i.e. it takes no args and returns nothing.

Sometimes we want to import a package only to run it's init function i.e. we only want the side-effects of initialization. e.g. 

{{< highlight go "linenos=table" >}}
import (
  "image/jpeg"
  _ "image/png"
  _ "image/gif"
)
{{< / highlight >}}

In the above snippet, we imported `image/png` only for it's initialization function i.e. prior registration of a decoder function.

If you want to see a real world example (there are many), here is one from [Cobra - A Commander for modern Go CLI interactions](https://github.com/spf13/cobra/blob/master/cobra/cmd/root.go)


