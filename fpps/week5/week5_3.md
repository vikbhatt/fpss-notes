# Higher Order List Functions

We have several recurring patterns like 

* transforming each element in a list in a certain way
* retrieving a list of all elements which satisfy certain criterion
* combining the elements of a list using an operator

Functional languages allow programmers to write generic functions that implement patterns such as these with higher-order functions.

## Map

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


## Filter

```scala
abstract class List[T]{
    def filter(p: T => Boolean): List[T] = this match {
        case Nil => Nil
        case x::xs => if p(x) x::xs.filter(p) else xs.filter(p)
    }
}

```

`filter` returns list of all elements for which the predicate is true. Besides filter the following methods extract sublists based on predicate.

| Property | Definition |
| ------------- |:-------------:|
| `xs filterNot p` | The list containing those elements of `xs` that doesn't satisfy the predicate. Same as `xs filter (x => !p(x))`.|
| `xs partition p` | Same as `(xs filter p, xs filterNot p)` but computed in single traversal of list `xs` |
| `xs takeWhile p` | The longest prefix of list `xs` consisting of elements that all satisfying `p`|
| `xs dropWhile p` | The remainder of the list `xs` after any leading elements satisfying `p` has been removed.|  
| `xs span p`| Same as `(xs takeWhile p, xs dropWhile p)` but computed in single traversal of list `xs`.|


---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`
Write a function pack that packs consecutive duplicates of list into sub lists

```scala
pack(List("a","a","a","b","c","c","a"))
```
should give 

```scala
List(List("a","a","a"), List("b"),List("c","c"),List("a"))
```
````

````{dropdown} Solution
```scala
def pack[T](xs: List[T]): List[List[T]] = xs match {
    case Nil => Nil
    case x::xs1 => {
        val (first, rest) = xs span(y => y==x)
        first :: pack(rest)
    }
}
```
````
---

---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`
Using pack, write a function encode that produces the run-length encoding of a list. The idea is to encode n consecutive duplicates of an element x as a pair (x, n). For instance,

```scala
encode(List("a", "a", "a", "b", "c", "c", "a"))
```

should give 

```scala
List(("a", 3), ("b", 1), ("c", 2), ("a", 1))
```

````

````{dropdown} Solution
```scala
def encode[T](xs: List[T]): List[(T, Int)] = pack(xs).map(x => (x.head, x.length))
}
```
````

---



## Reduction of Lists

Another common operation on lists is to combine the elements of list with a given operator 

For example

```scala

sum(List(1,2,3,4)) = 1+2+3+4
product(List(1,2,3,4)) = 1*2*3*4
```

### Reduce Left

`reduceLeft` inserts a given binary operator between two adjacent elements of the list.

`List(x1,x2,..,xn) reduceLeft op = (...(x1 op x2) op ...) op xn`. If we draw it as tree,it looks like left leaning tree.

We can express 


```scala

def sum(xs: List[Int]) = (0::xs) reduceLeft ((x,y) => x+y)
def product(xs: List[Int]) = 1:;xs reduceLeft ((x,y) => x*y)

```

Instead of `(x,y) => x*y` we can write `(_*_)`. Every `_` represents a new function parameter going from left to right. The parameters are defined at next outer pair of parentheses.

But `reduceLeft` can not be applied only empty list.

The function `reduceLeft` is defined in terms of more general function `foldLeft`. `foldLeft` is like `reduceLeft` but takes an accumulator `z`, as an additional parameter which is returned when called on empty list.

`(List(x1, ..., xn) foldLeft z)(op) = (...(z op x1) op ... ) op xn`

So sum and product can be defined as follows.

```scala
def sum(xs: List[Int]) = (xs foldLeft 0)(_ + _)
def product(xs: List[Int]) = (xs foldLeft 1)(_ * _)
```

`foldLeft` and `reduceLeft` can be implemented in class `List` as follows.

```scala
abstract class List[T] { ...
def reduceLeft(op: (T, T) => T): T = this match {
case Nil => throw new Error(ŏNil.reduceLeft)
case x :: xs => (xs foldLeft x)(op)
}
def foldLeft[U](z: U)(op: (U, T) => U): U = this match {
case Nil => z
case x :: xs => (xs foldLeft op(z, x))(op)
}
}
```


Applications of `foldLeft` and `reduceLeft` unfold on trees that lean to the left.


### Reduce Right

Two dual functions `foldRight` and `reduceRight` which produce trees lean to the right. They are defined as follows.

`List(x1, ..., x{n-1}, xn) reduceRight op = x1 op ( ... (x{n-1} op xn) ... )`

`(List(x1, ..., xn) foldRight acc)(op) = x1 op ( ... (xn op acc) ... )`

```scala
def reduceRight(op: (T, T) => T): T = this match {
case Nil => throw new Error("Nil.reduceRight")
case x :: Nil => x
case x :: xs => op(x, xs.reduceRight(op))
}
def foldRight[U](z: U)(op: (T, U) => U): U = this match {
case Nil => z
case x :: xs => op(x, (xs foldRight z)(op))
}
```


For operators that are associative and commutative `folLeft` and `foldRight` are equivalent.(even thought efficiency may be different). But sometimes only one of the two operators is appropriate.

Here is another formulation of `concat`.

```scala
def concat[T](xs: List[T], ys: List[T]): List[T] =
(xs foldRight ys) (_ :: _)
```

Here you cannot replace `foldRight` with `foldLeft`. You would get type check error.