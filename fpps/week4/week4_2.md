# Functions as objects

How function types relate to classes? 
How function values relate to objects?

 Function values are treated as objects in Scala. The function type `A => B` is just an abbreviation for the class 
 `scala.Function1[A,B]` which is roughly defined as follows


 ```scala
package scala
trait Function1[A, B]{
    def apply(x: A): B
}
 ```
So functions are objects with `apply` methods. There are also traits `Function2` , `Function3`,... for which take more parameters (currently we have 22).

## Expansion of function value

An anonymous function 

```scala
(x: Int) => x*x
```

is expanded into

```scala
class AnonFun extends Function1[Int, Int]{
    def apply(x: Int) = x*x
}
new AnonFun

```

or using anonymous class syntax

```scala
new Function1[Int, Int]{
    def apply(x: Int) = x*x
}

```

## Expansion of function calls

A function call `f(a,b)` where f is value of some class type is expanded to 
`f.apply(a,b)`.

So the OO-translation of 

```scala
val f = (x:Int) => x*x
f(7)

```

would be

```scala

val f = new Function1[Int, Int]{
    def apply(x: Int) = x*x
}
f.apply(7)
```
Note that a method such as 
```scala

def f(x: Int):Boolean ...

```
is not itself a function value. But if `f` is used in place where a `Function` type is expected, it is automatically converted into a function value.

```scala

(x:Int) = f(x)

```
is expanded into 

```scala
new Function1[Int, Boolean]{
    def apply(x:Int) = f(x)
}

```

-------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Define an object List with three functions, so users can create lists of lengths of 0-2.
```scala
object List{
    ....
}

List()
List(1)
List(1,2)
```
````

````{dropdown} Solution
`List` is used in function position like `List(1,2)` so we need to define `apply` method.
```scala
trait List[T]{
    def isEmpty: Boolean
    def head: T
    def tail: List[T]
}

class Cons[T](val head: T, val tail: List[T]) extends List[T]{
    def isEmpty: Boolean = False
}
class Nil[T] extends List[T]{
    def isEmpty: Boolean = True
    def head: Nothing = throw new NoSuchElementException("Nil.head")
    def tail: Nothing = throw new NoSuchElementException("Nil.tail")
}
object List{
    def apply[T](x1: T, x2: T): List[T] = new Cons(x1, new Cons(x2, new Nil))
    def apply[T]() = new Nil
}
```
````

--------------------------------------------------------------------------------