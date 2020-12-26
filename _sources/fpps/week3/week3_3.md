# Polymorphism

We have seen simple types such as `Int`, `Boolean`, and `Rational`.

A fundamental data structure to many languages are immutable linked list. It is constructed from two building blocks

* `Nil` - the empty list
* `Cons` - a cell the containing the element and the (pointer reference) remainder of the list.


Here is an outline of class hierarchy that represents list of integers

```scala
trait IntList ...
class Cons(val head: Int, val tail: IntList) extends IntList ...
class Nil extends IntList ...
```

Note that, by defining `Cons` parameters as `val`, it acts as parameters and fields of the class. It is equivalent to

```scala
class Cons(_head: Int, _tail: IntList) extends IntList {
val head = _head
val tail = _tail
}
```

We can generalize it using type parameter (written in square brackets).

```scala
trait List[T]
class Cons[T](val head: T, val tail: List[T]) extends List[T]
class Nil[T] extends List[T]
```

Let's construct a trait for general list.


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
```

Notice in `Cons` class implementation , we didn't explicitly define `head` and `tail` definitions. We have already implement using parameter names using `val` values. They are legal implementation. They count as fields. IN Scala, `val` definitions (a.k.a field definitions) in classes, are special cases of methods. They can overwrite and implement the abstract methods in traits. 

Functions can also have type parameters.

```scala
def singleton[T](elem: T) = new Cons[T](elem, new Nil[T])
```

We can write

```scala
singleton[Int](1)
singleton[Boolean](true)
```

In most cases type parameters are redundant and can be left out. So we can also write 

```scala
singleton(1)
singleton(true)
```

the compiler can infer the correct type.

As a matter of fact, type parameter do not effect evaluation. We can assume all type parameters and information are removed before evaluating the program. Type parameters are only required for compiler to verify the correctness of the program. This is called *type erasure*. (Ex. Java, Haskell, O'Cml). Some languages (C, C++, C#) keep the type information at run time.

Polymorphism means that a function type can come in many forms.
In programming it means that

* the functions can be applied to arguments of many types.
* the type can have instance of many types.

We have seen two principal forms of polymorphism

* Sub-typing: Instances of sub classes can be passed to a base class
* Generics: Instances of a class or function are created by type parametrization.
  
-------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Write a function `nth` that takes an integer `n` and list and selects the n'th element of the list. Elements are numbered from 0. If index is out of range, `IndexOutOfBoundsException` should be thrown.
````

````{dropdown} Solution
```scala
def nth[T](n : Integer, xs: List[T]): T = {
if xs.isEmpty throw new IndexOutOfBoundsException
if (n==0) xs.head
else nth(n-1, xs.tail)
}
```
````

--------------------------------------------------------------------------------
