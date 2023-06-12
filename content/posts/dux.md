---
title: "Introducing Dux, the Tiny Live Reload Tool"
summary: "I built a small tool for a personal use case, and it felt so good!"
date: 2023-06-11T10:34:06-04:00
draft: false
toc: false
tags:
  - Go
  - Software
---
Last week, I was playing around with Go's `html/template` library and HTMX when I encountered a problem.  Every time I made a change to an HTML template or the server code, I had to `Ctrl-C` to stop the server, then `go run main.go` to rerun it.  That felt like something I could automate!

A quick search revealed that a tool called [Air](https://github.com/cosmtrek/air) was widely recommended, but the problem still seemed easy enough to tackle myself.

After a few evenings of hacking at it, I think it's ready for my use case.  I give you, **[dux](https://github.com/mleone10/dux)**.

Dux is tiny.  The bulk of the logic is 159 lines of Go, and that includes a couple nonessential bits.  It also only uses Go standard library.

## How it works
Dux has two parts: **Engines** and **Watchers**.  A Watcher implements a `Watch(context.Context)` method that just blocks until a condition is met.  I've implemented two as an example: `FileWatcher` which monitors an `fs.FS` for changes, and `TimeWatcher` which waits for a certain `time.Duration` to elapse.

Engines, meanwhile, use Watchers to know when to do something.  That "something" can be either a command and arguments (as in the case of `ExecEngine`) or a compatible function (for the `FuncEngine`).  When the Engine starts, it executes its "thing" and waits for the Watcher to unblock.  When it does, the Engine stops the "thing", restarts it, and reruns the Watcher.  It executes this loop forever, or until its context is canceled.

I also built a small command line application (`dux`) that combines an `ExecEngine` with a `FileWatcher`.  This solves my initial use case, allowing me to reload my _other_ side project whenever I make a change:

```bash
dux -c "go run main.go"
```

## Challenges
I faced two main challenges in building this.  The first was how to monitor files for changes.  For this I got to learn a bit about Go's file system package and types.  I use its `fs.WalkDir(...)` method to spin up a goroutine for every file in the file system, and a cancelable `context.Context` to signal that a file has changed and that all of the goroutines should quit.  This was a fun foray into goroutines, contexts, and Go's `select` statement.

The second challenge was how to stop processes, specifically how to stop processes _and their children_.  By default, stopping an `exec.Cmd` by canceling its context only kills that command's process, not any of its children.  To get around this, I had to relearn some concepts of process management from college.

As I understand it, when a processes started via a Go `exec.Cmd` spawn a child process, those children to not share a "process group ID" (or "pgid").  As a result, stopping the parent process does not stop the children.  We have to explicitly configure the `Cmd` to ensure that pgid is passed on:

```go
cmd := exec.Command(e.Cmd, e.Args...)
cmd.SysProcAttr = &syscall.SysProcAttr{Setpgid: true}

go func() {
  <-ctx.Done()
  syscall.Kill(-cmd.Process.Pid, syscall.SIGKILL)
}()

go cmd.Run()
```

In this snippet, I create an `exec.Cmd` and set its `Setpgid` parameter to `true`.  I then start a goroutine which waits for a context to be canceled.  `<-ctx.Done()` blocks until then, at which point it issues a `SIGKILL` signal to the command's _negative_ PID (process ID).  This is process-speak for "stop this entire process group".

I was originally using `exec.CommandContext(...)` to create a process that could be stopped by canceling the registered context, but this combined with the above child-stopping goroutine had an unfortunate side effect.  It seemed like the _parent_ process was being stopped first, then the children.  As best I can tell, the children would be terminated, but their processes would linger as there was no parent to read their exit code.  The result was numerous zombie processes being left behind until the main `dux` application was stopped.  Since the above goroutine worked to stop the parent process as well as its children, there was no need to tie the context to it as well.

## Closing
Overall, I'm pretty proud of this little library and executable.  It feels clean, lightweight, and useful.  Most importantly, it feels sufficiently complete to use.

There are still parts that can be improved: I can add tests, modify the file-watching code to detect new or deleted files, and extend the command line capabilities.  But those can come later.

I really enjoyed this particular side project.  I think it was perfectly sized for a weekend.  If this is the kind of pace and scale I can aspire to in the future, I'll be a happy software engineer!

{{< signature >}}
