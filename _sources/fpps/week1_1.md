Imperative vs Functional Programming
===========================================


## Imperative Programming Paradigm

Our familiar imperative programming paradigm (C, Java etc.) allows:
- assignment of values
- mutation of variables
- use control structures (e.g., for loop, if-else, break)


Imperative programming can be modeled as sequence of instructions passing to CPU in a [Von Neumann architecture](https://en.wikipedia.org/wiki/Von_Neumann_architecture). In layman terms Von Neumann architecture contains memory and CPU connected by bus. Von Neumann bottleneck refers to the fact that CPU and memory connected by shared bus, we would obtain limited throughput for our CPU, because bus can only read one word at a time but CPU is much more faster.


 John Backus(inventor of FORTRAN) spoke about the bottleneck in his famous 1977 ACM Turing award lecture[^1]

 > Surely there must be a less primitive way of making big changes in the store than by pushing vast numbers of words back and forth through the von Neumann bottleneck. Not only is this tube a literal bottleneck for the data traffic of a problem, but, more importantly, it is an intellectual bottleneck that has kept us tied to word-at-a-time thinking instead of encouraging us to think in terms of the larger conceptual units of the task at hand. Thus programming is basically planning and detailing the enormous traffic of words through the von Neumann bottleneck, and much of that traffic concerns not significant data itself, but where to find it.

John Backus argued that imperative programming is limited by Von Neumann bottleneck because we conceptualize programs word by word. We would like to reason in larger structures and high level abstractions (collections, string, polynomials, shapes, documents etc.). He proposed functional programming as solution.

-----------------------------------

In order to be thorough about data structures, we define theory. Theory consists of
- one or more data types
- operations on these data types
- laws that describe relationship between the values and operations

Too often theory doesn't define mutation. Let's take a look at few examples:

### Polynomials
In theory of polynomials we define sum of two polynomials as

$$
(ax + b)+(px + q) = (a+p)x + (b+q)
$$



In imperative programming paradigm we can write something similar to below (in Java). We can assign coefficient a value without changing the object. **But the theory doesn't define an operator to change a coefficient while keeping the polynomial same.**

```java
class Polynomial{
  double[] coefficients;
}
Polynomial p = new Polynomial(new double[]{1,2,3});
p.coefficients[0] = 2;
```


### Strings
Theory of strings define a concatenation operator $++$ which is associative.

$$(a++b)++c =  a ++ (b++c)$$

**But it doesn't define operator to change an element while keeping the sequence same.**

```{note}
Imperative programming allows us to perform mutation operation on structures, which is not defined by the theory. Mutation can destroy useful laws in the theories.
```

```{note}
In functional programming paradigm we focus on defining theories for operators expressed as functions, avoid mutations and have powerful ways to abstract and compose functions.
```

-------------------------------------------

## Functional Programming Paradigm

In a strict sense functional programming means:
- immutability
- no assignments
- no for loops (instead recursion or map)
- no imperative control structures

In wider sense:
- functions are first class citizens
- function as values, return parameters, and composable methods to construct functions

```{note}
We don't need a functional programming language(e.g., Haskell, Scala, StandardML, Lisp, XSLT) to work in functional programming paradigm. You might as well work with well known (C, C++, Java) languages to get it done. But working in functional programming language makes it a lot easier.
```

[^1]: https://en.wikipedia.org/wiki/Von_Neumann_architecture#Design_limitations
