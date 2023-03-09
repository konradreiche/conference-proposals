# go generate: One File to Rule Them All

This talk was accepted for GopherCon Russia 2019.

## Abstract

Code generation is the fifth stage of grief over Go’s lack of generics. With go generate we have a powerful tool to skip writing boilerplate code. Using the ast package I will demonstrate how one file can declare your API, generate middlewares (logging, instrumentation, etc.) and even documentation.

## What problems will be discussed in the talk?

Code generation is ultimately the fifth stage of grief over Go's lack of generics. We've come to accept that Go doesn't have generics (yet!) but we still want to avoid the tedious task of writing duplicate code. In software engineering, code generation is a noble deed in any case. It improves code quality by delegating the task of writing boilerplate code to a machine thus less prone to errors.

The go binary comes with a subcommand called [generate](https://blog.golang.org/generate) which scans packages for generate directives and for each directive, the specified generator is run; if files are named, they must be Go source files and generation happens only for directives in those files, i.e. you would put this line at the top of your file:

```go
//go:generate apigen
```

This simple mechanism allows for plenty of use cases. 

## Description

In this talk we will explore how we can utilize go generate to generate middlewares and documentation for a HTTP REST API automatically out of a single file using the ast and template packages by writing our own parser. You can appoint a single file to the DSL of your API. Request and response structs and your HTTP handler method will create the internal model of your API. Eventually, this model will be used in conjunction with templates to generate your middlewares.

### Bullet Points, Talk Structure

1. The Five Stages of Grief Over Go's Lack of Generics
2. go generate - how does it work?
3. Writing a parser using the ast (Abstract Syntax Tree) and template package of Go
4. Generating a logging middleware
5. Generating all kinds of middlewares (recording API requests, instrumentation)
6. Utilizing code comments to generate your API documentation
8. Making it part of your automatic deployment

### Conclusion

The design for Go 2 is still on the horizon and we don't know when and if they will arrive. In the meantime we should utilize the tools we have to be as productive as possible and go generate is a Swiss Army knife kind of tool which is useful to have in your set of solutions if you want to generalize parts of your code base. Introducing your own tools bears the risk of introducing additional code that needs to be maintained. We cannot talk about automation without mentioning the xkcd comic: Is It Worth the Time?

![Don't forget the time you spend finding the chart to look up what you save. And the time spent reading this reminder about the time spent. And the time trying to figure out if either of those actually make sense. Remember, every second counts toward your life total, including these right now.](https://imgs.xkcd.com/comics/is_it_worth_the_time.png)

It has to be careful considered if it's worth investing into such a toolchain but especially In the case of an API which is in constant change such a tool can be priceless.

## Target Audience

The target audience would be Go developers who have to maintain an active code base and would benefit from learning new tools and ways to automate part of their work. The audience would learn a toolchain blueprint that they could customize to their own needs.
