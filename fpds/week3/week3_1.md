# Type-Directed Programming

We have seen that Scala compiler can infer types from values. For example,

```scala
val x = 42
```
Compiler can infer the type of `x` as `Int`, from the value assigned `42` is `Int`.

We can ask the compiler to do the opposite thing for us. Namely, infer values from types. When there is exactly one obvious value for the type, compiler can find this value and provide it to you.

## Sort Example

In order to represent the ordering relation between two elements of type `A`, there is already a type in the standard library.

```scala
package scala.math

trait Ordering[A] {
  def compare(a1: A, a2: A): Int
  def lt(a1: A, a2: A): Boolean = compare(a1, a2) <= 0
  ...
}
```

`Ordering` contains a single abstract method `compare(x: T, y: T): Int` which returns an integer whose sign communicates how x compares to y.

The result sign has the following meaning:

- negative if x < y
- positive if x > y
- zero otherwise (if x == y)

We can define a `sort` method for sorting `List` of polymorphic types.

```scala
import scala.math.Ordering
def sort[A](xs: List[A])(ord: Ordering[A]): List[A] = {
  ...
  ... if (ord.lt(x, y)) ...
  ...
}
sort(xs)(Ordering.Int)
sort(strings)(Ordering.String)
```

````{note}
Here `Ordering.Int` and `Ordering.String` represent values not types. In Scala, it is possible to use same symbol for both values and types. Scala can deduce wether a symbol refers to type or value depending on the context.
For sake of completeness, here is the implementation of `Ordering.Int` in standard library of `scala.math` package.
```scala
  trait IntOrdering extends Ordering[Int] {
    def compare(x: Int, y: Int): Int = java.lang.Integer.compare(x, y)
  }
  implicit object Int extends IntOrdering
```
````

`List[Int]` are always sorted according to `Ordering.Int`.

`List[String]` are always sorted with `Ordering.String`.

```scala
sort(xs)(Ordering.Int)
sort(ys)(Ordering.Int)
sort(strings)(Ordering.String)
```

Can we reduce boilerplate? Can we let the compiler systematically infer the ordering from the type of the element?

Turns out we can!

## Implicit Parameters

The first step in indicating that we want the compiler to supply the ordering argument by marking it with `implicit`.


```scala
def sort[A](xs: List[A])(implicit ord: Ordering[A]): List[A] = ...
```

We can omit the parameter `ord` and let the compiler try to infer it for us. The compiler infers the argument `ord` value based on the expected type `A`.

For example,

```scala

val xs = List(34, 12, 45, 17, 2)
sort(xs)

```

Since `xs` has type `List[Int]`, the compiler fixes the type parameter of the sort `A` to `Int`. The compiler looks for candidate definitions that match the expected type `Ordering[Int]` and the only matching definition is `Ordering.Int` in our scope.

````{note}
* A method can have only one implicit parameter list, and it must be the last parameter list given.
* At call site, the arguments of the given clause are usually left out, although it is possible to explicitly pass them:

```scala
sort(xs) //Ordering argument inferred by the compiler
sort(xs)(Ordering.Int.reverse) //Explicit Argument
```
````

## Candidates for Implicit Parameters

The compiler searches for candidate definitions that:

- have type `T`,
- are marked implicit,
- are visible at the point of the function call, or are defined in a companion object associated with T.

If there is a single most specific definition, it will be taken as the argument. Otherwise an error is reported.


### Implicit Defintions

We can write implicit definitions by marking it with keyword `implicit` 

```scala
object Ordering {
  implicit val Int: Ordering[Int] = ...
}
```

Above code defines implicit value of type `Ordering[Int]` named `Int`. Any `val`, `lazy val` , `def` and `object` can be marked implicit`. Implicit definitions can also take type parameters.

A companion object is an object that has the same name as a type. For instance, the object `scala.math.Ordering` is the companion of the type `scala.math.Ordering`.

The types associated with a type T are:

- if `T` has parent types `T₁ with T₂ ... with Tₙ`, the union of the parts of `T₁, ... Tₙ` as well as `T` itself,
- if `T` is a parameterized type `S[T₁, T₂, ..., Tₙ]`, the union of the parts of `S` and `T₁, ..., Tₙ`,
otherwise, just `T` itself.

The search process can result into one candidate found or no candidate found.
If there is no implicit definition matched the query type, an error is reported.

```scala
scala> def f(implicit n: Int) = ()

scala> f
       ^
error: could not find implicit value for parameter n: Int 
```

On, the other hand if more than one eligible definitions are found, ambiguity error is reported.


```scala
scala> implicit val x: Int = 0

scala> implicit val y: Int = 1

scala> def f(implicit n: Int) = ()

scala> f
       ^
error: ambiguous implicit values:
  both value x of type => Int
  and value y of type => Int
  match expected type Int 
```

Actually, several implicit definitions matching the same type don’t generate an ambiguity if one is more specific than the other.

A definition `a: A` is more specific than a definition `b: B` if:

* type A has more “fixed” parts,
* or, a is defined in a class or object which is a subclass of the class defining b.

For example,
```scala

implicit def universal[A]: A = ???
implicit def int: Int = ??? 
def f(implicit n: Int) = ()
f 
```

In the above code, `universal` allows a type parameter, and `int` doesn't. So `int` is more specific than `universal`, hence chosen as candidate for `f`.

Which implicit definition matches the Int implicit parameter when the following f method is called?

```scala
trait A {
  implicit val x: Int = 0
}
trait B extends A {
  implicit val y: Int = 1
  def f(implicit n: Int) = ()
  f
} 
```

Here, because y is defined in a trait that extends A (which is where x is defined), y is more specific than x. Thus, there is no ambiguity and the compiler selects y.


## Context Bounds

Syntactic sugar allows us omission of parameter list.

```scala
def printSorted[A: Ordering](as: List[A]): Unit = {
  println(sort(as))
}
```

Type parameter `A` has one context `Ordering`. This is equivalent to writing

```scala
def printSorted[A](as: List[A])(implicit ev1: Ordering[A]): Unit = {
  println(sort(as))
}
```

A definition such as

```scala
def f[A: U₁ ... : Uₙ](ps): R = ...
```

is expanded to

```scala
def f[A](ps)(implicit ev₁: U₁[A], ..., evₙ: Uₙ[A]): R = ...
```

We can query an implicit value of a type by using `implicitly` function.

```scala
scala> implicitly[Ordering[Int]]
res0: Ordering[Int] = scala.math.Ordering$Int$@73564ab0
```

`implicitly` is not a special keyword, it's a library operation.

```scala
def implicitly[A](implicit value: A): A = value
```