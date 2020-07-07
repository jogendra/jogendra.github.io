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

#### Setting up environment variables

Environment variables, are variables in your system that describe your environment. They are executed and added to your machine environment before you access/use them. During the Go installation, some Go specific environment variables are set to configure behavior of Go tools on your machine. You can check Go environment variables by running `go env`. 

In your `.zshrc` or `.bashrc` file, append your system's **$PATH** so that we can invoke `bookmark` command from anywhere.

```bash
export PATH=${PATH}:$HOME/go/bin
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

Cobra provides a lot of feature, you can [check them out here](https://github.com/spf13/cobra#overview).

#### Installing Cobra

You can simply use `go get` to install the latest version of Cobra. This command will install the cobra generator executable along with the library and its dependencies.

```bash
go get -u github.com/spf13/cobra/cobra
```

The cobra binary is now in the `bin/` (usually `~/go/bin/cobra`) directory, which is itself in your `PATH`, so it can be used directly from anywhere. You can run `cobra help` or just `cobra` to get more familiar with it.

## Let’s get started

First step to generate a basic organizational structure that cobra follows. Cobra provides [its own program](https://github.com/spf13/cobra/blob/master/cobra/README.md) that will create your application and add any commands you want. It's the easiest way to incorporate Cobra into your application. Run this in your newly created project directory:

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

Run the command below to install the module so that we can run `bookmark` command.

```bash
go install bookmark
```

Now, if you run `bookmark` inside your terminal, it will show the details about the tool, available commands, flags etc. You can notice that it already has `help` command, Cobra created that for us.

```bash
A Command Line tool to manage all of your bookmarks at one place.

Usage:
  bookmark [command]

Available Commands:
  help        Help about any command

Flags:
      --config string   config file (default is $HOME/.bookmark.yaml)
  -h, --help            help for bookmark
  -t, --toggle          Help message for toggle

Use "bookmark [command] --help" for more information about a command.
```

Here _config file_ is simple configuration file which will help you eliminate providing a bunch of repeated information in flags over and over. You can [check out more about it here](https://github.com/spf13/cobra/blob/master/cobra/README.md#configuring-the-cobra-generator).

### Creating our first command

We are done with initial setups. Now, let’s create the first command of our tool. With [`cobra add`](https://github.com/spf13/cobra/blob/master/cobra/README.md#cobra-add), you can generate new command.

```bash
Cobra add <command>
```

We will create a command called `insert` which we will use to add a new bookmark to the bookmarks list.

```bash
Cobra add insert
```

This will create a new go file `insert.go` to _cmd_ with the initial format. We will simply store bookmarks into a text file located at `~/.bookmarks/bookmarks.txt`.

```go
package cmd

import (
    "os"
    "os/exec"
    "github.com/spf13/cobra"
)

// insertCmd represents the insert command
var insertCmd = &cobra.Command{
    Use:   "insert",
    Short: "Add a new bookmark to bookmarks list",
    Long: `When you run this command with a link or text,
            it add that link/text to your bookmarks list`,
    Run: func(cmd *cobra.Command, args []string) {
        homeDir, _ := os.UserHomeDir()
		bookmarkFile := homeDir + "/.bookmarks/bookmarks.txt"
		if _, err := os.Stat(bookmarkFile);
		os.IsNotExist(err) {
			os.Mkdir(homeDir + "/.bookmarks", 0700)
			os.Create(homeDir + "/.bookmarks/bookmarks.txt")
		}
        f, _ := os.OpenFile(bookmarkFile,
                            os.O_APPEND|os.O_WRONLY,
                            os.ModeAppend) 
		f.WriteString(args[0] + "\n") 
		f.Close()
    },
}

func init() {
    rootCmd.AddCommand(insertCmd)
}
```
Every command will have a kind of similar structure to this. The `Run` inside the `insertCmd` is called when you run `bookmark insert` so we have implemented very simple functionality inside it. It simply checks if `~/.bookmarks/bookmarks.txt` exist, if not, create one otherwise add this new bookmark text/link (which we will provide in argument) to the database which is simply a text file.

Now, you can use `insert` command like this:

```bash
bookmark insert "this is my first bookmark, i love this tool"
```

Woah! You just created your first command successfully. Easy?

### Adding more commands

We will add couple of more commands to our tool. The procedure is same as what we followed in creating our first command.

```bash
cobra add list
cobra add clearall
```

We will use our newly created `list` command to display all the bookmarks in our bookmark database and `clearall` to clear all the bookmarks from databse. You can check out [`cmd/list.go`](https://github.com/jogendra/bookmark-cli/blob/master/cmd/list.go) and [`cmd/clearall.go`](https://github.com/jogendra/bookmark-cli/blob/master/cmd/clearall.go) files.

Now, if you run `bookmark help` in terminal, it will show newly added commands too. Make sure you run `go install bookmark` to see new changes.

### Implementing Subcommands

In this part, we will add a subcommand `last` to our `bookmark list` command. This subcommand will display the last most added bookmark. The cobra command for creating subcommand is like below:

```bash
cobra add <subcommand> -p <parent command>
``` 
Let’s add `last` subcommand to `list` with:

```bash
cobra add last -p listCmd
```

`listCmd` is internal representation of `list` command.

Our implementation for `last` subcommand look like this in `cmd/last.go` file:

```go
package cmd

import (
    "fmt"
    "os"
    "bufio"
    "github.com/spf13/cobra"
)

// lastCmd represents the last command
var lastCmd = &cobra.Command{
    Use:   "last",
    Short: "Show the last most bookmark added",
    Long: `Show the last most bookmark added from the bookmarks list`,
    Run: func(cmd *cobra.Command, args []string) {
        homeDir, _ := os.UserHomeDir()
        if _, err := os.Stat(homeDir + "/.bookmarks/bookmarks.txt");
        os.IsNotExist(err) {
            fmt.Println("Your bookmarks list is empty")
            return
        }
        f, _ := os.Open(homeDir + "/.bookmarks/bookmarks.txt")
        defer f.Close()
        scanner := bufio.NewScanner(f)
        var lastLine string
        for scanner.Scan() {
            lastLine = scanner.Text()
        }
        fmt.Println(">", lastLine)
    },
}

func init() {
    listCmd.AddCommand(lastCmd)
}
```

Now,  if we run `bookmark list last`, it will display the last bookmark added. Don’t forget to rebuild binary with `go install bookmark` before running command. You can check newly added subcommand on `list` by running `bookmark list --help`

<img class="fullimg" alt="bookmark-cli-subcommand" src="https://user-images.githubusercontent.com/20956124/86530983-1bb23300-bedb-11ea-9906-b9ad5ec5043e.png">

The `last` subcommand is considered a command line argument to the `list` command. Similarly, you can add subcommands to other commands and to subcommands itself.

Next, you can implement/add flags to your commands. It is very easy to working with flags in Cobra. You can follow their [Readme/docs on flags](https://github.com/spf13/cobra#working-with-flags). What we done is very basic, you can do lot with Cobra.

### Source Code Sturcture

After implementing commands and subcommands, project structure looks like below:

```bash
.
├── LICENSE
├── README.md
├── cmd
│   ├── clearall.go
│   ├── insert.go
│   ├── last.go
│   ├── list.go
│   └── root.go
├── go.mod
├── go.sum
└── main.go

1 directory, 10 files
```

Run `go install bookmark` to rebuild binary.

Now, if you run `bookmark help` in terminal, it will look like this:

<img class="fullimg" alt="bookmark-cli screenshot" src="https://user-images.githubusercontent.com/20956124/86528214-7e97d000-bec3-11ea-8d21-a2d3f7eff485.png">

#### Full source code: [jogendra/bookmark-cli](https://github.com/jogendra/bookmark-cli)

### Wrapping up

So now you have successfully made a user-friendly CLI tool which can perform basic operations. You can check out the full source code at [jogendra/bookmark-cli](https://github.com/jogendra/bookmark-cli). You have seen how easy and scalable it is to make a CLI tool in Go using Cobra. Go is simply great language choice to build CLI tools and Cobra is undoubtly first choice. Main aim is to create basic understanding. I will keep updating it with more basic operations if it requires. Thanks for reading. Looking forword to see what you are building.