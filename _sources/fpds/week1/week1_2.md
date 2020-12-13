# Recap: Collections

All collection types share general set of methods.

- map
- flatMap
- filter

and also

- foldLeft
- foldRight


## Idealized Implementation

```scala
abstract class List[+T]{

    def map[U](f: T => U):List[U] ={
        case x::xs => f(x)::xs.map(f)
        case Nil => Nil
    }
    def flatMap[U](f: T => List[U]): List[U]={
        case x::xs => f(x) ++ xs.flatMap(f)
        case Nil => Nil
    }
    def filter[U](p: T => Boolean): List[T] = {
        case x::xs => if(p(x)) x::xs.filter(p) else xs.filter(p)
        case Nil => Nil
    }
}
```

In practice, the type and implementation of these methods are different in order to

- make them apply to arbitary collections, not just lists
- make them tail recursive on the lists


## For-Expression

For expressions simplify the combinations of `map`, `flatMap`, and `filter`. Scala compiler translates a `for` expression in terms of `map`, `flatMap`, and `filter`. Let's see examples.

- A simple for expression
  ```scala
  for(x <- e1) yield e2
  ```
  is translated to

  ```scala
    e1.map(x => e2)
  ```

- A for expression
   ```scala
   for(x <- e1 if f; s) yield e2
   ```
   where `f` is a filter, `s` is sequence of (potentially empty) generators with filters is translated to

   ```scala
    for (x <- e1.withFilter(x => f); s) yield e2
   ```
   and translation continues with the new expression.

   `withFilter` is a lazy variant of the `fitler` where it doesn't produce intermediate outputs, but instead filters the following `map` of `filterMap` functions.

- A for expression
    ```scala
    for(x <- e1; y <- e2; s) yield e3
    ```
    where `s` is sequence of generators (potentially empty) translates to

    ```scala
    e1.flatMap(x => for (y <- e2; s) yield e3)
    ```
    and translation continues with new expression.

The left hand side of generator may be a pattern. An example would be

```scala
val data: List[JSON] = ...
for {
JObj(bindings) <- data
JSeq(phones) = bindings("phoneNumbers")
JObj(phone) <- phones
JStr(digits) = phone("number")
if digits startsWith "212"
} yield (bindings("firstName"), bindings("lastName"))
```

If pat is a pattern with a single variable x, we translate
`pat <- expr` to:

```scala
x <- expr withFilter {
case pat => true
case _ => false
} map {
case pat => x
}
```

## For-Expression Queries

`for` expressions look like embedded query languages for database. Suppose we have a database of books reprsented a list of books.

`case class book(title: String, authors: List[String])`

Let's create a mini database for our demonstration.


```scala

val books: List[Book] = List(
Book(title = "Structure and Interpretation of Computer Programs",
authors = List("Abelson, Harald", "Sussman, Gerald J.")),
Book(title = "Introduction to Functional Programming",
authors = List("Bird, Richard", "Wadler, Phil")),
Book(title = "Effective Java",
authors = List("Bloch, Joshua")),
Book(title = "Java Puzzlers",
authors = List("Bloch, Joshua", "Gafter, Neal")),
Book(title = "Programming in Scala",
authors = List("Odersky, Martin", "Spoon, Lex", "Venners, Bill")))

```

-----------------------------------------------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Ex1`

How do you  find the titles of books whose author’s name is "Bird"?

Use `for` loop expressions
````

````{dropdown} Ex1 solution
```scala
for (b <- books; a <- b.authors if a startsWith "Bird,")
yield b.title
)
```
````

-----------------------------------------------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Ex2`

How do you find all the books which have the word “Program” in the title?

Use `for` loop expressions
````

````{dropdown} Ex2 solution
```scala
for (b <- books; if b.title indexOf "Program" >= 0)
yield b.title
)
```
````
-----------------------------------------------------------------------------------------------------------

````{panels}
:column: col-lg-12 p-2

{badge}`Ex3`

Find the name of all authors who have written at least two books present in the database.


Use `for` loop expressions
````

````{dropdown} Ex3 solution
```scala
for (b1 <- books
     b2 <- books
     if b1!=b2
     a1 <- b1.authors
     a2 <- b2.authors
     if a1==a2) yield a1
```
But we get solutions twice because the same pair swapped in `b1` and `b2`.  We can avoid it by asking title of first book smaller than second.
```scala
for (b1 <- books
     b2 <- books
     if b1.title<b2.title
     a1 <- b1.authors
     a2 <- b2.authors
     if a1==a2) yield a1
```
````

-----------------------------------------------------------------------------------------------------------

The translation of `for`-expression is not limited to lists, or sequences or even collections.
It is based solely on presence of methods `map`, `flatMap`, and `withFilter`. This lets you use `for` your own syntax for your own types (arrays, iterators, databases, XML, parsers etc.). We must only define `map`, `flatMap` and `withFilter` functions.
