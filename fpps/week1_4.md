# Exercise Session

````{panels}
:column: col-lg-12 p-2

{badge}`Ex1`

Write functions `and` and `or` such that for any expression $x$, $y$ that evaluates to Boolean,  following holds true

`and(x,y) == x && y`  

`or(x,y) ==  x || y`

You cannot use `&&` or `||` in the solution.
````
-------------------------------------------------------------------

```{important}
Logical operators `&&` and `||` are short circuited. Operands on both side of the operator are not evaluated unless required.

For example `false && e` always evaluates to `false` irrespective of the value of the expression `e` and the expression `e` is not evaluated.

Another example is `true || e` always return `true`, `e` expression is not evaluated further.

Consider the short circuit nature of the logical operators, and think how you can implement this in terms of call by name or call by nature of the arguments in the `and` and `or` functions.
```


```{admonition} Call by Name *vs* Call by Value
:class: tip
In Scala definitions given by `def` keyword, are call by name in nature (expression on the right hand side of
definition won't be evaluated unless it is used or called and evaluates every time it is called).  

Definitions given by `val` are evaluated instantly and only once, hence call by value.

Also function arguments are generally call by value in Scala, unless you specify with `: =>`(mind the gap between `:` and `=>`) before type, which are call by name in evaluation.

```

--------------------------------------------------------------------------------
:::{admonition,dropdown} Ex1 solution

```scala
def and(x: Boolean, y: =>Boolean) = if(x) y else false
def or(x: Boolean, y: => Boolean)= if(x) true else y
```
:::

--------------------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Ex2`

Write a function that computes square root using Newton's iteration method in functional programming style.

`def sqrt(x: Double): Double`

You cannot use imperative structures like loops, iterators and mutations.
````

------------------------------------------------------------------------------------------
```{admonition}Newton's Method
:class: tip

Start with intial estimate $y$ and repeatedly improve estimate by taking mean of $y$ and $\frac{x}{y}$.

```

```{admonition}Block Scope
:class: tip

Any definitions defined before and outside of block expression ({...}) is visible inside the block, unless a new definition with same name exists inside the block, then it shadows the outside value.

```
--------------------------------------------------------------------------------------------

:::{admonition,dropdown} Ex2 solution
```scala

def square(x: Double): Double = {

  def isGoodEnough(guess: Double):Boolean = Math.abs(guess*guess - x)/x < 0.0001

  def improve(guess: Double): Double =  guess +  x/guess

  def sqrtIter(guess: Double):Double = if(isGoodEnough(guess)) guess else improve(guess)

   sqrtIter(1.0)
  }
```
:::
