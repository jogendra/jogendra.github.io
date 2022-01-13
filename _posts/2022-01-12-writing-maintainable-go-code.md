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

**main** package is unique as neither the exported names are exported, nor does the compiler treat it like a regular package; instead, it compiles it to an executable program. Inside `main` package, **main** function (`main.main`) is present, which is the entry point to a Go program. Expectation from the `main` package and the `main` function is that they do as little as possible.

**main.main** acts as a singleton and gets only called once. It is also hard to write tests for the code inside `main.main`, thus, it is highly recommended to drive the program with _main.main_ but not write the business logic inside `main` package. Segregating driver and business logic in separate packages improves the program's structure and maintainability.

### Use meaningful names

Naming in Go gives major emphasis to — consistent, short and accurate names as they tend to improve the readability of the code.

Russ Cox's [philosophy](https://research.swtch.com/names) on naming:

> A name's length should not exceed its information content. For a local variable, the name `i` conveys as much information as `index` or `idx` and is quicker to read. Similarly, `i` and `j` are a better pair of names for index variables than `i1` and `i2` (or, worse, `index1` and `index2`), because they are easier to tell apart when skimming the program. Global names must convey relatively more information because they appear in a larger variety of contexts. Even so, a short, precise name can say more than a long-winded one: compare `acquire` and `take_ownership`. Make every name [tell](http://www.bartleby.com/141/strunk5.html#13).

There's a high chance that years of programming experience and naming philosophies of Ken Thompson, Rob Pike, Robert Griesemer, Russ Cox, Ian Lance Taylor, etc. might have inspired the naming conventions in Go. [Here's](https://talks.golang.org/2014/names.slide) a slide by Andrew Gerrand which talks about naming in Go in more depth.

### Code grouping

Within a function (or method), some statements could be correlated. Therefore, keeping those statements within separate code blocks is recommended, separated by a newline. Grouping makes program construction better and improves readability by segregating the related parts.

```go
// Creating new HTTP request with request body
req, err := http.NewRequest("POST", "https://api.example.com/endpoint", body)
if err != nil {
    // handle err
}

// Setting headers to HTTP request
req.Header.Set("Content-Type", "application/json")
req.Header.Set("Authorization", "Bearer b7d03a6947b217efb6f3ec3bd3504582")

// Executing the request
resp, err := http.DefaultClient.Do(req)
if err != nil {
    // handle err
}

defer resp.Body.Close()
```

### Write meaningful comments

Comments are an excellent way to understand existing code, and it answers what it does and why some piece of code exists and why it was written like this. It is good to write comments while writing the code, but it is more important to update the comments whenever you update the code. Code updates may change the objective of a particular code, so it is crucial to update comments too; otherwise, it will create confusion rather than helping later. It is better not to write comments than comments that contradict the code. You can write block comments or inline comments in Go, whatever suits your code better.

Commenting your code correctly in Go will also allow you to use the _godoc_ tool. [godoc](https://blog.golang.org/godoc) will extract comments from your code and generate documentation for your Go program. Comments in Go have several good features, like `+build`, `go:generate`, `cgo`, to name a few. Refer to [The Magic of Go Comments](https://blog.jbowen.dev/2019/09/the-magic-of-go-comments/) to understand in detail.

Knowing what not to write in comments is as important as knowing what to write. It would be best to avoid over-commenting the code and leave it to other programmers to understand Go. You should avoid obvious comments, and if the code is readable enough, it doesn't need comments. For example:

```go
// Get country code from customer address.
countryCode := getCountryCode(address) {
    // If country code is "IN", assign "India" as
    // country.
    if countryCode == "IN" {
        country = "India"
    }
}
```

Comments written here are very obvious respective to code and don't add any value.

### Don't repeat

DRY (Don't Repeat Yourself) is a principle of software development aimed at reducing duplication of software patterns, replacing it with abstractions to avoid redundancy.

So, what's the problem with code repetition? Not much until there's a change. Imagine repeating the same code in 10 places and then changing at all 10 places whenever something small changes. It's easier to maintain code if it only exists in one place, ensuring consistency. If the code is duplicated, there's a good chance that you'll forget to update one of the copies, meaning the bugs you fix in one copy will still be there in the other copy.

If you have to write the same code again, move it into the shared package where most helper functions lie. With the removal of duplicate code, you will have less code that will be clearer and easier to maintain.

The absence of generics (up to Go 1.17) might make Go code look repetitive in some places. But with generics being officially supported from Go 1.18 would make writing generic code a lot simpler, less redundant, and much more maintainable.

But, sometimes repeating code is simpler and easier to understand than trying to force an abstraction to avoid redundancy. So it is more important to know where to apply DRY and where to not because code readability and ease of understanding trumps other concerns.

### Linting and style guidelines

Adhering to a coding standard makes the codebase consistent and easy to review and maintain. It makes the coding style consistent. Often, the style guides are opinionated, and the best way to make people adhere to the same guidelines is to create a standard style guideline for the Go code. Having a style guide is not essential, but it is more critical to make your team use it religiously. There are many open-source linting tools and style guidelines out there, and you can take the base from there and modify it to make it works for you.

Go has a code formatting standard generally used and accepted in the community, although it does not require special rules. Go provides the `gofmt` tool to encourage and safeguard that the Go code is formatted with established conventions.

Many editors that support integration with `gofmt` format the file upon saving the file. Alternatively, [gofumpt](https://github.com/mvdan/gofumpt), a stricter version of `gofmt` can be used as well. The tool is a modified fork of `gofmt`, which can be used as a drop-in replacement. Also, these tools do support custom source transformations and the addition of custom rules.

If you want to follow Go's community-style guidelines, you can use [golint](https://github.com/golang/lint). The tool gives helpful hints on code style and can also help review the Go's recognized conventions. This will dramatically help onboard each new developer added to the project.

### Avoid deep nesting

Excessive nesting bothers everyone. Deep nesting makes it hard to follow logic. If you're doing code reviews or revisiting your old code, oversized functions (or methods) with lots of nesting become a mess of logical workflows. Also, nested code is hard to read; the lesser the nesting, the lesser is the cognitive load on the reader.

```go
if condition1 {
    if condition2 {
        if condition3 {
            // ...
        } else {
            // ...
        }
    } else {
        if condition5 {
            // ...
        }
    }
}
```

There are several ways developers can avoid it. [Here](https://en.wikibooks.org/wiki/Computer_Programming/Coding_Style/Minimize_nesting) is a good read about it.