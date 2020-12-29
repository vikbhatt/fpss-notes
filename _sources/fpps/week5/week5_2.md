# Pairs and Tuples

As a non-trivial example, let’s design a function to sort lists that is more efficient than insertion sort.
A good algorithm for this is merge sort. The idea is as follows:
If the list consists of zero or one elements, it is already sorted.
Otherwise,

* Separate the list into two sub-lists, each containing around half of the elements of the original list.
* Sort the two sub-lists.
* Merge the two sorted sub-lists into a single sorted list.

Here is the implementation of that algorithm in Scala:

```scala
def msort(xs: List[Int]): List[Int] = {
val n = xs.length/2
if (n == 0) xs
else {
def merge(xs: List[Int], ys: List[Int]) = ???
val (fst, snd) = xs splitAt n
merge(msort(fst), msort(snd))
}
}
```

Here is a definition of the merge function:

```scala
def merge(xs: List[Int], ys: List[Int]) =
xs match {
case Nil =>
ys
case x :: xs1 =>
ys match {
case Nil =>
xs
case y :: ys1 =>
if (x < y) x :: merge(xs1, ys)
else y :: merge(xs, ys1)
}
}
```


The `splitAt` function on lists returns two sublists

* the elements up the the given index
* the elements from that index

The lists are returned in a pair. The pair consisting of `x` and `y` is written `(x, y)` in Scala. This works analogously for tuples with more than two elements.

A tuple type `(T1, ..., Tn)` is an abbreviation of the parameterized type `scala.Tuplen[T1, ..., Tn]`.
A tuple expression `(e1, ..., en)` is equivalent to the function application `scala.Tuplen(e1, ..., en)`.
A tuple pattern `(p1, ..., pn)` is equivalent to the constructor pattern `scala.Tuplen(p1, ..., pn)`.


Here, all `Tuplen` classes are modeled after the following pattern:

```scala
case class Tuple2[T1, T2](_1: +T1, _2: +T2) {
override def toString = ”(” + _1 + ”,” + _2 +”)”
}
```

The fields of a tuple can be accessed with names `_1, _2, …`.

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