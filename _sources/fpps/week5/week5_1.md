# Lists

There are two important differences between lists and arrays.

* Lists are immutable.
* Lists are recursive, arrays are flat.

All lists are constructed from

* the empty list `Nil`
* the construction operation `::` (cons). The operation `x::xs` gives a new list with first element `x`, followed  by  the elements of `xs`.

Lists are homogenous. All elements must have same type. The type of list with elements of type `T` written as `List[T]`.

## Right Associativity

**Convention**: All operators ending with `:` associate to the right.

`A::B::C` is interpreted as `A::(B::C)`

Operators end in the colon are seen as methods calls of the right operand.

```scala
val nums = 1::2::3::4::Nil
```

interpreted as

```scala
Nil.::(4).::(3)::.(2).::(1)
```

All operations on list is expressed in terms of 

* `head` first element of the list 
* `tail` a list composed of all elements except first
* `isEmpty` `true` if list is empty else  `false`

It is also possible to decompose lists with pattern matching.

* `Nil` - the `Nil` constant
* `p:ps` - a pattern matching a list with head `p` and a tail matching `ps`.
* `List(p1,p2,...,pn)` same as `p1::p2::...::pn`.

Examples

`1::2::xs` - List that starts with 1 and then 2.
`x :: Nil` - List of length 1
`List(x)` - Same as `x::Nil`
`List()` - Same as empty list
`List(2::xs)` - A list contains only another list that starts with 2.

---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Consider the pattern 
```scala
x::y:List(xs,ys)::zs
```
What is the condition that describes most accurately the length L of the list it matches?
````

````{dropdown} Solution
`zs` might be `Nil`. `ys` might be `Nil`. `L>=3`
````

---

---

````{panels}
:column: col-lg-12 p-2

{badge}`Exercise`

Complete the definition of insertion sort  method `insert`. What is the worst case complexity with respect to input length of list `N`?

```scala
def isort(xs: List[Int]): List[Int] = xs match {
case List() => List()
case y :: ys => insert(y, isort(ys))
}
```
````

````{dropdown} Solution
```scala
def insert(x: Int, xs: List[Int]): List[Int] = xs match {
    case List() => List(x)
    case y:ys() => if(x<=y>) x::xs else y::insert(x, ys)
}
```

Looking alone at `insert` the worst case element `x` greater than all elements we have N calls. We have to make a call for `insert` from `isort` for every element so the worst case complexity is proportional to  `N*N`.
````
---


## More Functions on List

### Sublists and elements access

| Property        | Definition           |
| ------------- |:-------------:|
| xs.length     | The number of elements of xs.|
| xs.last    | The list's last element, Exception if empty.      |
| xs.init | A list of all elements of xs except the last one, exception if xs is empty.|
| xs take n | A list containing the first n elements of xs or xs if it is shorter than n|  
| xs drop n| The rest of collection after taking n elements|
| xs(n)    | The element of xs at index n|

### Creating new lists

| Method        | Definition           |
| ------------- |:-------------:|
| xs++ys     | A list containing all elements of xs followed by all elements of ys.|
|xs.reverse | List containing the elements of xs in reverse order.|
|xs updated (n,x)| A list containing the same elements as xs except at index n where it contains x |