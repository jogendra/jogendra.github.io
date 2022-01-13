---
layout: post
title: "Writing maintainable Go code"
description: To write Go effectively, it is crucial to understand its properties and idioms and apply the established conventions related to naming, program construction, formatting, etc. The post explains some good practices that will help write maintainable Go code.
comments: true
keywords: go, golang, best practices
scover: import_cycles.png
---

Writing maintainable code is essential. Clarity, readability, and simplicity are all aspects of maintainability. It should make the process easy for someone to join your project or maintain it after someone leaves.
Maintainability is measured by how effortless it is to introduce changes and the amount of risk associated with those changes. To write Go effectively, it is crucial to understand its properties and idioms and apply the established conventions related to naming, program construction, formatting, etc.

Here are some good practices that will help write maintainable Go code.

> **Note**: This article was originally published on [Deepsource blogs](https://deepsource.io/learn/software-engineering-guide/writing-maintainable-go-code/).

### Keep `main` small
"[Tour of Go](https://go.dev/tour/basics/1)" reads:

> Every Go program is made up of packages. Programs start running in package `main`.

`main` package is unique as neither the exported names are exported, nor does the compiler treat it like a regular package; instead, it compiles it to an executable program. Inside `main` package, **main** function (`main.main`) is present, which is the entry point to a Go program. Expectation from the `main` package and the `main` function is that they do as little as possible.

`main.main` acts as a singleton and gets only called once. It is also hard to write tests for the code inside `main.main`, thus, it is highly recommended to drive the program with `main.main` but not write the business logic inside `main` package. Segregating driver and business logic in separate packages improves the program's structure and maintainability.