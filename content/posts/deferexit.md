---
title: "Defer on exit"
date: 2019-03-17T17:00:00-08:00
draft: false
tags: ["tech"]
---
Working with go, sometimes you may want to run a deferred action on Exit. Something like:

{{< highlight go "linenos=table" >}}
package main

import (
    "fmt"
    "os"
)

func main() {
    // THIS WILL NOT WORK
    defer fmt.Println("World")
    fmt.Println("Hello")

    // some code here...

    os.Exit(0)
}
{{< / highlight >}}

In the above code, `World` will never get printed. This is because `defer` is not run on `os.Exit`.

One pattern to fix this is to have main call another function that does the real work. Defer on that function will work e.g.

{{< highlight go "linenos=table" >}}
package main

import (
    "fmt"
    "os"
)

func main() {
    os.Exit(mainWorker())
}

func mainWorker() int {
    defer fmt.Println("World")
    fmt.Println("Hello")

    // some code here...

    return 0
}
{{< / highlight >}}

The above snipper correctly prints:
```
Hello
World
```

You can see real world usage of this pattern in [Packer](https://github.com/hashicorp/packer/blob/master/main.go#L35)