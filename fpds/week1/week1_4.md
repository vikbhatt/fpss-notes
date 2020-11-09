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

To qualify as a `monad` a type has to satisfy three laws.

Associativity
: `m flatMap f flatMap g == m flatMap (x => f(x) flatMap g)`

Left unit
: `unit(x) flatMap f == f(x)`

Right unit
: `m flatMap unit == m`