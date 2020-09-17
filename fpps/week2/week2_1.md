# Higher Order Functions

Functional programming languages treat functions as first class values. Functions can be passed, and returned from other functions.

Higher order functions
: Functions that take other functions as parameters and return other functions are called higher order functions.

The type `A => B` is the type of a functions that takes an argument of type A and result of type B.

 We can define types for our own convenience as

 ```scala
type Bit = Int
type aFuncofInterest = String => Int
 ```
## Anonymous functions

Anonymous functions are similar to string literals. They exist without a reference name.


```scala
(x:Int, y:Int) => x+y

```

```{admonition}
:class: tip
Type information can be ignored if the compiler can infer the type from the context.
```

```{admonition} Syntactic Sugar
:class: information
Syntactic sugar is syntax in a programming language that is designed to make things easier to read and express. It offers no functionality or expressiveness that the programming language couldn't.[^1]

Anonymous functions are syntactic sugar.

You can always rewrite `(x1:T1, x2:T2, ..., xn:Tn) => E` as follows

`{def f(x1:T1, x2:T2, ..., xn:Tn) => E; f}`

where `f` is an arbitrary fresh name not used in the programming. Note the return value of above block (see [block scope](../week1/week1_2.md)) is `f` which is the equivalent function defined.

```

## Currying

Currying is the technique of converting function with multiple parameters into sequence of functions that takes a single parameter.[^2]

$x = f(a,b,c)$ can be written as

$$
h = g(a)
i = h(b)
x = i(c)
$$

Scala has special syntax for functions with multiple arguments. We can write

```scala
def sum(x: Int , y: Int): Int = x+y
def sumCurry(x: Int)(y:Int): Int = x+y
```

both are equivalent. Why would you choose one over another? We can assign the partial function to another reference.

```scala
val x:(Int => Int) = sumCurry(2)(_) //return a lambda function whose value is 2 + y
x(3) //returns 5
```

In Scala, we have a method for currying a function with multiple arguments `.curried`
```scala
val sumCurried: (Int => (Int => Int)) = (sum  _).curried
```
--------------------------------------------------------------------------------

Functions with multiple parameter lists are syntactic sugar. In Scala, a definition of

$$\text{def } f(args_1)(args_2)..(args_n) = E;$$

equivalent to

$$\text{def } f(args_1)(args_2)..(args_{n-1}) = \{ \text{def } f(args_n) = E; g \}$$

or

$$\text{def } f(args_1)(args_2)..(args_{n-1}) = \{ args_n \Longrightarrow E \}$$


By repeating above process $n$ times we have

$$ \text{def } f = (args_1 \Longrightarrow (args_2 \Longrightarrow \cdots (args_n \Longrightarrow E))) $$,

this type of function definition and application is called _currying_.

We know that in Scala every function has a value, then guess the type of the function below.

```scala
def sum(f: Int => Int)(a: Int, b: Int):Int

```
`sum` take a function of type `Int => Int` return another function  of type `(Int, Int) => Int`.
So type of sum `(Int =>Int) => ((Int, Int) => Int)`.

Note that functional type associate to right. `Int => Int => Int` equivalent to `Int => (Int => Int)`.

--------------------------------------------------------------------------------

[^1]: https://en.wikipedia.org/wiki/Syntactic_sugar
[^2]: https://en.wikipedia.org/wiki/Currying
