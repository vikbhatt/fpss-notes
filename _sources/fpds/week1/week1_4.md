# Monads

We are going to study a general class of design patterns that come up almost everywhere in functional programming and reactive programming.

A `monad` M is a class of data structures , with parametric type `M[T]` with two operations `flatMap` and `unit` that have to satisfy some laws.

```scala
trait M[T] {
def flatMap[U](f: T => M[U]): M[U]
}

def unit[T](x: T): M[T]
```

In literature `flatMap` is also called `bind`.

## Example of Monands

* List is a `monad` with `unit(x)` = `List(x)`
* Set is a `monad` with `unit(x)` = `Set(x)`
* Option is a `monad` with `unit(x)` = `Some(x)`
* Generator is a `monad` with `unit(x)` = `Single(x)`

`map` can be defined as combination of `flatMap` and `unit` for every `monad`.

```scala
m map f == m flatMap (x => unit(f(x)))
== m flatMap (f andThen unit)
```

To qualify as `monad`,  a type has to satisfy three laws.

```{admonition} Laws of monad
:class: tip
Associativity
: `m flatMap f flatMap g == m flatMap (x => f(x) flatMap g)`

Left unit
: `unit(x) flatMap f == f(x)`

Right unit
: `m flatMap unit == m`

```

`Option` is a `monad`. Let's check that.

```scala
abstract class Option[+T] {

def flatMap[U](f: T => Option[U]): Option[U] = this match {
case Some(x) => f(x)
case None => None
}

}
```

````{admonition} Proof

Associativity
```scala
opt flatMap f flatMap g
== opt match { case Some(x) => f(x) case None => None }
match { case Some(y) => g(y) case None => None }
== opt match {
case Some(x) =>
f(x) match { case Some(y) => g(y) case None => None }
case None =>
None match { case Some(y) => g(y) case None => None }
}
== opt match {
case Some(x) =>
f(x) match { case Some(y) => g(y) case None => None }
case None => None
}
== opt match {
case Some(x) => f(x) flatMap g
case None => None
}
== opt flatMap (x => f(x) flatMap g)
```

Left unit

```scala 
Some(x) flatMap f

== Some(x) match {
case Some(x) => f(x)
case None => None
}

== f(x)
```

Right unit

```scala
opt flatMap Some
== opt match {
case Some(x) => Some(x)
case None => None
}
== opt

```
````


## Try 

`Try` resembles `Option` instead of `Some/None` there is a `success` case with value and `failure` case that contains an exception.

```scala
abstract class Try[+T]
case class Success[T](x: T) extends Try[T]
case class Failure(ex: Exception) extends Try[Nothing]
```
`Try`  is used to pass computational results that can fail with an exception between threads or computers. `Nothing` has no value or missing value in Scala.

A typical way of using `Try` is to wrap arbitrary expression in `Try`.

```scala
object Try {

def apply[T](expr: => T): Try[T] =
try Success(expr)
catch {
case NonFatal(ex) => Failure(ex)
}

}
```

Expression is passed as call by name parameter to `apply` method. If the computation throws a non-fatal exception (an exception is fatal if the exception doesn't make sense to export beyond the thread of computation) it results in `Failure`.

Just like `Option` we can use `for`-expressions with `Try`.

```scala
for {
x <- computeX
y <- computeY
} yield f(x, y)
```

If either of `computeX` or `computeY`, or `f` fails this will return `Failure(ex)`, otherwise `Success(f(x,y))` is returned. But we need to define the `flatMap` and `map` implementation for `Try`.

Here is an implementation.

```scala
abstract class Try[T] {
def flatMap[U](f: T => Try[U]): Try[U] = this match {
case Success(x) => try f(x) catch { case NonFatal(ex) => Failure(ex) }
case fail: Failure => fail
}
def map[U](f: T => U): Try[U] = this match {
case Success(x) => Try(f(x))
case fail: Failure => fail
}}
```
You might think `Try` is a `monad` but left unit law fails.

```scala
Try(expr) flatMap f != f(expr)
```

The left hand side would never raise a non-fatal exception, whereas the right hand side can throw an exception by the `expr` or `f`. `Try` trades one monad law for another.

Bullet Proof principle
: An expression composed from ‘Try‘, ‘map‘, ‘flatMap‘ will never throw a non-fatal exception.

If the `monads` also define `withFilter` with additional laws are called `monads with zero`. The three monad laws gives useful guidance in the design of library API.