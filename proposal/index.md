---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design"
doodle: "/doodle.png"
---

# Project Proposals and Starting Points

Deadline Feb 25.

Within the proposals/ directory, submit a file called your-project.md that
contains the following:

- 10 representative example programs related to your feature. For each, a
description of how their behavior will be different/work/etc. when you are
done.
- A description of how you will add tests for your feature.
- A description of any new AST forms you plan to add.
- A description of any new functions, datatypes, and/or files added to the
codebase.
- A description of any changes to existing functions, datatypes, and/or files
in the codebase.
- A description of the value representation and memory layout for any new
runtime values you will add.
- A milestone plan for March 4 – pick 2 of your 10 example programs that
you commit to making work by March 4.

You will commit this proposal to your project's branch, and create a **pull
request** that includes this file, along with a tiny change to the codebase
that is important for your project (e.g. adding parsing of string literals,
adding a test case for bignums that currently fails, adding parsing for
superclass position, adding an extra no-op stage to the compiler for
optimization, adding a way to print out the heap for memory management, etc).
You can do more than this, but some working change is required for this step
so that we can talk about how you'll get from there to working, simple
implementation on March 4.

Still do all of this if you **do not** plan to work on the large project
long-term. If you don't want your work to be public, you can do this on a
private fork of our implementation of chocopy-wasm-compiler and add us to the
repository to grade it. We won't include your code in the running compiler
and you can progress independently.

## Project Week Work

(Updated March 1)

For March 4, please submit another pull request. Include:

- Code and tests demonstrating that the 2 programs you chose from your
proposal are working as expected
- A description of which examples will work by March 11, including any
updates you want to make to the examples you plan for March 11 (it's OK to
scale back or up depending on where you got!)
- A description of the biggest challenge you faced in your week of implementation

Put the latter two updates into a file called `milestone-your-project.md` in
the `proposals/` directory.