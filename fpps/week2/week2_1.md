# Higher Order Functions

Functional programming languages treat functions as first class values. Functions can be passed, and returned from other functions.

Higher order functions
: Functions that take other functions as parameters and return other functions are called higher order functions.

The type `A => B` is the type of a functions that takes an argument of type A and result of type B.

## Anonymous functions

Anonymous functions are similar to string literals. They exist without a reference name.


```scala
(x:Int, y:Int) => x+y

```

```{admonition}
:class: tip
Type information can be ignored if the compiler can infer the type from the context.
```

```{admonition} Syntactic Sugar
:class: information
Syntactic sugar is syntax in a programming language that is designed to make things easier to read and express. It offers no functionality or expressiveness that the programming language couldn't.

Anonymous functions are syntactic sugar.

You can always rewrite `(x1:T1, x2:T2, ..., xn:Tn) => E` as follows

`{def f(x1:T1, x2:T2, ..., xn:Tn) => E; f}`

where `f` is an arbitrary fresh name not used in the programming. Note the return value of above block (see [block scope](../week1/week1_2.md)) is `f` which is the equivalent function defined.

```
