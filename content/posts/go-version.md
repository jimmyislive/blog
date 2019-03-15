---
title: "Populating go vars at build time"
date: 2019-03-12T20:54:45-08:00
draft: false
tags: ["golang"]
---
When we build go executables, one common command is the `version` command. It should tell you the commit, version number etc of the released executable. However, we don't want to hardcode these in code. We want to populate these variables at build time. Here is a common pattern that will let you do this. 

Let's assume you have a `main.go` that imports a package `version` and prints out the output of `version.GetBuildInfo()`.

The `version.go` is a very simple file:

{{< highlight go "linenos=table" >}}

package version

import "fmt"

var buildVersion, buildGitRevision, buildTag string

// GetBuildInfo returns the current build info
func GetBuildInfo() string {
        return fmt.Sprintf("Version: %s\nGitRevision: %s\nTag: %s", buildVersion, buildGitRevision, buildTag)
}
{{< / highlight >}}


In order to populate the `buildVersion, buildGitRevision, buildTag` variables at build time, we will be using the `-ldflags` argument to the `go build` tool when building. Assuming you are using a `Makefile` in your root to build your project, add a target like this:

{{< highlight makefile "linenos=table" >}}

.PHONY: build

VERSION=0.0.1
TAG=foo
REVISION=bar

PKG_PATH=github.com/jimmyislive/goversion/pkg
GO_LD_FLAGS="-X ${PKG_PATH}/version.buildVersion=${VERSION} -X ${PKG_PATH}/version.buildTag=${TAG} -X ${PKG_PATH}/version.buildGitRevision=${REVISION}"

build:
        @go build -ldflags ${GO_LD_FLAGS} cmd/goversion/main.go
{{< / highlight >}}


Now you can build your project via `make build`. If you run the generated `main` executable, you will see that the version, tag and revision values are taken from the Makefile i.e. at build time.

You can view a fully working example on [github](https://github.com/jimmyislive/goversion).




