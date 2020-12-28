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