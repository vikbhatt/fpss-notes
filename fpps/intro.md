Introduction
==========================================


The objective of course is to learn functional programming from basic principles using Scala.  Functional programming  is quite different from imperative programming paradigm (e.g., C, Java).

Scala provides support for both functional and object oriented programming. Scala has strong static type and inferred system. Scala is interoperable with Java and vice versa, and compiles to Java bytecode, so the executable runs on Java Virtual Machine.[^1]

The name Scala is portmanteau[^3] of **scalable** and **language**. Scalability is not necessarily defined as a generic example of high traffic sever with 100000 requests per second or highly available microservices, but Scala can also (See [Play!](https://www.playframework.com/)) be used to achieve it.[^2]

Scala is scalable in the sense:

- It is suitable for scripting purposes, as it is concise and easy because of functional style and type inference. Scala interactive shell (REPL -- READ, EVALUATE, PRINT LOOP) would be great place to start.
- It can be readily used to develop applications because of static type checking and interoperability with Java (hence huge eco system of libraries and community support available to the programmer.)
- It can be used to develop deploy enterprise level applications, microservices, web servers (Akka Actors concurrency model) and all the cool stuff.
- It can scale in terms of expressiveness and flexibility of programming models. We want to avoid scaling up data structures word by word (here word means word size of specific processor) but rather conceptually think in terms of theory of collections and their methods. Read [here.](./week1_1.md)


[^1]: https://en.wikipedia.org/wiki/Scala_(programming_language)
[^2]: https://softwareengineering.stackexchange.com/questions/130335/why-is-scala-more-scalable-than-other-languages
[^3]: a word blending the sounds and combining the meanings of two others, for example motel or brunch.
