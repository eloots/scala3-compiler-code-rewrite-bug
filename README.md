# Rewriting to non-indentation bsaed syntax

## Introduction

This repository illustrates that a bug was introduced in Scala 3.0.2
that affects code syntax rewriting.

Different branches, one each for a specific version of the compiler,
show the problem. Only Scala 3.0.0 and 3.0.1 don't have the problem.

## So, what _is_ the problem?

Scala 3 introduces a couple of [optional] changes to the language syntax.
It introduces a new control structure syntax and a significant indentation
based syntax.

The compiler has new compiler options that allow one to have the compiler
rewrite source code between the different syntaxes.

There is a restriction of what syntaxes can be combined. Significant
indentation based syntax only works with the new control structures syntax.

As a consequence, if one start from the 'classic' syntax, a rewrite
to using the new control structures based syntax as to be performed
before rewriting to the significant indentation based syntax. After doing
so, one can revert all the syntax changes by having the compiler rewrite
the code in the opposite direction.

So, a full cycle can be performed in 4 rewrite steps as shown here:

<pre>

        Classic ('old control structures syntax, no significant indentation)  <--+
                           |                                                     |
                           v  (-rewrite, -new-syntax)                            |
                           |                                                     |
           (new control structures syntax)                                       |
                           |                                                     |
                           v  (-rewrite, -indent)                                |
                           |                                                     |
           (new control structures syntax, significant indentation)              |
                           |                                                     |
                           v  (-rewrite, -no-indent)                             |
                           |                                                     |
           (new control structures syntax, no significant indentation)           |
                           |                                                     |
                           v  (-rewrite, -old-syntax)                            |
                           |                                                     |
                           +--------------------------->-------------------------+

</pre>

On version 3.0.2 and higher of the Scala compiler, the compiler generates incorrect
code: it omits closing curly braces '}' in some cases. I haven't been able to see
a pattern in the places where this occurs. Overall, 12 closing braces are missing.

## The different branches

Different branches have been created, each one is linked and named to a specific Scala version:

<pre>
   `3.0.0`:   Scala 3.0.0
   `3.0.1`:   Scala 3.0.1
   `3.0.2`:   Scala 3.0.2
   `3.1.0`:   Scala 3.1.0
   `3.1.3`:   Scala 3.1.3
   `main`:    Scala 3.3.0-RC3
</pre>


