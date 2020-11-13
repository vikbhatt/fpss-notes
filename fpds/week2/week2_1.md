# Streams

We introduce streams as new data structures. Stream is like a list, but it's tail is evaluated only on demand.

For instance, find the second prime between 1000 and 10000. How do we proceed? We can write

```scala
( 1000 to 10000 filter isPrime)(1)
```

It constructs, all the prime numbers between 1000 and 10000 and only even looks at the second element. Reducing upper bound might speed things up, you might miss the number you are looking for all together.

We can make short code efficient, by using trick

> Avoid computing the tail of list until it is needed for the evaluation result (which might be never)

The idea is implemented in `Stream`. Streams are similar to lists but the tail is only evaluated when required.

Streams are defined from constant stream `Stream.empty` and a constructor `Stream.cons`.

For example

```scala
val x = Stream.cons(1, Stream.cons(2, Stream.empty))
```

You can use `Stream` factory or use `toStream` on collection to turn it into `Stream`

```scala
Stream(1,2,3)
```

`Stream` supports almost all methods on list. One major exception is `::` operator on List.  `x::xs` always produce a list never a stream. An alternative operator `#::` produces stream.
`x #:: xs == Stream.cons(x, xs)` .

Here is the trait of `Stream`

```scala
trait Stream[+A] extends Seq[A] {
def isEmpty: Boolean
def head: A
def tail: Stream[A]
}
```

A concrete implementation of `Stream` is defined in the companion object.

```scala
object Stream {

def cons[T](hd: T, tl: => Stream[T]) = new Stream[T] {
def isEmpty = false
def head = hd
def tail = tl
}

val empty = new Stream[Nothing] {
def isEmpty = true
def head = throw new NoSuchElementException("empty.head")
def tail = throw new NoSuchElementException("empty.tail")
}

}
```

Notice the `tl` parameter in `cons` which is call by name parameter. For list the tail is normal call by value parameter. When I construct a `Stream` tail is not evaluated. Other methods are implemented in a similar way to the list.

## Lazy Evaluation

Laziness means do them as late as possible, never do them twice.
The implementation of `Stream` solves the problem of avoiding unnecessary computation when tail value is not required. But it suffers from a potential serious problem. If tail is called several times, the Stream will be recomputed and can cause exponential blow up of program complexity.

This can be avoided, by storing the first evaluation of tail and reusing the result instead of recomputing the tail. We could just squirrel away the first time we have produced the result, and reuse that result every time. That scheme is called lazy evaluation.

Scala uses strict evaluation by default. Pure functional programming languages use `lazy` evaluation because it doesn't matter when computation happened in such paradigm. But Scala doesn't use it because lazy evaluation is quite unpredictable when it happens and how much space it takes.

Since Scala allows mutable side effects, `lazy` is not allowed vby default.

Lets compare `lazy val x = expr` and `def x = expr`. Both right hand side `expr` not evaluated,  unless called. But once `lazy val` is called, the result is stored and used in subsequent calls, rather in `def x=expr` the `expr` is evaluated every time it is required.

-----------------------------------------------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Ex1`

What is the output of the following program?
```scala
def expr = {
val x = { print(”x”); 1 }
lazy val y = { print(”y”); 2 }
def z = { print(”z”); 3 }
z + y + x + z + y + x
}
expr
```
````

````{dropdown} Ex1 solution

- xzyz

The compiler sees `val` definition of `x` and evaluated it immediately and prints `x`. But `lazy val` and `def` are evaluated when required. Finally the expression `z + y + x + z + y + x` is evaluated from left to right. `z` is evaluated on demand so "z" is printed. `y` is evaluated on demand, but the result is also stored. While evaluating the value of `y`, "y" is printed, and the result is stored in `y`. `x` is already evaluted so the value is substituted as it is. `z` is again evaluated by the `def` form, so "z" is printed. `y` and `x` are already evaluated and saved, so the values are substituted as it is.

````
-----------------------------------------------------------------------------------------------------------