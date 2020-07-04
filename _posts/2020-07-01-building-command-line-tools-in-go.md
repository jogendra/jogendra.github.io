---
layout: post
title: Building Command Line Tools in Go
description: An example post about building command line tools in golang using cobra
comments: true
keywords: go, golang, cli, cobra
---

Command line interface (CLI) tools are an essential part of any developer. CLI tools are lightweight, less noisy, fun to use and of course they make you more **productive**. Everyday I try to be more dependent on Terminal (I use [iTerm](https://www.iterm2.com/)) and get rid of GUI applications, simply because I am more focused using a tool that provide me everything with just some commands/scripts than some fancy GUI application that come with fancy popups, ads, pictures which add noise to my focus.

In this example, we will create a simple (but scalable to any level) bookmark CLI tool in Go using Cobra that can do basic operations like adding a new bookmark, listings, clear etc. I will try to keep it as much detailed as possible.

### Why Go?

Go brings a whole set of advantages. Go compiles very quickly into a single binary, works across platforms with a consistent style, and brings a strong development community. Go applications are built into a single self contained binary making installing Go applications trivial. Easy concurrency model makes Go easy to progress with multiple tasks at the same time. Concurrency is an integral part of Go (Selling point of Go :P), supported by goroutines, channels. Go also provides backward compatibility. The list is long, let’s move on to the next part.

If you aren’t familiar with Go, you simply go through [Go Tour](https://tour.golang.org/) for basic understandings.

Go is simply great to build CLI tools. Go might be the fastest and perhaps the most fun way to automate tasks. With Go, you can develop cross-platform command-line tools that are fast and reliable. There are many good libraries out there for building CLI tools and one of such library is [Cobra](https://github.com/spf13/cobra), which we will using in this example project.

#### Before we start
Before we proceed any further, install Go on your machine, if you have not already installed so. For this, you can follow the [installation procedure](https://golang.org/doc/install) on the official Golang website. It is good to have recent version of Go on your machine. You can simply execute `go version` to check Go version installed on your machine.

#### Setting up Go environment variables

Environment variables, are variables in your system that describe your environment. They are executed and added to your machine environment before you access/use them. During the Go installation, some Go specific environment variables are set to configure behavior of Go tools on your machine. You can check Go environment variables by running `go env`. We mostly care about `GOPATH`, `GOBIN` and `GOROOT`. 

In your `.zshrc` or `.bashrc` file, set your **$GOPATH** and **$GOBIN** environment variables so that we can invoke `bookmark` command from anywhere.

```bash
export GOPATH=$HOME/go
export GOBIN=$GOPATH/bin
export PATH=${PATH}:$GOBIN
```
> `PATH=${PATH}:path/to/folder` is an interesting thing of unix, I will keep it for some other blog post to explain it in details.

Restart your terminal once to source them into your machine environment or just `source ~/.zshrc`.

### Playing with [Cobra](https://github.com/spf13/cobra)

![Cobra logo](https://cloud.githubusercontent.com/assets/173412/10886352/ad566232-814f-11e5-9cd0-aa101788c117.png)

Cobra is widely popular Go package and many projects use it for building CLI tools. Cobra is both a library for creating powerful modern CLI applications as well as a program to generate applications and command files. Many of the most widely used Go projects are built using Cobra such as [Kubernetes](http://kubernetes.io/), [Hugo](https://gohugo.io), [Docker (distribution)](https://github.com/docker/distribution), [GitHub CLI](https://github.com/cli/cli) and [many more big names](https://github.com/spf13/cobra/blob/master/projects_using_cobra.md).

Cobra is built on a structure of _commands_, _arguments_ and _flags_. Commands represent actions, Args are things and Flags are modifiers for those actions. _APPNAME_ is the name of your tool.

```bash
APPNAME COMMAND ARG --FLAG
```

For example:

```bash
git clone URL --bare
```

#### Installing Cobra

You can simply use `go get` to install the latest version of Cobra. This command will install the cobra generator executable along with the library and its dependencies.

```bash
go get -u github.com/spf13/cobra/cobra
```

The cobra binary is now in the `bin/` (usually `~/go/bin/cobra`) directory of your `$GOPATH`, which is itself in your `PATH`, so it can be used directly from anywhere. You can run `cobra help` or just `cobra` to get more familiar with it.

## Let’s get started

First step to generate a basic organizational structure that cobra follow. Cobra provides [its own program](https://github.com/spf13/cobra/blob/master/cobra/README.md) that will create your application and add any commands you want. It's the easiest way to incorporate Cobra into your application. Run this in your newly created project directory:

```bash
cobra init --pkg-name bookmark
```
Here _**bookmark**_ is our tool name. This will generate basic structure which will look like:

```bash
├── LICENSE
├── cmd
│   └── root.go
└── main.go
```

A package named **cmd** is created which contains all of your commands files. `main.go` is the entry point of your tool, it export _cmd_ package and run `Execute()` func from `cmd/root.go` which execute the root command (base command of your tool, `bookmark` in this example).
`rootCmd` represents the base command when called without any subcommands.

```go
var rootCmd = &cobra.Command{
    Use:   "bookmark",
    Short: "A brief description of your application",
    Long: `A longer description that spans multiple lines
           and likely contains examples and usage of using
           your application.`,
}
```

Here is how `main.go` look like:

```go
package main

import "bookmark/cmd"

func main() {
    cmd.Execute()
}
```
We are importing `bookmark/cmd` inside our `main.go`. Let's create `bookmark` module before building and using it.

```bash
go mod init bookmark
```

It will create `go.mod` file which look like:

```go
module bookmark

go 1.14

require (
	github.com/mitchellh/go-homedir v1.1.0
	github.com/spf13/cobra v1.0.0
	github.com/spf13/viper v1.7.0
)
```

Now, let's just build this module with:

```bash
go build
```

This build a binary for `bookmark` module and generate `go.sum` for the same.