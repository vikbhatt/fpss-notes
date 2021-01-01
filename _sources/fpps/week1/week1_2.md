# Expressions and Evaluation model
In Scala an expression can be:

-  An identifier such as `x`, `isGoodEnough`,
-  A literal, like `0`, `1.0`, `
  "abc"`,
-  A function application, like `sqrt(x)`,
-  An operator application, like `-x`, `y + x`,
- A selection, like `math.abs`,
- A conditional expression, like `if (x < 0) -x else x`,
- A block, like `{ val x = math.abs(y) ; x * 2 }`
- An anonymous function, like `x => x + 1`.


Definitions can be:
- A function definition, like `def square(x: Int) = x * x`
- A value definition, like `val y = square(2)`

Definitions in Scala are given by `def` keyword. We write type of the variable followed by colon.
(Follow along by typing the expression in Scala REPL shell. Get it from [here](https://www.scala-lang.org/download/))

```scala
def x: Int = 1
```
or drop the type information and let Scala infer the type.

```scala
def pi = 3.1415
```

Definitions can also have type parameters. We can define

```scala
def square(x: Int): Int = x*x
def concat(x: String, y:String): String = x+y
```

Functions are evaluated as:
- Evaluate all the function arguments from left to right
- Replace function application by right hand side and at the same time
- Replace the formal parameters by actual arguments.

----------------------------------------------------------------------

## Substitution Model


- The scheme of evaluation is called Substitution model.
- The idea underlying in this model is all evaluation does is reduce an expression to value (as long as no side effects are present).
- Formalized by $\lambda-$calculus.

Not every expression reduces to value in finite steps. For example try this in your Scala interpreter.

```scala
def loop: Int = loop
```


The interpreter tries to reduce the function arguments to values before the function application. Function parameters can be passed in two ways:
- A call-by-value parameter, like `(x: Int)`,
- A call-by-name parameter, like `(y: => Double)`.

Both strategies reduce expressions to same values as long as the reduced expression consist of pure functions and both values terminate.

call-by-value
: evaluate arguments once and substitute the result.

In the below example both arguments `x` and `y` are evaluated and the values are substituted in the body of the function.

```scala
def multiply(x:Int, y:Int) = x*y
```

----------------------------------------------------------------
call-by-name
: doesn't evaluate argument unless it is used by the function's body.

```{note}
Scala normally uses call by value. But when the function parameters starts with :=> it uses call by name.
```

In the below example, `y` doesn't evaluate until it encounters (lazy evaluation) in the body.

```scala
def multiply(x: Int, y: => Int) = x*y
```

-------------------------------------------------

The same distinction applies to definitions.
- `def` form is called by name. The right hand side evaluated each time it's called.
- `val` form is called by value. The right hand side is evaluated at the point of definition itself. `val` forms are immutable.
Example:

```scala
def loop:Boolean = loop
def x = loop \\REPL returns because def is call by name.
val x = loop \\REPL stuck in loop because val is call by value.
```

```scala
val y: Int = 32
y = 32 // error: reassignment to val
```

Let's trace the evaluation calls for
```scala
def constOne(x:Int, y:=> Int) = 1
constOne(1+2, loop)
constOne(loop, 1+2)
```

First argument is called by value, and second argument called by name.

`constOne(1+2,loop) => constOne(3, loop) => 1`

`constOne(loop, 1+2)` never terminates, because when the interpreter tries to evaluate the first argument it never returns.

## Blocks in Scala

- Blocks are themselves expressions.
- A block is delimited by braces {}.
- It contains sequence of definitions or expressions.
- The last element of block is an expression, that defines the value of the block.
- You can use block expression to define the return value of function.

```scala
def foo(x:Int) = {
  val y = do_something()
  val z = do_something_with(y)
  z
}
```
return value of above function is z (expression evaluated).
