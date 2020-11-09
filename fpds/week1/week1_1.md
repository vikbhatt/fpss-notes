# Recap Functions and Pattern Matching

case class is preferred way to define complex data. Let's look at possible domain we can express with functions and pattern matching is Java Script Object Notation (JSON).

See below example of JSON object from Wikipedia.

```json
{
  "firstName": "John",
  "lastName": "Smith",
  "isAlive": true,
  "age": 27,
  "address": {
    "streetAddress": "21 2nd Street",
    "city": "New York",
    "state": "NY",
    "postalCode": "10021-3100"
  },
  "phoneNumbers": [
    {
      "type": "home",
      "number": "212 555-1234"
    },
    {
      "type": "office",
      "number": "646 555-4567"
    }
  ],
  "children": [],
  "spouse": null
}
```

How would we represent JSON in Scala? A most straightforward way would be to

```scala
abstract class JSON
case class JSeq (elems: List[JSON]) extends JSON
case class JObj (bindings: Map[String, JSON]) extends JSON
case class JNum (num: Double) extends JSON
case class JStr (str: String) extends JSON
case class JBool(b: Boolean) extends JSON
case object JNull extends JSON
```

How do we write a function which prints the JSON string?

```scala
def show(json: JSON): String = json match {
case JSeq(elems) =>
"[" + (elems map show mkString ", ") + "]"

case JObj(bindings) =>
val assocs = bindings map {

case (key, value) => "\"" + key + "\": " + show(value)
}
"{" + (assocs mkString ", ") + "}"

case JNum(num) => num.toString

case JStr(str) => '\"' + str + '\"'

case JBool(b) => b.toString

case JNull => "null"
}
```

Let's take a deeper look at the `case` passed into map combinator of `assocs`. 

What is the type of 

```scala
{case (key, value) =>   key + ":" + show(value) }
```
Taken by itself, the expression is not typable. We need to prescribe expected type. The expected type on the map in above code for `assocs` is `JBinding => String` where `type JBinding = (String, JSON)`

In scala, every concrete type is the type of some class or trait. Functions are no exception.
`JBinding => String` is shorthand for `scala.Function1[JBinding, String]`.

Here is an outline of trait `Functon1`:

```scala
trait Function[-A, +R]{
  def apply(x:A): R
}
```

The pattern matching block 
```scala
{case (key, value) =>   key + ":" + show(value) }
```
expands to the `Function1` instance

```scala
new Function1[JBinding, String]{
  def apply(x: JBinding) = x match {
    {case (key, value) =>   key + ":" + show(value) }
  }
}
```

Since functions are traits we can subclass them.
For instance maps are functions from keys to values.

```scala
trait Map[Key, Value] extends (Key => Value)
```

Sequences are functions from `Int` indices to values.

```scala
trait Seq[Elem] extends (Int => Elem)
```

Thus, we can write `elems(i)` for sequences indexing.

## Partial Functions

Consider a function 
```scala
def f(String => String) = { case "Ping" => "Pong"}
```

We have seen a pattern matching block can be given type `Function1`. But what would happen if such function is not defined at a point?

```scala
f("Ping") \\"Pong"
f("abc") \\ error
```

Can we query wether a function is defined at certain point? The answer is `PartialFunction`.

The trait for `PartialFunction` is defined as follows.
```scala
trait PartialFunction[-A, +R] extends Function1[-A, +R] {
def apply(x: A): R
def isDefinedAt(x: A): Boolean
}
```

So we write 

```scala
valf f: PartialFunction[String, String] = {case "Ping" => "Pong"}
f.isDefinedAt("Ping") \\true
f.isDefinedAt("abc") \\false
```

If the expected type is a PartialFunction, the Scala compiler will expand
```scala
{ case "ping" => "pong" }
```
as follows:

```scala
new PartialFunction[String, String] {
def apply(x: String) = x match {
case "ping" => "pong"
}
def isDefinedAt(x: String) = x match {
case "ping" => true
case _ => false
}
}
```

-----------------------------------------------------------------------------------------------------------------

```{admonition} PartialFunctions  
 `isDefinedAt` guarantee that partial function gives you only applies to the outermost matching block, it's not a guarantee that if a function is defined at an argument that this function definitely will not throw a match error when it is run because the match error could be thrown by a completely different function that gets called from the function in a chain of cause.
````