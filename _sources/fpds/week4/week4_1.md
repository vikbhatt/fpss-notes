# Functions and State


So far our programs are side-effect free. That means all program that terminate, any sequence of actions would give the same result. This was reflected also on the substitution model of the computation.
We are going to broaden our notion of functions, to work with mutable state.

In substitution model, the programs can be evaluated by rewriting the program text
The most important rewrite rule covers function applications:

```scala
def f(x1, ..., xn) = B; ... f(v1, ..., vn)
→
def f(x1, ..., xn) = B; ... [v1/x1, ..., vn/xn] B
```

In the above example, the call for `f` with actual parameter `f(v1, ..., vn)` can be evaluated by substituting all the occurrences of formal parameters `x1, x2, ..., xn` with `v1, v2, ..., vn`.

Let's see it in action in an example.

```scala
def iterate(n: Int, f: Int => Int, x: Int) =
if (n == 0) x else iterate(n-1, f, f(x))
def square(x: Int) = x * x
iterate(1, square, 3)
```

```scala
→ if (1 == 0) 3 else iterate(1-1, square, square(3))
→ iterate(0, square, square(3))
→ iterate(0, square, 3 * 3)
→ iterate(0, square, 9)
→ if (0 == 0) 9 else iterate(0-1, square, square(9)) → 9
```

Rewriting can be done anywhere in the term and all rewriting which terminate leads to same result. This is a important result of $\lambda-$ calculus, theory behind functional programming.