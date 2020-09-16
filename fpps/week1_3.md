# Tail Recursion

  If the last action of a call consists of calling another function (which may
  be the same), one stack frame is sufficient for both functions. Such calls are called tail calls.

  Tail calls are sub routines performed as last action of a procedure.


  If the tail call is same as the routine (which calls the tail call), then the routine is tail-recursive. [^1]


  Tail recursive functions are iterative process. You can say tail recursive functions are functional forms of iterative loop.


-------------------------------------------------------

## Tail Call Optimization[^1]  


- Tail calls can be implemented without addition of new stack frame.
- Most of the current stack frame is not necessary and can be replaced by the frame of tail call.
- The program can then jump to the called subroutine.
- Producing such code instead of standard sequence of call stack is called tail call optimization or tail call elimination.
- In functional programming language tail call elimination is implemented in the language standard.


(tail-rec-note)
```{important}
- A tail call doesn't have to appear lexically after all the statements in source code.
- Only requirement is calling function return immediately after the tail call, and returning the tail call result if any, since the calling function bypassed when optimization is performed.
```


- In Scala only directly recursive calls to the current function are optimized.
- We can require a function to be tail recursive by using `@tailrec` annotation.
- Throws an error when the implementation is not tail recursive.

----------------------------------------------------------------------------
Let's take a look at few examples.

```scala
def gcd(a: Int, b:Int):Int =
    if(b==0) 1 else gcd(b, a % b)
```

Above function can be tail call optimized because the last call of routine `gcd` returns immediately.
Note that we have two branches for if-condition but both returns immediately and hence `gcd` is tail recursive (even though lexically doesn't appear at the end of source code, see {ref}`tail-rec-note`)


```scala
def factorial(a: Int): Int = {
  if(a == 0) 1 else a*factorial(a-1)
}
```
`factorial` function is not tail recursive. Let's take a look trace of `factorial(4)`

\begin{align}
& factorial(4) \\
\Longrightarrow & 4*factorial(3) \\
\Longrightarrow & 4*3*factorial(2) \\  
\Longrightarrow & 4*3*2*factorial(1) \\
\Longrightarrow & 4*3*2*1*factorial(0) \\
\Longrightarrow & 4*3*2*1*1
\end{align}


The final action requires multiplication of all factors gathered from stack build up before the return of caller. Thus above version of not `factorial` tail recursive.

**Can we make it tail recursive?**

Yes we can.

```scala
def factorial(a: Int):Int = {

  def accumulator(acc:Int, n:Int):Int = {
    if (n == 0) acc
    else accumulator(acc*n, n-1)
  }
  accumulator(1, a)
}
```

Now stack frame for `factorial` can be replaced by `accumulator` routine (tail call optimization!),and the accumulator function is itself is tail recursive. So its essentially a loop and takes $\mathcal{O}(1)$ space.

Let's look at the stack trace of `factorial(4)`:


\begin{align}
& factorial(4)  \\
\Longrightarrow & accumulator(1,4) \\
\Longrightarrow & accumulator(1*4,3) \\
\Longrightarrow & accumulator(4*3,2) \\
\Longrightarrow & accumulator(12*2,1) \\
\Longrightarrow & accumulator(24*1,0) \\
\Longrightarrow & 24 \\
\end{align}


In the next stack the arguments are evaluated because Scala is generally call by value. (See [here](./week1_2.md))

Notice we haven't used imperative loop or any kind of mutations in the above example. A similar design pattern is ubiquitous in functional programming and well worth to know about it.



[^1]: https://en.wikipedia.org/wiki/Tail_call
