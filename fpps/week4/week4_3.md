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

One might want to express that `assertAllPos` takes `Empty` sets to
`Empty` sets and `NonEmpty` sets to `NonEmpty` sets.
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

Can every parametrized type be covariant?

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

Initially, a `NonEmpty` array created with reference `a`. Another `IntSet` array initialized with reference `b` assigned the value a (which is allowed by covariance property of arrays). But we set the fist value of array using reference `b` to `Empty` and pull out the first value of array using reference `a` and assign it to `NonEmpty` reference `s`.
It looks like we assigned in the last line an `Empty` set to a variable of type `NonEmpty`!


The third line throws run time exception `ArrayStoreException`. In order to save itself from covariance, Java needs to store in every array type tag, at what type the array is created (here it is `NonEmpty`). When we assign, the run time type gets checked against the assigned value. But the code compiles just fine. 


The following principle, stated by Barbara Liskov, tells us when a
type can be a subtype of another.
If `A <: B`, then everything one can to do with a value of type `B` one should also be able to do with a value of type `A`.

-------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

The problematic array example would be written as follows in Scala:
```scala
val a: Array[NonEmpty] = Array(new NonEmpty(1, Empty, Empty))
val b: Array[IntSet] = a
b(0) = Empty
val s: NonEmpty = a(0)
```
When you try out this example, what do you observe?
* A type error in line 1
* A type error in line 2
* A type error in line 3
* A type error in line 4
* A program that compiles and throws an exception at run-time
* A program that compiles and runs without exception
````

````{dropdown} Solution
We would expect type error in line 2, because `a` is `Array[NonEmpty]` and `b` is `Array[IntSet]` but in Scala arrays are not covariant.
````

--------------------------------------------------------------------------------