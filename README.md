# Task

This fork is based on https://github.com/gleam-lang/otp/tree/v0.16.0
Original work Copyright © 2019 Louis Pilfold <louis@lpil.uk>

Modifications made by Alex Kelley, 2025:
- Updated dependencies to the latest versions
- Slight modifications to functions necessary to support glaem_opt@1.0

## Introduction
The module `gleam_otp/task` was removed in the release of `1.0.0`. I found it useful
and thus decided to port `gleam_otp/task@0.16.1` to support the dependencies (i.e., modules, functions, etc.)
introduced in `gleam_opt/task@1.0`. Otherwise, everything remains the same.  

## Background (gleam_otp/task@0.16.1 documentation)
A task is a kind of process that computes a value and then sends the result back
to its parent. Commonly multiple tasks are used to compute multiple things at
once.

If you do not care to receive a result back at the end then you should not
use this module, `actor` or `process` are likely more suitable.

```gleam
let task = task.async(fn() { do_some_work() })
let value = do_some_other_work()
value + task.await(task, 100)
```

Tasks spawned with async can be awaited on by their caller process (and
only their caller) as shown in the example above. They are implemented by
spawning a process that sends a message to the caller once the given
computation is performed.

There are some important things to consider when using tasks:

1. If you are using async tasks, you must await a reply as they are always
sent.

2. Tasks link the caller and the spawned process. This means that,
if the caller crashes, the task will crash too and vice-versa. This is
on purpose: if the process meant to receive the result no longer
exists, there is no purpose in completing the computation.

3. A task's callback function must complete by returning or panicking.
It must not `exit` with the reason "normal".

This module is inspired by Elixir's [Task module][1].

[1]: https://hexdocs.pm/elixir/master/Task.html

## Installation

Add the following dependency to your `gleam.toml` file

```toml
task = { git = "https://github.com/adkelley/task", ref = "main"}
```

```gleam
import task

pub fn main() -> Nil {
  let task = task.async(fn() { do_some_work() })
  let value = do_some_other_work()
  value + task.await(task, 100)
}
```

## Development

```sh
gleam run   # Run the project
gleam test  # Run the tests
```
