# Variance

We talk about how sub-typing relates to generic property.

ROughly speaking, a type that accepts mutation should not be covariant. But immutable types can be covariant if some conditions are met.

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
* 

````

````{dropdown} Solution
`A <: B`. Using `B` I can pass `NonEmpty` and get an `IntSet`. Can you do that with `A`? Yes. Since `NonEmpty <: IntSet` you can pass `NonEmpty` in place of `IntSet`. The return value of `A` is `NonEmpty` which is special case for `IntSet`.
````

---
