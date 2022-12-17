---
title: "How I Structure Go CLI Projects"
summary: "Go's speed and portability makes it the perfect tool for small scripts shared among teammates."
date: 2020-09-20T14:54:19-04:00
draft: false
tags:
  - Tech
  - Go
---

**At work, I have a bit of a reputation as "the Go guy".** Nevermind that I've never technically run an enterprise-grade production service written in Go, but I've apparently been vocal enough in my advocacy for the language that it stuck. I have, however, written a few different command line tools that I'm fairly proud of, and have established some patterns that make development much easier.

## Repository Structure

Whenever I start a new Go project, I usually use the same high-level repository structure:

```bash
.
├── cmd
│   └── examplecmd
│       └── main.go
├── go.mod
├── internal
│   └── library.go
├── Makefile
└── README.md
```

First, always start by initializing a git repository with `git init`. It's quick, it's nearly universal, and it'll no doubt end up saving you a massive headache down the road. Additionally, initialize a Go module:

```bash
$ go mod init github.com/mleone10/go-cli-example
```

I use GitHub, so my module's path reflects the remote repository to which I'll be pushing my code.

Next, create an empty Makefile and README with `touch Makefile README.md`. We'll discuss the Makefile later. A README is another good practice for every repository. It doesn't have to contain much for now - you can add documentation as your project evolves. For fantastic advice about the contents of a README, see Danny Guo's [Make a README](https://www.makeareadme.com/).

The last thing we'll do is set up our source directories. In Go, it's common to isolate executables in their own `cmd` directory, within which there are subdirectories for each individual executable. Those subdirectories are required due to the way Go's build tooling groups code. All of the `*.go` files within a single directory constitute a package. Additionally, Go handles the `internal` directory in a special way. Any packages located within a module's `internal` directory can only be used by that module. This way, you can separate application-specific code from code others can import into their own projects.

## Executable vs Library Code

In this directory structure, the `main.go` executable code should be kept as small as possible. In the cases I've encountered, this means parsing flags and calling out to `internal` library methods. Our example `main.go` contains the following:

```go
package main

import (
	"github.com/mleone10/go-cli-example/internal"
)

func main() {
	internal.Hello()
}
```

All I'm doing is calling out to my `internal` package, which consists of a single `library.go` file:

```go
package internal

import "fmt"

func Hello() {
	fmt.Println("Hello there!")
}
```

It's easy to imagine extending this as your use case demands. Perhaps you need to call out to an API using code located within `internal/apiclient`. Or maybe you need to query a database with a client in `internal/db`. The pattern is the same in either case. The key takeaway, though, is to keep anything _not_ strictly related to the command line-based interface out of the executable code.

## Input, Output, and Logs

I tend to be a [Unix Philosophy](https://en.wikipedia.org/wiki/Unix_philosophy) fanboy in many ways. Specifically, I always start designing software by thinking about the interfaces: databases, APIs, queues, files. And when it comes to small CLI tools, those interfaces tend to be STDIN, STDOUT, and STDERR.

The [standard streams](https://en.wikipedia.org/wiki/Standard_streams) are a key aspect of terminal-based programs, and Go supports interactions with them perfectly.

Whenever I need to read in data, I build my executable to read from STDIN. Let `cat` read from files, build your program to accept STDIN:

```go
// Source: https://stackoverflow.com/a/28311177
scanner := bufio.NewScanner(os.Stdin)
for scanner.Scan() {
    fmt.Println(scanner.Text())
}
```

Standard out (STDOUT) is designed to be consumed by another program. So you should assume that some other program is reading whatever you send to STDOUT and structure it accordingly. CSVs, JSON, and other line-delimited data formats are perfect for this. As for writing to STDOUT, fear not - the standard library's `fmt.Print*` methods already do that for you:

```go
fmt.Println("")
fmt.Printf("%s", "")
fmt.Print("")
```

Finally, standard error might be more appropriately named "standard diagnostic". I like using STDERR for human readable logs and errors - various ways I can keep track of a program's execution without muddying the output. Once again, the standard library comes to the rescue. By default, the `log` package writes to STDERR:

```go
log.Println("")
```

In practice, programs that use all three standard streams are executed using [I/O redirection](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection). The following will read the contents from `input.txt` and send it into `examplecmd`. Output data will go to `output.txt`, while logs (STDERR) will stay on the terminal:

```bash
$ cat input.txt | examplecmd > output.txt
```

## Build Scripts

Building executables in this structure is a bit tricky due to the nested nature of the `cmd` directory. First, the solution I've arrived at. Although it's a definitely a bastardization of `make`'s original purpose, I really do like using `Makefile`s for common scripting tasks. The tool is nearly universal, and removes the need to document small commands in a README or elsewhere.

In a `Makefile`, I put the following:

```makefile
clean:
        rm -rf ./bin

build: clean
        for CMD in `ls cmd`; do \
                env GOOS=linux go build -ldflags="-s -w" -o bin/$$CMD ./cmd/$$CMD/...; \
        done
```

And now, the explanation. Go's `build` and `install` subcommands are used to compile code into executables, but they work slightly differently when the active directory is not on the GOPATH. The above `Makefile` script is the best way I've found to overcome those challenges and enable compilation of multiple executables in one project.

When you run `make build`, the script first wipes away any already-built binaries in `./bin`. It then lists all subdirectories of `./cmd`. For each (which conceivably represent a single binary to be built), it executes `go build`. The invocation specifies that built binaries should be placed in `./bin`.

Your project's `Makefile` can be further extended to support other scripts for running, testing, or deploying. For example, you might script a local run of an executable by prefacing it with necessary environment variables:

```Makefile
run: clean build
        PROPS_FILE=./localProps.json PORT=8080 ./bin/examplecmd
```

This has the added benefit of linking your `run` script with your `clean` and `build` scripts. `Make` will run the dependent stages in the order they appear before running the target stage. So `make run` will remove old binaries, rebuild them, and run the resultant executable.

## Conclusion

In this post, we examined one minimalist approach to structuring small Go projects. We established a basic repository structure, separated code into executable and library components, discussed I/O, and created a simple way to build the project's executables.

I've pushed the aggregate examples from this post to a [GitHub template repository](https://github.com/mleone10/go-cli-example). Feel free to take a closer look at it and apply it to future projects of your own.

Until next time,  
\- Mario
