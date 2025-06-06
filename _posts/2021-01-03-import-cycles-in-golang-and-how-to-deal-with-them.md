---
layout: post
title: "Import Cycles in Golang: How To Deal With Them"
description: Post explaining how the Import Cycles in Golang occurs and how you can deal with them
comments: true
keywords: go, golang, import cycle
scover: import_cycles.png
---

As a Golang developer, you probably have encountered import cycles. Golang do not allow import cycles. Go throws a compile-time error if it detects the import cycle in code. In this post, let's understand how the import cycle occurs and how you can deal with them.

### Go Compilation Model

To truly appreciate why Go disallows import cycles, it's crucial to understand its compilation model.

When you compile a Go program, the `go build` command (or `go install`, `go run`) performs several steps:

- The Go toolchain first determines the complete dependency graph of your program. It traces all import statements.
- It then compiles packages in a specific order: from the leaves of the dependency tree (packages with no outgoing imports) upwards to the root (`main` package or the package being compiled).
- Finally, all compiled package archives are linked together into a single executable binary.

This ordered compilation works because Go is designed with a strict acyclic dependency graph (DAG) in mind. Each package is compiled exactly once, and its compiled output (an archive file, typically `.a`) is then used by packages that import it.

### Import Cycles

At its core, an import cycle occurs when two or more packages directly or indirectly depend on each other.

Let's illustrate with the simplest scenario involving two packages, p1 and p2:

- **Direct Cycle:** Package `p1` imports `p2`, and `p2` imports `p1`. This forms a direct cycle of dependency.
The problem can also be more complex, involving multiple packages in a chain:

- **Indirect Cycle:** Package `p1` imports `p2`, `p2` imports `p3`, and `p3` then imports `p1`. Even though `p1` and `p2` don't directly import each other, a cycle is still formed through `p3`.

Visually, an import cycle creates a closed loop in your project's dependency graph:

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

This error clearly indicates the presence of a circular dependency between `p1` and `p2`.

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

Modern Go IDEs like VS Code with the Go extension or GoLand often provide features to visualize package dependencies and can sometimes highlight import cycles directly within the editor. This can be a very convenient way to spot issues early.

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

Benefits of the this approach:

- Decoupling: Packages become more independent, adhering to the Dependency Inversion Principle.
- Testability: You can easily mock the pp1Interface when testing `p2`, allowing `p2` to be tested in isolation.
- Flexibility: It's easier to swap out different implementations of `pp1Interface` in the future.


**Other way** of using the interface to break cycle can be extracting code into separate 3rd package that act as bridge between two packages. But many times it increases code repetition. You can go for this approach keeping your code structure in mind.

How it works:

- New Package `m1`: This package contains only the interfaces, shared types, or constants that both `p1` and `p2` need. `m1` should not import `p1` or `p2`.
- `p1` and `p2` import `m1`: Both original packages now import m1 to access the common definitions.
- Dependency Direction: The dependency flow becomes `p1 -> m1` and `p2 -> m1`. The cycle is broken because `m1` doesn't depend on `p1` or `p2`.

> **"Three Way"** Import Chain: Package p1 -> Package m1 & Package p2 -> Package m1

This approach often leads to cleaner code when the shared abstraction is truly general-purpose. However, be mindful of "anemic" packages (packages with only interfaces and no concrete logic), as they can sometimes increase complexity.

#### The Ugly Way:

Interestingly, you can avoid importing package by making use of `go:linkname`.
`go:linkname` is compiler directive (used as `//go:linkname localname [importpath.name]`). This special directive does not apply to the Go code that follows it. Instead, the _//go:linkname_ directive instructs the compiler to use _“importpath.name”_ as the object file symbol name for the variable or function declared as _“localname”_ in the source code. (definition from [golang.org](https://golang.org/cmd/compile/#hdr-Compiler_Directives), hard to understand at first sight, look at the source code link below, I tried solving import cycle using it.)

There are many Go standard package rely on runtime private calls using `go:linkname`. You can also solve import cycle in your code sometime with it but you should avoid using it as it is still a hack and not recommended by the Golang team.
This approach is also loss of type safety, means the compiler won't check if the types you're linking are compatible, leading to potential runtime errors.

**Point to note** here is Golang standard package **do not** use `go:linkname` to avoid import cycle, rather they use it to avoid exporting APIs that shouldn't be public.

Here is the source code of solution which I implemented using `go:linkname` :

**->** [jogendra/import-cycle-example-go -> golinkname](https://github.com/jogendra/import-cycle-example-go/tree/golinkname)

### Bottom Lines

The import cycle is definitely a pain when the codebase is large. Try to build the application in layers. The higher-level layer should import lower layers but lower layers should not import higher layer (it create cycle). Keeping this in mind and sometimes merging tightly coupled packages into one is a good solution than solving through interfaces. But for more generic cases, interface implementation is a good way to break the import cycles.

You can checkout interesting [discussion about this blog post on Reddit here](https://www.reddit.com/r/golang/comments/kphblv/import_cycles_in_golang_and_how_to_deal_with_them).

I hope you got a fair understanding of Import Cycles. Do share and reach out to me on [Twitter](https://twitter.com/jogendrafx) in case of anything. You can follow my open source work at [github/jogendra](https://github.com/jogendra). Thanks for the read :)
