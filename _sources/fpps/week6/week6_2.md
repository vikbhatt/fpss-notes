# Combinatorial Search and For-Expressions

Given a positive integer $n$ find all pairs of positive integers $i$ and $j$ with $i \leq j < i < n $ such that $i+j$ is prime.

## Algorithm 

A natural way to do this is to:

 * Generate the sequence of pair of integers $(i,j)$ such that $i \leq j < i < n $.
 * Filter the pairs for which $i+j$ is prime.