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
All the content for this post was gathered by reading the [7 part blog series by Russ Cox](https://research.swtch.com/vgo) and other notes. There has been an [introductory post](https://blog.golang.org/using-go-modules) on the Go blog as well. I would encourange to read them as well. 

## Why Go Modules
Before jumping into the workings of Go modules, we should first understand why we need it in the first place. The current state of Go development has some drawbacks:

* All code needs to be in the $GOPATH/src directory. Such a structure not compatible with many developer workflows. Why can't I wonk in a different directory ?
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

## Example


