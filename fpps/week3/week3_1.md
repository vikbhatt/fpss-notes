# Class Hierarchies

Let's consider implementing sets(only integer sets for now) with binary trees.

```scala
abstract class IntSet{
  def incl(x: Int): Boolean
  def contains(x: Int): Boolean
}
```
You cannot instantiate above class with `new` operator since it's an abstract class. Abstract classes can contain member which are missing an implementation.

A tree can be defined recursively as follows:
- A tree for empty subset.
- A tree consisting of an integer and two subtrees.

For each node, the right trees contains values higher than the node and left trees contains values less than the node.

```scala
class Empty extends IntSet{
  def contains(x: Int): Boolean = false
  def incl(x: Int): IntSet = new NonEmpty(x, new Empty, new Empty)
}
```


```scala
class NonEmpty(elem: Int, left: IntSet, right: IntSet) extends IntSet{
  def contains(x: Int): Boolean = {
    if(x < elem) left contains x
    if(x > elem) right contains x
    else true
  }
  def incl(x: Int): Boolean = {
    if(x < elem) new NonEmpty(elem, left incl x, right)
    if(x > elem) new NonEmpty(elem, left, right incl x)
    else this
  }
}
```

Above definitions are purely functional (no mutations are defined). When we say include a new element in the tree, it means that we create a new tree that contains the previous element of the tree, and a larger left sub tree where x is included in the previous left sub-tree, and the current sub-tree on the right.

These type of data structures are called persistent data structures because parts of data structures are reused when new items are created (changes to data structure results in new data structure). This idea is key to scaling up functional programming up to collections.


In Scala any user defined class extends another class. If no super class is given `java.lang.Object` is assumed.

The direct and indirect superclass of a class C are called base classes.

It is also possible to define non-abstract definition in subclass by using override.

In the above example, we could argue there is only single empty set. It is overkill to creat too many instances of empty set. Create singleton object

```scala
object Empty extends IntSet{
  def contains(x: Int):Boolean = false
  def incl(x: Int):IntSet = new NonEmpty(3, Empty, Empty)
}
```

No other `Empty` instances can be(or need to be) created. Singleton objects are values, so `Empty` evaluates to itself, the first time it is called(and created for you).

-------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Write a method for union for forming union of two sets.
```scala
abstract class IntSet {
def incl(x: Int): IntSet
def contains(x: Int): Boolean
def union(other: IntSet): IntSet
}
```
````

````{dropdown} Solution

```scala
object Empty extends IntSet{
  def contains(x: Int): Boolean = false
  def incl(x: Int): IntSet = new NonEmpty(x, Empty, Empty)
  def union(other: IntSet): IntSet = other
}

class NonEmpty(elem: Int, left: IntSet, right: IntSet) extends IntSet{
  def contains(x: Int): Boolean = {
    if(x < elem) left contains x
    if(x > elem) right contains x
    else true
  }
  def incl(x: Int): IntSet = {
    if(x < elem) new NonEmpty(elem, left incl x, right)
    if(x > elem) new NonEmpty(elem, left, right incl x)
    else this
  }
  def union(other: IntSet): IntSet = {
    ((left union right) union other) incl elem
  }
}
```
````

--------------------------------------------------------------------------------


## Dynamic Dispatch

Object oriented languages implement dynamic method dispatch. That means code invoked by method call depends on the runtime type of the object that contains the method.

---------More--------------------
