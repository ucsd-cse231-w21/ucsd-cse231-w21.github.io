---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design"
doodle: "/doodle.png"
---

# PA3: ChocoPy Classes

**Due Tuesday, February 16th, at 7pm** (About 1.5 weeks)

<!-- **Draft, not official until this notice removed** -->

In this PA, you will, _without help_, design and implement a compiler for
classes in ChocoPy. You should treat this assignment as a take-home exam, and
not discuss it with anyone. You can ask _private_ questions on Ed and we
might answer or we might simply say that we can't because it's an assessment.
The purpose of this PA is to evaluate how well you learned material from the
first half of the course; you might learn a lot from it, but there should be
no new or surprising material.

## Language Specification

You'll be implementing the following subset of ChocoPy:

<pre>
<code>program := &lt;var_def | class_def><sup>*</sup> &lt;stmt><sup>*</sup>
class_def := class &lt;name>(object):
                  &lt;var_def | method_def><sup>+</sup>
var_def := &lt;typed_var> = &lt;literal>
typed_var := &lt;name> : &lt;type>
method_def := def &lt;name>(self: &lt;type> [, &lt;typed_var>]<sup>*</sup>) [-> &lt;type>]<sup>?</sup>: &lt;method_body>
method_body := &lt;var_def><sup>*</sup> &lt;stmt><sup>+</sup>
stmt := &lt;name> = &lt;expr>
      | &lt;expr>.&lt;name> = &lt;expr>
      | if &lt;expr>: &lt;stmt><sup>+</sup> else: &lt;stmt><sup>+</sup>
      | return &lt;expr><sup>?</sup>
      | &lt;expr>
expr := &lt;literal>
      | &lt;name>
      | &lt;uniop> &lt;expr>
      | &lt;expr> &lt;binop> &lt;expr>
      | ( &lt;expr> )
      | print(&lt;expr>)
      | &lt;name>()
      | &lt;expr>.&lt;name>
      | &lt;expr>.&lt;name>([&lt;expr> [, &lt;expr>]<sup>*</sup>]<sup>?</sup>)
uniop := not | -
binop := + | - | * | // | % | == | != | &lt;= | >= | &lt; | > | is
literal := None
         | True
         | False
         | &lt;number>
type := int | bool | &lt;name>
number := 32-bit integer literals
name := Python identifiers other than `print` or keywords
</code>
</pre>

We will explicitly _exclude_ inheritance from the subset we implement. We
also limit the subset beyond the limitations in PA2:

- There are no function definitions (but there are method definitions, which
are quite similar)
- If-else statements always have a then branch and an else branch, with no
`elif`
- There are no `while` loops
- As with PA2, there are no lists, strings, for loops, nested functions, or
global/nonlocal declarations

The behavior of a program in the subset described above is specified to be
the behavior of ChocoPy on that program. Programs outside the subset of the
grammar defined can have any behavior, so if you have a compiler you want to
start from that implements more of PA2, feel free.

Note that by “behavior” we mean both the static and dynamic behavior. So if
ChocoPy fails to compile a program with a type error, your compiler should as
well. Error messages don't need to match ChocoPy exactly, but should be in
the same spirit as ChocoPy's errors.

## REPL

You will also implement REPL support for your compiler. Since a REPL isn't
specified by ChocoPy, we describe the expected behavior here.

Generally, evaluating a new REPL entry is similar to running a program with 
some notable differences. Every REPL entry should be able to:
- declare additional global variables and functions
- read and assign to global variables declared in the program or previous entries
- call methods and manipulate fields of previously-created objects
- instantiate objects of previously-declared classes

In ChocoPy, it is an error to use `print` on the value `None` or on object
values. However, it would be a disappointing REPL experience indeed if a
program that evaluated to an object caused an immediate error. Instead, your
REPL should have the following behavior for programs that evaluate to an
object or none:

- For `None`, the REPL should print nothing
- For an object, the REPL should print `<classname address>`, where
`classname` is the name of the object's class, and `address` is the location
at which the object is stored on the heap (in **byte** offset from heap
location 0). There isn't a specific specified value for the address. However,
it must be the case that different objects print as different address values.

## Interfaces

To automatically test your compiler, we will need your implementation to respect
the following requirements:

- You must have a `ast.ts` file for your grammar. The file must at least 
contain the following two types: 1) _Values_ are used to represent the return 
result of running a ChocoPy program. The value of running a program is the value
 of the last statement in a program. All statements are evaluated to **None**, 
 except for expression statements. 2) _Types_ are used to represent the
 results of running the type-checker.

```typescript
export type Value =
    { tag: "none" }
  | { tag: "bool", value: boolean }
  | { tag: "num", value: number }
  | { tag: "object", name: string, address: number}

export type Type =
  | {tag: "number"}
  | {tag: "bool"}
  | {tag: "none"}
  | {tag: "class", name: string}
```
- You must have a `repl.ts` file implementing a `BasicREPL` class. The `BasicREPL` 
class must have at least the following two methods:
```typescript
async run(source : string) : Promise<Value> 
async tc(source : string) : Promise<Type>
```

## Recommendations, Starting Points, and Resources

There is no official starter code for the project. You are free to use:

- Any code posted from class
- Any code from your PA2 submission
- Any code from the repository we shared for PA2.5 submissions (unless you
opted out). You can use the compilers you reviewed, or others
- Any code from posted notes from the class

There are some specific tools we think you might find useful:

- [Code w/repl](https://github.com/jpolitz/toy-wabt-on-client)

  We've updated this to also contain a way to _test_ the REPL by writing a
  sequence of interactions. This is helpful for making sure changes don't
  break the REPL interface and codifying experiments you try in the web
  interface.

- [Code w/classes and decorated AST](https://github.com/ucsd-cse231-w21/lecture8/)
- [Notes about implementing methods](https://ucsd-cse231-w21.github.io/objects/)

You are also free to use the internet, books, other course resources, and any
programming tools. The only constraint is that you can't have communication
with others (inside or outside the class) to help complete your
implementation.

If you were unhappy with your PA2, it's not a bad idea to either start from a
compiler you think is pretty good, or just start from scratch. You might be
surprised how much you've learned and how much progress you can make starting
from a blank slate for PA3, and how much you have to modify a PA2
implementation to get to PA3.

## Grading and Handin

A number of automated tests will be run on your compiler in order to assess
it. A _subset_ will be available through [Gradescope](https://www.gradescope.com/courses/222971/assignments/999592) while the assignment is
out. We may (and probably will) run a more extensive set of tests that we do
not share that will also be a part of your grade.

You will submit your _code_ to [Gradescope](https://www.gradescope.com/courses/222971/assignments/999592), and you should see immediate
feedback on which tests you passed and failed from the subset we've shared.
