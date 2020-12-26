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