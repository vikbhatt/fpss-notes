# Functional Random Generators

Are `for`-expressions tied to only collections?

No! All that required is some interpretation of `map`, `flatMap` and `withFilter`. There are many domains outside the collections that can afford such interpretation.

Functional random generators is once such good example.

Is there a systemic way to generate random values for domains such as 
    - booleans, string, tree, list, pair, set and tuple

We know that Java random module can be used to generate integers.

```scala
import java.util.Random
val random = new Random()
random.nextInt()
```

Let's define a trait `Generator[T]` that generates random values of type `T` 

```scala
trait Generator[+T]{
    def generate: T
}
```

Let's look at one instance of `Generator`

```scala
val integers = new Generator[Int]{
    import java.util.Random
    val random = new Random()
    def generate: Int = random.nextInt()
}
```

Using integers generator, we can define `Boolean` generator.

```scala
val boolean = new Generator[Boolean]{
    def generate: Int = integers.generate > 0
}
```

How about pairs of integers?

```scala
val boolean = new Generator[(Int, Int)]{
    def generate: Int = (integers.generate, integers.generate)
}
```

We would like to avoid the `Generator` boilerplate and write using `for` expression generators.

```scala
val booleans = for(x <- integers) yield x > 0

def pairs[T, U](t: Generator[T], u: Generator[U]) = for{
    x <- t
    y <- u
} yield (x,y)

```

Above definition would translate in terms of `map`, `flatMap` and `withFilter` methods.

```scala

val booleans = integers map (x => x > 0)
def pairs[T, U](t: Generator[T], u: Generator[U]) =
t flatMap (x => u map (y => (x, y)))
```

So we need implementation of `map` and `flatMap` for that!.

Here is a more convenient version of `Generator` which can be used along with `for` expression.

```scala
trait Generator[+T] {
    self =>  //an alias for this
    def generate: T
    def map[S](f: T => S): Generator[S] = new Generator[S]{
        def generate = f(self.generate)
    }
    def flatMap[S](f: T => Generator[S]): Generator[S] = new Generator[S]{
        def generate = f(self.generate).generate
    }
}
```

What does this definition resolve to?

```scala
val booleans = for (x <- integers) yield x > 0

```

```scala
val booleans = integers map { x => x > 0 }
val booleans = new Generator[Boolean] {
def generate = (x: Int => x > 0)(integers.generate)
}
val booleans = new Generator[Boolean] {
def generate = integers.generate > 0
}
```

Looks good! What can we say about the following definition of `pairs`?

```scala
def pairs[T, U](t: Generator[T], u: Generator[U]) = for {
x <- t
y <- u
} yield (x, y)
```

```scala
def pairs[T, U](t: Generator[T], u: Generator[U]) = t flatMap {
x => u map { y => (x, y) } }

def pairs[T, U](t: Generator[T], u: Generator[U]) = t flatMap {
x => new Generator[(T, U)] { def generate = (x, u.generate) } }

def pairs[T, U](t: Generator[T], u: Generator[U]) = new Generator[(T, U)] {
def generate = (new Generator[(T, U)] {
def generate = (t.generate, u.generate)
}).generate }

def pairs[T, U](t: Generator[T], u: Generator[U]) = new Generator[(T, U)] {
def generate = (t.generate, u.generate)
}
```

-----------------------------------------------------------------------------------------

## Generator Instances

Let's look at few instances of `Generator` which act like building blocks.

```scala
def single[T](x: T): Generator[T] = new Generator[T] {
def generate = x
}
```

`single` instance always returns the same value given by the parameter `x`. Not so random at all!

```scala
def choose(lo: Int, hi: Int): Generator[Int] =
for (x <- integers) yield lo + x % (hi - lo)
```

`choose` instance return random integers between the values `lo` and `hi`.

```scala
def oneOf[T](xs: T*): Generator[T] =
for (idx <- choose(0, xs.length)) yield xs(idx)

oneOf("red", "green", "blue")
```

`oneOf` instance pick arbitrary value from list of choices.

With these blocks in place, let's write random generators for more structured units.

Here is a `list` generator.

```scala

def lists: Generator[List[Int]] = for {
isEmpty <- booleans
list <- if (isEmpty) emptyLists else nonEmptyLists
} yield list

def emptyLists = single(Nil)

def nonEmptyLists = for {
head <- integers
tail <- lists
} yield head :: tail
```

`lists` returns a `Generator` which yields either an empty list or non-empty list. `nonEmptyLists` randomly generates integer and list, and append the integer to the head of the list.

-----------------------------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Ex1`

Can you implement a random `Tree` generator?

```scala
trait Tree
case class Leaf(x: Int) extends Tree
case class Inner(left: Tree, right: Tree) extends Tree
```
````

````{dropdown} Ex1 solution
```scala

val integers = new Generator[Int]{
    def generate: Int = scala.util.Random.nextInt()
}

val booleans = integers.map(x => x>0)

def leafs: Generator[Leaf] = for { x <- integers} yield Leaf(x)

def inners: Generator[Inner] = for{
    l <- trees 
    r <- trees
    } yield Inner(l, r)

def trees: Generator[Tree] = for{ 
    isLeaf <- booleans 
    tree <- if(isLeaf) leafs else inners
}yield tree
```
````

-----------------------------------------------------------------------------------------

## Applications

Post condition is the property of expected result of a program. We can verify, if a program satisfies post condition by generating random inputs.

Using generators, we can write a random test function:

```scala
def test[T](g: Generator[T], numTimes: Int = 100)
(test: T => Boolean): Unit = {
for (i <- 0 until numTimes) {
val value = g.generate
assert(test(value), ”test failed for ”+value)
}
println(”passed ”+numTimes+” tests”)
}
```

`ScalaCheck` is a tool, that can be used to test post conditions of programs in Scala. Instead of writing tests, we write properties, we assume to hold.
 `ScalaCheck` can come up with random value generators (even for structured types) and run the sanity checks. For example 

 ```scala
forAll{

    (l1: List[Int], l2: List[Int]) => l1.size + l2.size == (l1 ++ l2).size
}
 ```
 
 `ScalaCheck` is smart enough to find smaller examples in case the test example failed.