---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design"
doodle: "/doodle.png"
---

# PA2: ChocoPy Functions, Types, and Control Flow

**Draft, not official until this notice removed**

In this PA, you'll design and implement a compiler for all but the
heap-manipulating parts of ChocoPy including a REPL that supports functions
and global variables.

There is some support code and examples that can help you:

- From lecture3, basics of single-argument, single-statement functions
(you'll need to extend this, but it has simple starting points)
https://github.com/ucsd-cse231-w21/lecture3

- From a running demo we have, the necessary support for a basic REPL that
stores global variables in memory and loads them across REPL entries:
https://github.com/jpolitz/toy-wabt-on-client

You can use any of this code directly or for inspiration -- it is by no means
guaranteed to perfectly match the ChocoPy spec, but it _does_ run and provide
some valuable code structure suggestions you will find useful. You might
choose to base your implementation on how you approached PA1, or take a
different approach entirely based on what you learned.

We also provide code that has the front-end HTML and JavaScript management of
the REPL on the main page. There is one interface between this front-end code
and your code that you must respect in order to use our REPL implementation,
described below. Of course, you can make any changes you need to the REPL
implementation that you want.

## Language Specification

You'll be implementing the following sub-grammar of ChocoPy:

<fill>

Your compiler should have _the same output and error messages_ as ChocoPy for
programs in this subset. If you need to test out a program to check its
behavior, you can do so at ChocoPy's web site.

## REPL

### REPL Behavior

### REPL Front-end Interface

## Discussion Checkpoint

On Thursday, January 21, we will devote most of the lecture time to
discussion in groups about the current state of our implementations.

You should prepare the following:

- Three programs/scenarios involving REPL interaction where your
compiler does what you expect and you think are interesting. Be ready to talk
about how your compiler handles each of these cases
  - One should be interesting because of type-checking
  - One should be interesting because of how it works with the REPL
  - One should be interesting because of a reason of your choosing
- One program/scenario that does _not_ do what you want yet, whether because of
incorrect behavior or simply not being implemented

The structure of the discussion will be first illustrating how your compiler
works on interesting examples, followed by discussion and support on how you
might approach the problematic/incomplete cases.

## Deliverables
