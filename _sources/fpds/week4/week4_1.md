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

```scala
if (1 == 0) 3 else iterate(1 - 1, square, square(3))
iterate(0, square, square(3)) if (1 == 0) 3 // first way
else iterate(1 - 1, square, 3 * 3) //another way to rewrite
```

Both paths would give the same result. This property is called confluence (Church-Russev Theorem).

Let's introduce state in the objects. States can change over the course of time. An object has a state, if it's behavior is influenced by the history.

Every form of state is constructed from variable. In Scala, we use the keyword `var` to create variable. In practice object with state are represented as object with variables as members.

Here is an example of bank account:

```scala
class BankAccount {
private var balance = 0
def deposit(amount: Int): Unit = {
if (amount > 0) balance = balance + amount
}
def withdraw(amount: Int): Int =
if (0 < amount && amount <= balance) {
balance = balance - amount
balance
} else throw new Error(”insufficient funds”)
}
```
