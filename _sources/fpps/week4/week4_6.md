# Pattern Matching

The task we are trying to solve is find a general and convenient way to access objects in a extensible class hierarchy

Attempts seen previously:

* Classification and access methods: quadratic explosion
* Type tests and casts: unsafe, low-level
* Object-oriented decomposition: does not always work, need to touch all classes to add a new method.

Observation: the sole purpose of test and accessor functions is to reverse the construction process:

* Which subclass was used?
* What were the arguments of the constructor?

This situation is so common that many functional languages, Scala included, automate it. This situation is so common that many functional languages, Scala included, automate it.

A case class definition is similar to a normal class definition, except
that it is preceded by the modifier case. For example:

```scala
trait Expr
case class Number(n: Int) extends Expr
case class Sum(e1: Expr, e2: Expr) extends Expr
```

Like before, this defines a trait `Expr`, and two concrete subclasses `Number` and `Sum`. It also implicitly defines companion objects with apply methods.

```scala
object Number {
def apply(n: Int) = new Number(n)
}
object Sum {
def apply(e1: Expr, e2: Expr) = new Sum(e1, e2)
}
```

so you can write `Number(1)` instead of `new Number(1)`. However, these classes are now empty. So how can we access the members?

Rules:

* `match` is followed by a sequence of cases, `pat => expr`.
* Each case associates an expression `expr` with a pattern `pat`.
* A `MatchError` exception is thrown if no pattern matches the value of the selector.

```scala
def eval(e: Expr): Int = e match {
case Number(n) => n
case Sum(e1, e2) => eval(e1) + eval(e2)
}
```

Patterns are constructed from:

* constructors, e.g. Number, Sum,
* variables, e.g. n, e1, e2,
* wildcard patterns _,
* constants, e.g. 1, true.
Variables always begin with a lowercase letter. Names of constants begin with a capital letter, with the exception of the reserved words `null`, `true`, `false`.

An expression of the form

```scala
e match { case p1 => e1 ... case pn => en }
```

matches the value of the selector `e` with the patterns `p1, ..., pn` in the order in which they are written.
The whole match expression is rewritten to the right-hand side of the first case where the pattern matches the selector `e`.
References to pattern variables are replaced by the corresponding parts in the selector.

* A constructor pattern `C(p1, ..., pn)` matches all the values of
type `C` (or a subtype) that have been constructed with arguments matching the patterns `p1, ..., pn`.
* A variable pattern `x` matches any value, and binds the name of the variable to this value.
* A constant pattern `c` matches values that are equal to `c` (in the sense of `==`)


Of course, it’s also possible to define the evaluation function as a method of the base trait.

Example

```scala
trait Expr {
def eval: Int = this match {
case Number(n) => n
case Sum(e1, e2) => e1.eval + e2.eval
}
}
```



---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Write a function show that uses pattern matching to return the representation of a given expressions as a string.
```scala
def show(e: Expr): String = ???
```
````

````{dropdown} Solution
```scala
def show(e: Expr): String = e match {
    case Number(x) => x.toString
    case Sum(l, r) => l.show()+ " + " + r.show()
}
```
````

---
