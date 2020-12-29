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