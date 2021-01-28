---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design"
doodle: "/doodle.png"
---

# PA2: ChocoPy Functions, Types, and Control Flow

<!-- **Draft, not official until this notice removed** -->

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

<html>
<meta charset="utf-8"/>
<pre>
<code>program := &lt;var_def | func_def><sup>*</sup> &lt;stmt><sup>*</sup>
var_def := &lt;typed_var> = &lt;literal>
typed_var := &lt;name> : &lt;type>
func_def := def &lt;name>([&lt;typed_var> [, &lt;typed_var>]<sup>*</sup>]<sup>?</sup>) [-> &lt;type>]<sup>?</sup> : &lt;func_body>
func_body := &lt;var_def><sup>*</sup> &lt;stmt><sup>+</sup>
stmt := &lt;name> = &lt;expr>
      | if &lt;expr>: &lt;stmt><sup>+</sup> [elif &lt;expr>: &lt;stmt><sup>+</sup>]<sup>?</sup> [else: &lt;stmt><sup>+</sup>]<sup>?</sup>
      | while &lt;expr>: &lt;stmt><sup>+</sup>
      | pass
      | return &lt;expr><sup>?</sup>
      | &lt;expr>
expr := &lt;literal>
      | &lt;name>
      | &lt;uniop> &lt;expr>
      | &lt;expr> &lt;binop> &lt;expr>
      | ( &lt;expr> )
      | &lt;name>([&lt;expr> [, &lt;expr>]<sup>*</sup>]<sup>?</sup>)
uniop := not | -
binop := + | - | * | // | % | == | != | &lt;= | >= | &lt; | > | is                 
literal := None
         | True
         | False
         | &lt;number>
type := int | bool
number := 32-bit integer literals</code>
</pre>
</html>

<!-- ```
program := <var_def | func_def>* <stmt>*
var_def := <typed_var> = <literal>
typed_var := <name> : <type>
func_def := def <name>(<typed_var>*) [-> <type>]? : <func_body>
func_body := <var_def>* <stmt>+
stmt := <name> = <expr>
      | if <expr>: <stmt>+ [elif <expr>: <stmt>+]? [else: <stmt>+]?
      | while <expr>: <stmt>+
      | pass
      | return <expr>?
      | <expr>
expr := <literal>
      | <name>
      | <uniop> <expr>
      | <expr> <binop> <expr>
uniop := not | -
binop := + | - | * | // | % | == | != | <= | >= | < | > | is                 
literal := None
         | True
         | False
         | <number>
type := int | bool
number := 32-bit integer literals
``` -->

The grammar above is a strict subset of ChocoPy's. Namely, the grammar above 
excludes:
- lists
- strings
- classes
- nested functions
- for loops
- global and nonlocal declarations inside a function

Your compiler should have _the same output and error messages_ as ChocoPy for
programs in this subset. If you need to test out a program to check its
behavior, you can do so at ChocoPy's web site.

## REPL
In addition to your existing program editor, you will be implementing a 
Read–eval–print loop (REPL) similar to CPython's. Feel free to use any of the 
code in our demo REPL (https://github.com/jpolitz/toy-wabt-on-client) though 
you are certainly free to explore other approaches. We do expect your 
implementation to accept any number of REPL entries and behave as described 
below.
### REPL Behavior
Generally, evaluating a new REPL entry is similar to running a program with 
some notable differences. Every REPL entry should be able to:
- declare additional global variables and functions
- read and assign to global variables declared in the program or previous entries
- call previously declared functions

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

You will turn in two deliverables, a repository containing your
implementation, and an informative README file (preferably a pdf file).

There is no autograder for this assignment. You are responsible for testing
your implementation and ensuring that it matches the ChocoPy reference
implementation's behavior on the relevant sub-language for this PA.

Your README should include the following components:

1. A description of the representation of values (integers, booleans, and
None) in your implementation. Give examples, and explain why it is necessary
to do so.
2. Give an example of a program that uses
    - At least one global variable
    - At least one function with a parameter
    - At least one variable defined inside a function

    By linking to specific definitions and code in your implementation,
    describe where and how those three variables are stored and represented
    throughout compilation.
3. Write a Python program that goes into an infinite loop. What happens when
you run it on the web page using your compiler?
4. For each of the following scenarios, show a screenshot of your compiler
running the scenario. If your compiler cannot handle the described scenario,
write a few sentences about why.
    - A function defined in the main program and later called from the
    interactive prompt
    - A function defined at the interactive prompt, whose body contains a call
    to a function from the main program, called at a later interactive prompt
    - A program that has a type error because of a mismatch of booleans and
    integers on one of the arithmetic operations
    - A program that has a type error in a conditional position
    - A program that calls a function from within a loop
    - Printing an integer and a boolean
    - A recursive function.
    - Two mutually-recursive functions.

