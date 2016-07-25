---
layout: post
title: "Scala Underscore"
category: "Scala"
tags: [language,Java,scala]
---

Underscore `_` is a special charactors in scala, it is wildly used in many places with different meanings, here are the summary

#### Pattern Matching ####

In Scala, pattern matching is somewhat similar to java switch statement. But it is more powerful.

```
def doMatch(x: Int): String = x match {
    case 1 => "one"
    case 2 => "two"
    case _ => "anything other than one and two"
  }
```

`_` here acts like the wildcard (`default` in Java switch)

#### Existential types ####

```
def foo(l: List[Option[_]]) = ...
```

The `_` defined the nested type of `l`: any type is accepted.


#### Higher kinded type parameters ####

```
case class A[K[_],T](a: K[T])
``` 

The `_` here defined a template class A accept any template paramter which is a template type K with any inner template types

#### Ignored variables ####

```
val _ = 5
```

#### Default initialize ####

```
class A {
  var x : Any = _
}
```

#### Ignored parameters ####

```
List(1, 2, 3) foreach { _ => println("Ignored input") }
```

#### Wildcard imports ####

```
import java.io._
```

Import all the package/class under java.io

#### Hiding imports ####

```
import java.util.{ArrayList => _, _}
```

Import the all the names under `java.util` except the `ArrayList`

#### Joining letters to punctuation ####

```
def bang_!(x: Int) = 5
```

#### Assignment operators ####

```
def foo_=(x: Int) { ... }
```

#### Placeholder syntax ####

```
List(1, 2, 3) map (_ + 2)
```

#### Partially applied functions ####

```
List(1, 2, 3).foreach(println(_))
```

#### Parameter expandation ####

```
def f(args : String*) = {
  args.map { arg =>
    arg.capitalize
  }
}

val caped = f(Array("a", "b"):_*)
```