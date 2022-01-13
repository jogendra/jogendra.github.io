---
layout: post
title: "Writing maintainable Go code"
description: To write Go effectively, it is crucial to understand its properties and idioms and apply the established conventions related to naming, program construction, formatting, etc. The post explains some good practices that will help write maintainable Go code.
comments: true
keywords: go, golang, best practices
scover: maintainable_go.png
---

Writing maintainable code is essential. Clarity, readability, and simplicity are all aspects of maintainability. It should make the process easy for someone to join your project or maintain it after someone leaves.
Maintainability is measured by how effortless it is to introduce changes and the amount of risk associated with those changes. To write Go effectively, it is crucial to understand its properties and idioms and apply the established conventions related to naming, program construction, formatting, etc.

> **Note**: This article was originally published on [Deepsource blogs](https://deepsource.io/learn/software-engineering-guide/writing-maintainable-go-code/).

Here are some good practices that will help write maintainable Go code.

### Keep `main` small
"[Tour of Go](https://go.dev/tour/basics/1)" reads:

> Every Go program is made up of packages. Programs start running in package `main`.

**main** package is unique as neither the exported names are exported, nor does the compiler treat it like a regular package; instead, it compiles it to an executable program. Inside **main** package, **main** function (`main.main`) is present, which is the entry point to a Go program. Expectation from the `main` package and the `main` function is that they do as little as possible.

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

### Write better functions

Avoid writing longer functions; smaller functions are better. Longer functions can be hard to read, test, and maintain. Longer functions often have bigger responsibilities, so it is recommended to break them into smaller functions. More callers can use shorter functions created from breaking down the longer functions as they now serve more managed, independent tasks.

Doug McIlroy, the inventor of Unix pipes and one of the founders of the Unix tradition, said (Unix Philosophy):

> Make each program do one thing well. To do a new job, build afresh rather than complicate old programs by adding new features.

So breaking down function to do one thing well does resonate well with the Unix Philosophy.

As discussed earlier, naming is crucial to readability. Good function names are better than comments, and they can be just as helpful in aiding the understanding of code as a well-written comment or API documentation. Try to keep fewer function parameters; they are always better.

### Avoid package level state

In Go, only one instance of a package exists for any given import path. This means that there is only one instance of any variable at the package level. Package level variables are shared across the global level, which means that all accessors will share the same instance. Function X can modify the variable, and function Y can read the modified value.

The use of package-level variables can have many implications:

- It’s hard to trace where the variable got modified and where it got accessed to make any decision.
- Package-level variables cause tight coupling; change in one corner of code may require a modification in another corner of code, making it tougher to read, modify, and unit test the code.
- It may cause problems such as race conditions.

However, the use of package-level constants is excellent. So it is always recommended to avoid using the package-level states as much as possible. To reduce the coupling, move the relevant variables as fields on structs that need them. Defining dependencies and configuration in a struct makes it easy. The use of interfaces is also quite helpful.

### Return early and use conditions wisely

Conditional statements are something that we have to write a lot. It plays a massive role in whether the code is clean or messy. For example:

```go
func do(n int) bool {
    if n > 12 {
        return false
    } else {
        return true
    }
}
```

The problem with this code is that the `else` statement is not helping here; rather, it makes the code messy and less readable. Instead, write it like:

```go
func do(n int) bool {
    if n > 12 {
        return false
    }
    return true
}
```

We often see whole functions being wrapped in `if` statements like this:

```go
func do(n int) {
    if n > 12 {
        sum()
        subtract()
        multiply()
    }
}
```

Inverting conditional blocks will make it more clean and readable.

```go
func do(n int) {
    if n <= 12 {
        return
    }
    sum()
    subtract()
    multiply()
}
```

### Use switch more often

The **switch** statement is the best way to keep the sequence of if-else statements shorter. `switch` is a beneficial feature for writing clean programs. The program often requires comparison, and our program uses too many `if-else`, making the code messy and less readable. So the use of switch helps a lot.

```go
func transact(bank string) {
    if bank == "Citi" {
        fmt.Printf("Tx #1: %s\n", bank)
    } else if bank == "StandardChartered" {
        fmt.Printf("Tx #2: %s\n", bank)
    } else if bank == "HSBC" || bank == "Deutsche" || bank == "JPMorgan" {
        fmt.Printf("Tx #3: %s\n", bank)
    } else if bank == "NatWest" {
        fmt.Printf("Tx #4: %s\n", bank)
    } else {
        fmt.Printf("Tx #E: %s\n", bank)
    }
}
```

This looks messy, right? Now let’s use a `switch` instead. Here is how you can rewrite the same code in an idiomatic way:

```go
func transact(bank string) {
    switch bank {
    case "Citi":
        fmt.Printf("Tx #1: %s\n", bank)
    case "StandardChartered":
        fmt.Printf("Tx #2: %s\n", bank)
    case "HSBC", "Deutsche", "JPMorgan":
        fmt.Printf("Tx #3: %s\n", bank)
    case "NatWest":
        fmt.Printf("Tx #4: %s\n", bank)
    default:
        fmt.Printf("Tx #E: %s\n", bank)
    }
}
```

In the future, if new banks get added, it will be easier and cleaner with `switch-case`.

### Continuous code refactoring

In large codebases, it's essential to refactor the codebase structure. Code refactoring improves the readability and quality of code from time to time. It is not a one-time process; Teams should pay tech debts continuously to keep the codebase sane. I read somewhere nicely — "refactor early, refactor often", which makes a lot of sense in writing maintainable Go code. Packages become heavy with time regarding the amount of code and responsibility, so it is better to break some packages into smaller packages as they are easy to maintain. Another good reason to refactor packages is to improve naming. It is also essential that a package only contains code related to its function. For example, Go moved from `os.SEEK_SET`, `os.SEEK_CUR`, and `os.SEEK_END` to `io.SeekStart`, `io.SeekCurrent`, and `io.SeekEnd` (respectively) are more readable. The package `io` is better for code involving _file I/O_. Breaking packages into small ones also makes the dependencies lightweight.

## Conclusion

With time and other programmers working on a codebase, we understand better what maintainability means. Writing maintainable code is not complicated; it requires knowledge, experience, and careful thought from everyone contributing to the code. The set of good practices that we discussed should help you and the team maintain your Go code better.
