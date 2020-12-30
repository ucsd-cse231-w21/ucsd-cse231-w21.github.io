---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design"
doodle: "/doodle.png"
---

# Advanced Compiler Design (CSE 231)

<a href="https://jpolitz.github.io">Joe Gibbs Politz</a> (Instructor)

## Projects

The main course project is building an in-browser, interactive compiler for
(typed) Python programs.

There are four main parts:

- Basic code generation, type-checking, and runtime functionality
- Multi-file module support
- An interactive REPL
- Optimization

Rather than spending time on these _in serial_, we will work on all four
together throughout the quarter.

Week 1-3:

- **B**: Strings, numbers, variables, type-checked ops, built-in functions
- **R**: REPL with variables and strings/numbers
- **M**: imports + exports of variables
- **O**: Constant folding and propagation

Week 4-6:

- **B**: Functions, conditionals, loops
- **R**: Make sure we can call a function from another REPL entry
- **M**: Import/export functions w/types, type-checked apps
- **O**: Inlining of short functions, dead code elim

Week 7-10

- **B**: Built-in collections (set/map/list)
- **R**: Rendering collections
- **M**: Generics for collections
- **O**: Special reps for collections based on type

Code/design reviews –how and where?

