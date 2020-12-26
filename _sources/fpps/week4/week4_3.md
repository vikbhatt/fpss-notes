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

## Covariance

There’s another interaction between subtyping and type parameters
we need to consider. Given:

`NonEmpty <: IntSet`


is


`List[NonEmpty] <: List[IntSet] ?`


Intuitively, this makes sense: A list of non-empty sets is a special
case of a list of arbitrary sets.
We call types for which this relationship holds *covariant* because
their subtyping relationship varies with the type parameter.

Can every parametrized is covariant?

## Array Typing Problem

For perspective, let’s look at arrays in Java (and C#).
Reminder:

*  An array of T elements is written `T[]` in Java.
* In Scala we use parameterized type syntax `Array[T] `to refer to the same type.
Arrays in Java are covariant, so one would have:
`NonEmpty[] <: IntSet[]`

But covariant array typing causes problems.
To see why, consider the Java code below.

```java
NonEmpty [] a = new NonEmpty []{ new NonEmpty (1 , Empty , Empty )}
IntSet [] b = a
b [0] = Empty
NonEmpty s = a [0]
```

Initially, a `NonEmpty` array created with reference `a`. Another `IntSet` array initialized with reference `b` assigned the value a (which is allowed by covariance). But we set the fist value of array using reference `b` to `Empty` and pull out the first value of array using reference `a` and assign it to `NonEmpty` reference `s`.
It looks like we assigned in the last line an `Empty` set to a variable of type `NonEmpty`!
