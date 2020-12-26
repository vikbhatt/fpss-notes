# Objects Everywhere

 A pure object oriented language is one in which every value is an object. If the language is based on class, it means the type of each value is a class.

 Is Scala a pure object oriented language?

 Let's look at primitive types and functions (looks like non-objects!)

 Conceptually types such as `Int` and `Boolean` are like other classes defined in package Scala. For efficiency, Scala compiler represents the `Scala.Int` as 32-bit integers and `Scala.Boolean` as Java's Booleans etc.
 The `Boolean` type refers to the JVM's primitive type `boolean`. But one could define it from the first class.

 ```scala
package idealized.scala

abstract class Boolean {
def ifThenElse[T](t: => T, e: => T): T
def && (x: => Boolean): Boolean = ifThenElse(x, false)
def || (x: => Boolean): Boolean = ifThenElse(true, x)
def unary_!: Boolean = ifThenElse(false, true)
def == (x: Boolean): Boolean = ifThenElse(x, x.unary_!)
def != (x: Boolean): Boolean = ifThenElse(x.unary_!, x)
...
}

object true extends Boolean {
def ifThenElse[T](t: => T, e: => T) = t
}
object false extends Boolean {
def ifThenElse[T](t: => T, e: => T) = e
}
```


-------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Provide an implementation of the abstract class `Nat` that represents non-negative integers. Don't use primitive numerical classes.
```scala
abstract class Nat {
def isZero: Boolean
def predecessor: Nat
def successor: Nat
def + (that: Nat): Nat
def - (that: Nat): Nat
}

object Zero extends Nat
class Succ(n: Nat) extends Nat
```
````

````{dropdown} Solution
```scala
object Zero extends Nat{
    def isZero = true
    def predecessor = throw new Error("0.predecessor")
    def successor: Nat = new Succ(this)
    def + (that: Nat): Nat = that
    def - (that: Nat): Nat = if (that.isZero) this else throw new Error("negative number")
}
class Succ(n: Nat) extends Nat{
    def isZero = false
    def predecessor = n
    def successor: Nat = new Succ(this)
    def + (that: Nat): Nat = new Succ(n + that)
    def - (that: Nat): Nat = if(that.isZero) that else n - that.predecessor
}

```
````

--------------------------------------------------------------------------------

We see complete treatment of natural number from first principles. We didn't need primitive types. 