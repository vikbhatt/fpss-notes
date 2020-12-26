# Subtyping and Generics

Two principle type of polymorphism

* subtyping - You can pass subtype when base type is required
* generics - Parametrize type with other types

Let's look at their interactions

* bounds
* variance

Consider the method `assertAllPos` which 

* takes an `IntSet`
* returns the `IntSet` itself if all elements are positive
* throws an exception otherwise

What would be best type to give for `assertAllPos` 

```scala
def assertAllPos(s: IntSet): IntSet

```

Can we be more precise? (Above is fine by the way!)

For example if we pass `Empty`, we get `Empty` set. If we pass `NonEmpty` we might get `NonEmpty` or and exception. But the above knowledge is not shown in the definition.

One might want to express that assertAllPos takes Empty sets to
Empty sets and NonEmpty sets to NonEmpty sets.
A way to express this is:
```scala
def assertAllPos[S <: IntSet](r: S): S =

```

Here, `<: IntSet` is an upper bound of the type parameter `S`:
It means that S can be instantiated only to types that conform to `IntSet`.

Generally, the notation

* `S <: T` means: `S is a subtype of T`, and
* `S >: T` means: `S is a supertype of T`, or `T is a subtype of S`. 

You can also use lower bound for a variable.

`[S >: NonEmpty]` introduces a type parameter `S` that can range only over supertypes of `NonEmpty`.
So `S` could be one of `NonEmpty`, `IntSet`, `AnyRef`, or `Any`.

Finally it is possible to mix upper and lower bound.
For instance, `[S >: NonEmpty <: IntSet]` would restrict `S` any type on the interval between `NonEmpty` and
`IntSet`.