# Other Collections


We have seen that lists are **linear**: Access to the first element is much faster than access to the middle or end of a list. The Scala library also defines an alternative sequence implementation, `Vector`.
This one has more evenly balanced access patterns than `List`. `Vector` is implemented as very very shallow trees. A vector of 32 elements is just an array (stored in sequence). If a vector becomes larger than 32, you would have vector of 32 pointer to arrays of 32 elements. Once we filled 32*32 you would have pointers to pointers to arrays of 32 elements. Each level would go deeper.

How much time does it take to retrieve for an element?

The number of access are the depth of the vector. A depth of 6 would give billion elements (actually it's $log_{32}(N)$ where N is number of elements)

Vectors are created analogously to lists:

```scala
val nums = Vector(1, 2, 3, -88)
val people = Vector(”Bob”, ”James”, ”Peter”)
```

They support the same operations as lists, with the exception of `::`
Instead of `x :: xs`, there is
`x +: xs` Create a new vector with leading element `x`, followed by all elements of `xs`.
`xs :+ x` Create a new vector with trailing element `x`, preceded by all elements of `xs`.
(Note that the `:` always points to the sequence.)

A common base class of `List` and `Vector` is `Seq`, the class of all sequences. `Seq` itself is a subclass of `Iterable`

Arrays and Strings support the same operations as Seq and can
implicitly be converted to sequences where needed.
(They cannot be subclasses of Seq because they come from Java)

```scala
val xs: Array[Int] = Array(1, 2, 3)
xs map (x => 2 * x)
val ys: String = "Hello world!"
ys filter (_.isUpper)
```

Another simple kind of sequence is the range. It represents a sequence of evenly spaced integers.
Three operators:
`to` (inclusive), `until` (exclusive), `by` (to determine step value):

```scala
val r: Range = 1 until 5
val s: Range = 1 to 5
1 to 10 by 3
6 to 1 by -2
```
Ranges are represented as single objects with three fields: lower bound, upper bound, step value.

## Operations on sequences

| Operation       | Definition           |
| ------------- |:-------------:|
|`xs exists p`| true if there is an element `x` of `xs` such that `p(x)` holds, false otherwise.|
|`xs forall p`| true if `p(x)` holds for all elements `x` of `xs`, false otherwise.|
|`xs zip ys`| A sequence of pairs drawn from corresponding elements of sequences `xs` and `ys`.|
|`xs.unzip`| Splits a sequence of pairs `xs` into two sequences consisting of the first, respectively second halves of all pairs.|
|`xs.flatMap f`| Applies collection-valued function `f` to all elements of `xs` and concatenates the results|
|`xs.sum`| The sum of all elements of this numeric collection.|
|`xs.product`| The product of all elements of this numeric collection|
|`xs.max`| The maximum of all elements of this collection (an Ordering must exist)|
|`xs.min`| The minimum of all elements of this collection|