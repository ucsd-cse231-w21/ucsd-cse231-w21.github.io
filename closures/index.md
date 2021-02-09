---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design – Closures"
doodle: "/doodle.png"
---

# From Nested Functions to Closures

Consider this Python program that _isn't_ a ChocoPy program:

```
def f(x : int):
  def g(y : int) -> int:
    return x + y
  return g

g_where_x_is_6 = f(6)
print(g_where_x_is_6(5))
```

<div class='sidenote'>In this development, we probe beyond the limits of
ChocoPy's restrictions. We'll start doing that more and more.</div>

It prints `11`. The function `g` has its definition _nested_ within the body
of `f`, and it has access to read the parameter `x` of the `f` function.
Further, `g` is _returned from f_ as a value. As a point of terminology, we
can say that the function **escapes** from `f` ([escape
analysis](https://en.wikipedia.org/wiki/Escape_analysis) can help us
determine this). This will have profound consequences for the notion of
functions in our compiler and runtime.

The key insight we need in order to understand this case is that `x` must be
somehow _stored in a way that is accessible from the returned function
value_. In general, we'd need to make sure this storage happens for any
nonlocal variables in the function. That means that somehow, we need to store
a collection of names mapped to runtime values along with a reference to a
runnable function.

Wait a minute.

- A collection of names mapped to runtime values
- A reference to a runnable function

That sounds an awful lot like **fields** and a **method** inside a class
definition. Indeed, this insight is a useful one for a sketch of how to
implement this. The key idea is (as with nested functions) to identify the
nonlocal variables, and lift the definition outside the function it is nested
in. However, in the case of functions that escape, we can't just use extra
arguments. Since `g` could be called from anywhere, we can't rely on all
other contexts (in other files/REPL entries) knowing that we've changed the
number of arguments, or having access to the correct value for `x`. So,
instead of lifting out a function definition, we will lift out the function
into a method of a _class definition_ with a field for each nonlocal
variable.

<div class='sidenote'>This <a href="https://stackoverflow.com/questions/21858482/what-is-a-java-8-lambda-expression-compiled-to">StackOverflow</a> discussion shows that this is in fact the same approach that Java takes</div>

Here's the idea of the transformation for the above example:

```
class closure_f_g(object):
  x : int = 0
  def apply(self, y : int) -> int:
    return self.x + y

def f(x : int):
  g : G = G()
  g.x = x
  return g

g_where_x_is_6 : closure_f_g = f(6)
print(g_where_x_is_6.apply(5))
```

The process for this step is:

- Identify the nonlocal variables in the escaping function
- Create a new class with a field for each of those nonlocal variables
- Put the definition of the function in that class as a method, with an extra
`self` parameter
- At the original site of the definition of the function, change the program
to instantiate an object of that class and initialize its fields to the
values of those variables. Call this object the _closure_
- At each call to the escaping function, instead perform a method call to the
function using the closure that stores the variables' values

It's important to note that for the function `g` above, `x` is nonlocal but
not nonlocally-mutable. The creation of this class structure for the closure
doesn't eliminate the purpose of shared references for nonlocally-mutable,
because different sets of nonlocally-mutable variables may be shared across
many different closures. If multiple closures all have access to read and
write a shared variable, it would need to be stored as a `Ref` that could
refer to the same shared heap location across all those closures.

## Surface Syntax and REPL Consequences

Until closures, our transformations have been fully expressible within
ChocoPy's restrictions. In addition, the results of our AST transformations
haven't needed to be considered at the REPL at all. That changes with
closures. In fact, you may have been uneasy already about this line:

```
g_where_x_is_6 = f(6)
```

Returning `g` from `f` without calling it broke one of ChocoPy's
restrictions. But this assignment statement breaks another – the variable
`g_where_x_is_6` doesn't have a corresponding variable initialization!
Indeed, what would we write for such an initialization?

```
g_where_x_is_6 : ____________ = _______________
```

None of our types capture this case. It would be problematic for the
_programmer_ to use the class type `closure_f_g`, because this type doesn't
exist in the original program. Before we come up with a solution to this
problem, we should examine the consequences of our choice to allow returning
`g` from `f` further.

Since this means that the returned function is a value (a reference to
object-like data on the heap), we can store it in a global variable, which we
do in the example. We can also _pass it to other functions_, _call it from
the REPL_, _store it in an object's fields_, and so on; anything we can do
with a value. In particular:

1. Passing it to other functions and storing it in objects' fields requires
that we can describe its type
2. Calling it from the REPL requires that we can detect which function calls
are to closures, so we can correctly compile them to a method call instead of
a function call.

### Types for Closures

Let's tackle task 1. above first. We need a way to describe the type of these
function values. Python actually has a recommendation for this, which is to
describe them using the `Callable` type constructor (we pick this because
it's [a built-in concept in
Python](https://docs.python.org/3/library/typing.html#typing.Callable)). The
type of `g` above would be

```
Callable[[int], int]
```

The first part of the `Callable` is a list of types for the arguments, and
the second part is the return type. This is similar to what you may have
chosen to use to represent the function environment in your compilers, except
now it's a type that could be used in annotations. We would expect to make it
part of our `Type` ADT, probably something like:

```
type Type = ... numbers, booleans, classes, none ...
  | { tag: "function", args : Array<Type>, ret: Type }
```

Here's how this would fill into the original program:

```
def f(x : int) -> Callable[[int], int]:
  def g(y : int) -> int:
    return x + y
  return g

g_where_x_is_6 : Callable[[int], int] = f(6)
print(g_where_x_is_6(5))
```

Now we start to see how our type-checker could help us determine how to
compile the function call to `g_where_x_is_6` on the last line – since the
function's type is a `Callable` type, the compiler can have the context to
know to use `.apply` in the generated code and treat it as a method call,
rather than expecting a top-level function to be used.

### A Realistic Example

A major consequence of this change is that we could now imagine programs that
pass functions around as arguments in many positions. For example, consider
our integer list:

```
class List(object):
  def sum(self : List) -> int:
    return 1 / 0 # Intentional error! Make sure we implement in subclasses
  def map(self : List, f : Callable[[int], int]) -> List:
    print(1 / 0) # Intentional error! Make sure we implement in subclasses
    return None
  
class Empty(List):
  def sum(self : Link) -> int:
    return self.val + self.next.sum()
  def map(self : Empty, f : Callable[[int], int]) -> List:
    return self

class Link(List):
  val : int = 0
  next : List = None

  def sum(self : Link) -> int:
    return self.val + self.next.sum()

  def map(self : Link, f : Callable[[int], int]) -> List:
    return Link().new(f(self.val), self.next.map(f))

  def new(self : Link, val : int, next : List) -> Link:
    self.val = val
    self.next = next
    return self

def square(x : int) -> int: return x * x
def twice(x : int) -> int: return x * 2

l : List = None
l = Link().new(5, Link().new(1, Empty()))
print(l.map(square).sum())
print(l.map(twice).sum())
```

Here, the `f` in `Link.map` is two _different_ functions at runtime. This is
just like dynamic dispatch! This underscores the need for similar
infrastructure to what we build for inheritance and methods – the function
value needs to store enough information to find a particular function
reference in a method table. In our vtable setup, it suffices to think of all
function values as subclasses of a built-in `Callable` class with an `apply`
method at offset 0. When we compile these functions with the class expansion
shown above, they will all have a single `apply` method, so this
representation works out.

In terms of type-checking, we will need to augment our type-checker to
understand the new `"function"` `Type`. This means that definitions like
`square` and `twice` above are more like _variables_ than global
_definitions_ when they are used in argument position (like when calling
`map`). We need to check that `Callable[[int], int]` matches the argument
type of `map`, in this case (and report a type error for mismatched function
types).

We also need to make sure the type-checker checks function calls
appropriately when the _function_ might be a variable of type `Callable`. We
need to look up and compare the parameter types from the `Type`, rather than
from a global environment of definitions by function name, because the name
in the call expression isn't necessarily the name of any definition (like `f`
in `map`).

So we have an implementation checklist to make this work:

- Parse a new `Callable` type annotation and add it to our `Type`
specification.
- Add an additional step to type-checking function calls when the function
position could be a function value (a closure).
- Add a post-type-checking transformation step to the compiler that treats
closures as classes
- Add an additional step to the compiler when generating code for function
calls to closures.

## Gradually Increasing Complexity Redux

In the section on nested functions, we talked about how inlining, adding
extra arguments, and reference wrappers were compilation choices for
increasingly complex uses of functions. In this case, making the class and
instantiating closures is yet another technique for handling **escaping**
functions. This naturally extends the types of functions we can handle, and
also means we have even more choice in the compiler. Our optimizing compiler
could now:

- Inline small, non-recursive nested functions
- Add reference wrappers to variables that are **nonlocally mutable**
- Add extra arguments to un-inlinable nested functions that **do not escape**
and change their call sites
- Create closure classes for functions that **escape** and change their
instantiation

Note that variable reference wrapping _and_ closure class transformation is
necessary if an **escaping function** refers to a **nonlocally mutable**
variable! So the fully general function implementation (for what we've seen
so far) would need to wrap all variables and create closure classe for all
functions. We can view the omission of a closure class as an optimization for
non-escaping functions, and the omission of variable references as an
optimization for variables without nonlocal mutability, and so on.

<div class='sidenote'>Other popular language implementations, like Python,
JavaScript, Scheme, and more, handle this case just fine.</div>

It's also worth noting that this is where the implementors of Java gave up!
! in Java it is a static error to have a
nested function refer to a **nonlocally mutable** variable:

```
$ jshell
jshell> int m() {
   ...>   int x = 10;
   ...>   Function<Integer, Integer> y = (Integer z) -> x + z;
   ...>   x = 100;
   ...>   return y.apply(10);
   ...> }
|  Error:
|  local variables referenced from a lambda expression must be final or effectively final
|    Function<Integer, Integer> y = (Integer z) -> x + z;
|                                                  ^
```