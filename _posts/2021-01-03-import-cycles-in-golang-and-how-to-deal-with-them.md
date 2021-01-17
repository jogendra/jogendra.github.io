---
layout: post
title: "Import Cycles in Golang: How To Deal With Them"
description: Post explaining how the Import Cycles in Golang occurs and how you can deal with them
comments: true
keywords: go, golang, import cycle
scover: import_cycles.png
---

As a Golang developer, you probably have encountered import cycles. Golang do not allow import cycles. Go throws a compile-time error if it detects the import cycle in code. In this post, let's understand how the import cycle occurs and how you can deal with them.

### Import Cycles

Let’s say we have two packages, `p1` and `p2`. When package `p1` depends on package `p2` and package `p2` depends on package `p1`, it creates a cycle of dependency.  Or it can be more complicated than this eg. package `p2` does not directly depend on package `p1` but `p2` depends on package `p3` which depends on `p1`, again it is cycle.

<img class="fullimg" alt="import cycle golang" src="https://user-images.githubusercontent.com/20956124/103145320-6c099f80-475e-11eb-8d24-9112b5e23dee.png">

Let’s understand it through some example code.

_**Package p1**:_
```go
package p1

import (
	"fmt"
	"import-cycle-example/p2"
)

type PP1 struct{}

func New() *PP1 {
	return &PP1{}
}

func (p *PP1) HelloFromP1() {
	fmt.Println("Hello from package p1")
}

func (p *PP1) HelloFromP2Side() {
	pp2 := p2.New()
	pp2.HelloFromP2()
}
```
_**Package p2**:_
```go
package p2

import (
	"fmt"
	"import-cycle-example/p1"
)

type PP2 struct{}

func New() *PP2 {
	return &PP2{}
}

func (p *PP2) HelloFromP2() {
	fmt.Println("Hello from package p2")
}

func (p *PP2) HelloFromP1Side() {
	pp1 := p1.New()
	pp1.HelloFromP1()
}
```

On building, compiler returns the error:

```bash
imports import-cycle-example/p1
imports import-cycle-example/p2
imports import-cycle-example/p1: import cycle not allowed
```

### Import Cycles Are Bad Design

Go is highly focused on faster compile time rather than speed of execution (even willing to sacrifice some run-time performance for faster build). The Go compiler doesn’t spend a lot of time trying to generate the most efficient possible machine code, it cares more about compiling lots of code quickly.

Allowing cyclic/circular dependencies would **significantly increase compile times** since the entire circle of dependencies would need to get recompiled every time one of the dependencies changed. It also increases the link-time cost and makes it hard to test/reuse things independently (more difficult to unit test because they cannot be tested in isolation from one another). Cyclic dependencies can result in infinite recursions sometimes.

Cyclic dependencies may also cause memory leaks since each object holds on to the other, their reference counts will never reach zero and hence will never become candidates for collection and cleanup.

[Robe Pike, replying to proposal for allowing import cycles in Golang](https://github.com/golang/go/issues/30247#issuecomment-463940936), said that, this is one area where up-front simplicity is worthwhile. Import cycles can be convenient but their cost can be catastrophic. They should continue to be disallowed.

### :hammer_and_wrench: Debugging Import Cycles

The worst thing about the import cycle error is, Golang doesn’t tell you source file or part of the code which is causing the error. So it becomes tough to figure out when the codebase is large. You would be wondering around different files/packages to check where actually the issue is. Why golang do not show the cause that causing the error? Because there is not only a single culprit source file in the cycle.

But it does show the packages which are causing the issue. So you can look into those packages and fix the problem.

To visualize the dependencies in your project, you can use [godepgraph](https://github.com/kisielk/godepgraph), a Go dependency graph visualization tool. It can be installed by running:

```
go get github.com/kisielk/godepgraph
```

It display graph in [Graphviz](http://graphviz.org) dot format. If you have the graphviz tools installed (you can download from [here](https://graphviz.org/download)), you can render it by piping the output to dot:

```
godepgraph -s import-cycle-example | dot -Tpng -o godepgraph.png
```

You can see import cycle in output png file:

<img class="fullimg" alt="import cycle golang" style="width:25% !important;margin:unset" src="https://user-images.githubusercontent.com/20956124/103155444-0f41cf80-47c6-11eb-9ae8-791310e10b0a.png">

Apart from it, you can use `go list` to get some insights (run `go help list` for additional information).

```
go list -f '{\{join .DepsErrors "\n"\}}' <import-path>
```

You can provide import path or can be left empty for current directory.

### Dealing with Import Cycles

When you encounter the import cycle error, take a step back, and think about the project organization. The obvious and most common way to deal with the import cycle is implementation through interfaces. But sometimes you don’t need it. Sometimes you may have accidentally split your package into several. Check if packages that are creating import cycle are tightly coupled and they need each other to work, they probably should be merged into one package. In Golang, a package is a compilation unit. If two files must always be compiled together, they must be in the same package.

#### The Interface Way:

- Package `p1` to use functions/variables from package `p2` by importing package `p2`.
- Package `p2` to call functions/variables from package `p1` without having to import package `p1`. All it needs to be passed package `p1` instances which implement an interface defined in `p2`, those instances will be views as package `p2` object.

That’s how package `p2` ignores the existence of package `p1` and import cycle is broken.

After applying steps above, package `p2` code:

```go
package p2

import (
	"fmt"
)

type pp1 interface {
	HelloFromP1()
}

type PP2 struct {
	PP1 pp1
}

func New(pp1 pp1) *PP2 {
	return &PP2{
		PP1: pp1,
	}
}

func (p *PP2) HelloFromP2() {
	fmt.Println("Hello from package p2")
}

func (p *PP2) HelloFromP1Side() {
	p.PP1.HelloFromP1()
}
```

And package `p1` code look like:

```go
package p1

import (
	"fmt"
	"import-cycle-example/p2"
)

type PP1 struct{}

func New() *PP1 {
	return &PP1{}
}

func (p *PP1) HelloFromP1() {
	fmt.Println("Hello from package p1")
}

func (p *PP1) HelloFromP2Side() {
	pp2 := p2.New(p)
	pp2.HelloFromP2()
}
```

You can use this code in `main` package to test.

```go
package main

import (
	"import-cycle-example/p1"
)

func main() {
	pp1 := p1.PP1{}
	pp1.HelloFromP2Side() // Prints: "Hello from package p2"
}
```

You can find full source code on GitHub at [**jogendra/import-cycle-example-go**](https://github.com/jogendra/import-cycle-example-go)

### Bottom Lines

The import cycle is definitely a pain when the codebase is large. Try to build the application in layers. The higher-level layer should import lower layers but lower layers should not import higher layer (it create cycle). Keeping this in mind and sometimes merging tightly coupled packages into one is a good solution than solving through interfaces. But for more generic cases, interface implementation is a good way to break the import cycles.

I hope you got a fair understanding of Import Cycles. Do share and reach out to me on [Twitter](https://twitter.com/jogendrafx) in case of anything. Thanks for read :)
