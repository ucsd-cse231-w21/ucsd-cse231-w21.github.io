---
layout: page
title: "UCSD CSE231 – Advanced Compiler Design, PA2.5"
doodle: "/doodle.png"
---

# PA2.5 – Design Iteration and Review

In this assignment, you'll spend time reflecting on, and learning from, the
designs you and others chose for PA2.

We will make all of the PA2 compilers available to the class. We will make an
effort to anonymize them, but if there are lots of comments, etc, with your
name on them we may not be able to, and we may not be able to remove all
traces of git history and other identifying information. You can provide us
with a git repository that you anonymize if you'd like to manage this
yourself.

You can email the instructor by **4pm on Friday, Jan 29** to opt out of this
and get a different assignment, but we highly encourage you to participate in
review.

## Code Reviewing Another Compiler

You will be assigned 2 other compilers to review. Spend no more than 1 hour
per compiler per prompt below (many will take much less than that). If you
find yourself taking more, summarize and move on. The whole assignment
shouldn't take more than about 6 hours to complete, though you may want to
spend more time than that reading and studying the compilers you are
reviewing (and we encourage it)!

Your feedback will be **shared with the class (including the author of the
compiler)**, so make sure to keep what you write professional and
constructive.

### Assigned Compilers
All submitted compilers are available [here](https://classroom.github.com/a/3vSipAyG). 
You are assigned two compilers to review, which can be found in `assigned_IDs.csv`.
First, you need to find your own submission ID, by going to your 
[PA2 codebase Gradescope submission](https://www.gradescope.com/courses/222971/assignments/941917). 
After naviagting to your submission, you can find your submission ID as part of the URL:
https://www.gradescope.com/courses/222971/assignments/941917/submissions/**THIS IS YOUR SUBMISSION ID**

Next, navigate to `assigned_IDs.csv` and find your submission ID in the first column.
The submission IDs in the seconds and third column are your assigned compilers. 
You should be able to find these submissions in the repository above. 

### Tracing the Compiler

For **each** of the compilers you are reviewing, choose two programs that run
successfully on the compiler under review (e.g. they match ChocoPy's
behavior). Make sure that between them, they at least use:

  - Global variables
  - A function definition and function call
  - If _or_ while
  - At least 2 different binary operators
  - An int and a bool

For each program you chose, show _three_ relevant code snippets from the
compiler that are critical to its compilation. For example, you might
show the data structures used in the type checker, the code generation,
and the parsing for a particular expression. Only choose the same snippet
of code for both programs if it behaves in an interestingly different way
across the two.

For each code snippet, write a sentence of how it relates to different parts
of the program you're testing.

You can use the same two programs on both compilers if you think they
illustrate the behavior well.

### Bugs, Missing Features, and Design Decisions

For **each** compiler you are reviewing, choose a program that has different
behavior than the PA2 subset of ChocoPy (either produces an error, isn't
implemented, or produces a different answer).

- If a key feature in the program isn't implemented, describe how you would
add it to the compiler (see below for how to do this).
- If it is implemented but produces an error, describe how you could fix the
error and make it produce the same answer as ChocoPy (see below for how to do
this)
- If it is implemented but produces the wrong answer, decide if you
think this was a reasonable design decision. Describe as appropriate:

  1. If you think producing this answer instead made certain parts of the
  compiler design simpler or easier than matching ChocoPy, and identify how.
  3. If you think it's just a bug, and if so, how to fix it.
  4. If you think it's a better design decision than what was chosen by
  ChocoPy.
- If you think the compiler perfectly implements the PA2 subset of ChocoPy,
explain what you tested to reach this conclusion and why you are confident
that it does.

### Adding New Features

(2 hours total)

For each of the features below, choose **one** of the compilers you are
reviewing, and describe how you would add it.

- `and`/`or`
- `global` declarations inside functions

To describe how you would add a feature:

1. Identify each file that would need to be modified
2. For each of those files, give any _new_ functions or types you'd need
to add by describing their signature and purpose (you don't have to
implement them)
3. For each of those files, give any _changes_ to existing types (like
the environment, ast, and so on) you would make
4. For each of those files, describe which functions you would need to
add to:
    - Describe any alterations to the signature (arguments and return
    type of the function).
    - Describe where any code would be added or changed, and what the
    behavior of the new code would be.
5. Give an example of the generated `.wat` code you would expect to be
generated for an example use of the feature. If you're not sure what
examples to choose, use these:
    - For `and`/`or`:
      ```
      def f(x: int) -> bool:
        print(x)
        return x > 3
      f(10) or f(5)
      ```
    - For `global`:
      ```
      x : bool = True
      def f(n : int) -> int:
        global x
        if x:
          x := False
          return n * -1
        else:
          x := True
          return n
      
      print(f(6))
      print(f(5))
      print(f(10))
      print(f(15))
      ```
6. Describe any new errors (type-checking, scope) by giving an example of
a program that would cause them, and where in the compiler the error
would be reported.

### Lessons and Advice

Answer the following questions:

1. Identify a decision made in this compiler that's different from yours.
Describe one way in which it's a **better** design decision than you made.
1. Identify a decision made in this compiler that's different from yours.
Describe one way in which it's a **worse** design decision than you made.
1. What's one improvement you'll make to your compiler based on seeing this
one?
1. What's one improvement you recommend this author makes to their compiler
based on reviewing it?

## Handin

You will [hand in](https://www.gradescope.com/courses/222971/assignments/978616) this assignment as a PDF, first with the pages containing
the review of the first compiler you were assigned followed by pages
containing the review of the second. (We wish you could submit and label 2
pdfs but Gradescope doesn't allow that).
