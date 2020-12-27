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