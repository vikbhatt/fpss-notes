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

Consider adding a new method `prepend` could look like this:

```scala

trait List[+T]{
    ...
    def prepend(elem: T): List[Y] = new Cons(elem, this)
    ...
}
```

But above code fails variance checking because, covariant parameters cannot appear in method parameters (can only appear in results). But we designed the rules for immutable data structures. In case of `prepend` methods we are actually creating new data structure. So, is there deep wisdom to the rule?

Yes, actually the compiler is right to throw out our `List` with `prepend` because it violates Liskov Substitution Principle.

Here is something one can do with a list `xs` of type `List[IntSet]`:

```scala
xs.prepend(Empty)
```

But the same operation on a list `ys` of type `List[NonEmpty]` would lead to type error:

```scala
ys.prepend(NonEmpty) //compile error
```

So `List[NonEmpty]` cannot be a subtype of `List[IntSet]`.

But can we make it variance correct?

We can use a lower bound.

```scala
def prepend[U >: T](elem: U): List[U] = new Cons(elem, this)
```

This passes, variance checks because:

* Covariant type parameters may appear in lower bounds of method type parameters.
* Contravariant type parameters may appear in upper bounds of method

---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Implement prepend as shown in trait List.
```scala
def prepend[U >: T](elem: U): List[U] = new Cons(elem, this)
```
What is the result type of this function:
```scala
def f(xs: List[NonEmpty], x: Empty) = xs prepend x
```
````

````{dropdown} Solution
As always we have `IntSet` supertype of `NonEmpty` and `Empty`. `T` in this case is `NonEmpty`, which takes `U >: T` here `U` is `Empty`. Here type inference would chose `IntSet` which would workout. `List[NonEmpty] <: List[IntSet]`. The type gets returned from `f` is `List[IntSet]`. `IntSet` is the smallest type that contains `NonEmpty` and `Empty`.
````

---