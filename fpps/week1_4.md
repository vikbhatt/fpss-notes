# Exercise Set 1

```{panels}

Write functions `and` and `or` such that for any expression $x$, $y$ that evaluates to Boolean,  
`and(x,y) == x && y`  

`or(x,y) ==  x || y`

You cannot use `&&` or `||` in the solution.

```


```{sidebar} `def` vs `val`
In Scala definitions given by `def` keyword, are call by name in nature (expression on the right hand side of
definition won't be evaluated unless it is used or called and evaluates every time it is called).  

Definitions given by `val` are evaluated instantly and only once, hence call by value.

Also function arguments are generally call by value in Scala, unless you specify with `:=>` before type, which are call by name in evaluation.

```
