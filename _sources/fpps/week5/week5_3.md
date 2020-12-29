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

