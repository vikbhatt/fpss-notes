# Implicit Parameters

Problem: How to parameterize `msort` so that it can also be used for
lists with elements other than `Int`?

```scala
def msort[T](xs: List[T]): List[T] = ...
```

does not work, because the comparison `<` in merge is not defined for arbitrary types `T`.

The most flexible design is to make the function sort polymorphic and to pass the comparison operation as an additional parameter:

```scala
def msort[T](xs: List[T])(lt: (T, T) => Boolean) = {
...
merge(msort(fst)(lt), msort(snd)(lt))
}
```

There is already a class in the standard library that represents orderings.
`scala.math.Ordering[T]` provides ways to compare elements of type T. So instead of parameterized with the `lt` operation directly, we could parameterize with `Ordering` instead:

```scala
def msort[T](xs: List[T])(ord: Ordering) =
def merge(xs: List[T], ys: List[T]) =
... if (ord.lt(x, y)) ...
... merge(msort(fst)(ord), msort(snd)(ord)) ...
```

## Higher Order List Functions

We have several recurring patterns like 

* transforming each element in a list in a certain way
* retrieving a list of all elements which satisfy certain criterion
* combining the elements of a list using an operator

Functional languages allow programmers to write generic functions that implement patterns such as these with higher-order functions.

### Map

A simple way to define map on lists

```scala


abstract class List[T]{
    def map[U](f: T => U): List[U] = this match {
        case Nil => Nil
        case x::xs => f(x)::xs.map(f)
    }
}

```

The actual definition of `map` is bit complicated because it is tail recursive and because it works for arbitrary collections.


### Filter

```scala
abstract class List[T]{
    def filter(p: T => Boolean): List[T] = this match {
        case Nil => Nil
        case x::xs => if p(x) x::xs.filter(p) else xs.filter(p)
    }
}

```

`filter` returns list of all elements for which the predicate is true. As a matter of fact, `partition` method on `List`.

```scala
def partition(p: (A) => Boolean): (List[A], List[A])
```

A pair of, first, all elements that satisfy predicate p and, second, all elements that do not. Interesting because it splits a collection in two.


Another method of list 

```scala
final def takeWhile(p: (A) => Boolean): List[A]
```

takes longest prefix of elements that satisfy a predicate.

```scala
def dropWhile(p: (A) => Boolean): List[A]
```
drops longest prefix of elements that satisfy a predicate.

```scala
final def span(p: (A) => Boolean): (List[A], List[A])
```
Splits this list into a prefix/suffix pair according to a predicate. Returns
a pair consisting of the longest prefix of this list whose elements all satisfy `p`, and the rest of this list.

---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

The merge function as given uses a nested pattern match.
This does not reflect the inherent symmetry of the merge algorithm.
Rewrite merge using a pattern matching over pairs.
```scala
def merge(xs: List[Int], ys: List[Int]): List[Int] =
(xs, ys) match {
???
}
```
````

````{dropdown} Solution
```scala
def merge(xs: List[Int], ys: List[Int]): List[Int] =
(xs, ys) match {
case (Nil, ys) => ys
case (Nil, xs) => xs
case (x:xs1, y:ys1) => if (x<y) x::merge(xs1, ys) else y::merge(xs, ys1)
}
```
````

---