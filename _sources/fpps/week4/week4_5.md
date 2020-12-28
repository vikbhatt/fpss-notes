# Decomposition 

Let's say you have a hierarchy of classes and you want to build tree-like data structures from the instances of these classes. How do you build such a tree? And how do you find out what kind of elements are in the tree? And how do you access the data that's stored in this elements? That's the problem of decomposition, which we are going to tackle in this session.

Suppose you want to write a small interpreter for arithmetic expressions.

To keep it simple, let’s restrict ourselves to numbers and additions.

Expressions can be represented as a class hierarchy, with a base trait `Expr` and two subclasses, `Number` and `Sum`.

To treat an expression, it’s necessary to know the expression’s shape and its components.

```scala

trait Expr {
def isNumber: Boolean
def isSum: Boolean
def numValue: Int
def leftOp: Expr
def rightOp: Expr
}
class Number(n: Int) extends Expr {
def isNumber: Boolean = true
def isSum: Boolean = false
def numValue: Int = n
def leftOp: Expr = throw new Error("Number.leftOp")
def rightOp: Expr = throw new Error("Number.rightOp")
}
class Sum(e1: Expr, e2: Expr) extends Expr {
def isNumber: Boolean = false
def isSum: Boolean = true
def numValue: Int = throw new Error("Sum.numValue")
def leftOp: Expr = e1
def rightOp: Expr = e2
}
```

You can now write an evaluation function as follows.

```scala
def eval(e: Expr): Int = {
if (e.isNumber) e.numValue
else if (e.isSum) eval(e.leftOp) + eval(e.rightOp)
else throw new Error("Unknown expression" + e)
}
```

_Problem_: Writing all these classification and accessor functions quickly becomes tedious!

So, what happens if you want to add new expression forms, say

```scala
class Prod(e1: Expr, e2: Expr) extends Expr // e1 * e2
class Var(x: String) extends Expr // Variable ‘x’
```

You need to add methods for classification and access to all classes defined above. 


---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

To integrate `Prod` and `Var` into the hierarchy, how many new method definitions do you need?
(including method definitions in Prod and Var themselves, but not counting methods that were already given.)

````

````{dropdown} Solution
We need two additional methods in `trait Expr` `def isProd` and `def isVar`. Another operation that gives the name of the variable `def name` is required. So in total 8 methods for the trait. Each class (`Expr`,`Prod`,`Var`,`Sum`,`Number`) has to implement these methods (8 * 5 = 40). Previously we have 3 classes each have 5 methods, so additionally we implement 25 methods.
````

---

So generally if we continue to extend the hierarchy, the number of methods need tend to grow quadratically. What to do about it?

Scala let’s you do these using methods defined in class `Any`:

```scala
def isInstanceOf[T]: Boolean // checks whether this object’s type conforms to ‘T‘
def asInstanceOf[T]: T // treats this object as an instance of type ‘T‘
// throws ‘ClassCastException‘ if it isn’t.
```