# Combinatorial Search and For-Expressions

Given a positive integer $n$ find all pairs of positive integers $i$ and $j$ with $i \leq j < i < n $ such that $i+j$ is prime.

## Algorithm 

A natural way to do this is to:

 * Generate the sequence of pair of integers $(i,j)$ such that $i \leq j < i < n $.
 * Filter the pairs for which $i+j$ is prime.

One way to generate the natural sequence of pairs

* Generate all the integers between $1$ and $n$ (excluded).
* For each integer $i$, we generate the list of pairs $(i,1), \dots, (i, i-1)$

It can be achieved by combining `map` and `until`

```scala
(1 until n) map (i => (1 until i) map (j => (i, j)))
```

