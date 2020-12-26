# Variance

We talk about how sub-typing relates to generic property.

Roughly speaking, a type that accepts mutation should not be covariant. But immutable types can be covariant if some conditions are met.

Say `C[T]` is a parametrized type and, `A`, and `B` are types such that `A <:B`. 
In general there are three possible relationships between `C[A]` and `C[B]`

* `C[A] <: C[B]` `C` is *covariant*
* `C[A] >: C[B]` `C` is *contravariant*
* neither `C[A]` nor `C[B]` is subtype of other. `C` is *non-variant*

Scala lets you declare the variance of a type by annotating the type parameter

* `class C[+A] { ... }` `C` is covariant
* `class C[-A] { ... }` `C` is contravariant
* `class C[A] { ... }` `C` is non-variant

---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Say you have two function types

`type A = IntSet => NonEmpty`

`type B = NonEmpty => IntSet`

According to Liskov Substituion Principle, which of the following is true?
* `A <: B`
* `B <: A`
* `A` and `B` are unrelated


````

````{dropdown} Solution
`A <: B`. Using `B` I can pass `NonEmpty` and get an `IntSet`. Can you do that with `A`? Yes. Since `NonEmpty <: IntSet` you can pass `NonEmpty` in place of `IntSet`. The return value of `A` is `NonEmpty` which is special case for `IntSet`.
````

---

Generally we have the following rules for subtyping between the function types.

If `A2 <: A1` and `B1 <: B2` then `A1 => B1 <: A2 => B2`. So functions are contravariant in their argument types and covariant in their result types.

See the revised definition of `Function1` trait:

```scala
trait Function1[-A, +B]{
    def apply(x: A): B
}
```

If we turn `Array` into class and `update` into method it would look like this

```scala
class Array[+T]{
    def update(x:T) ... 
}
```

The problematic combination is

* the covariant type parameter `T`
* which appears in the parameter position of the update.

Scala compiler will check there is no problematic combinations when compiling a class with variance annotations.

Roughly

* *covariant* type parameters can only appear in method results.
* *contravariant* type parameters can only appear in method parameters.
* *invariant* type parameters can appear anywhere.

The precise rules are more involved. `Function1` checks out. 

Let's look at our previous implementation of `List`. One shortcoming was `Nil` had to be a class, but we want it to be a object (after all there is only one empty list). Can we change that?

Yes! Let's make `List` covariant.

```scala
trait List[+T]{
    def isEmpty: Boolean
    def head: T
    def tail: List[T]
}

class Cons[T](val head: T, val tail: List[T]) extends List[T]{
    def isEmpty: Boolean = False
}
object Nil extends List[Nothing]{
    def isEmpty: Boolean = True
    def head: Nothing = throw new NoSuchElementException("Nil.head")
    def tail: Nothing = throw new NoSuchElementException("Nil.tail")
}

```

