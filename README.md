Nyaya
=====

Nyaya is a library to validate propositions/properties about your data…
* …in tests using random data.
* …at non-prod runtime using real data.

Nyaya is
* Built for Scala.JS and standard Scala.
* Includes tools for uniqueness validation, and generating *compliant* random data.

It was initially created because ScalaCheck wasn't available on Scala.JS.
Rather than being a port or clone, it's evolved into its own unique solution building on good and bad experiences using ScalaCheck, and my own needs, and values.


Status
======
It's been used for months and months behind closed doors; I consider it useful and stable.

As for the API itself, I don't know how little or large future change will be.

For example, on a separate branch I have shrinking working that is way more effective than
my experience with ScalaCheck's, but haven't decided how or if I'll merge it. It could
be quite distruptive to the API. (?)


Features
========

### Composition with Detailed Reporting

* Propositions are composable using all operations available in first-order logic.
* When propositions fail, care is taken to provide enough information to comprehend why, quickly.
  This is a goal even when the proposition is the composite of many underlying propositions of which, only a few failed.
* Individual propositions can written to provide specialised info about why they fail, when they fail.

##### Example
This property checks that each number in a list is even, divisible by 3 and divisible by 5:
```scala
val even = Prop.test[Int]("even", _ % 2 == 0)
val div3 = Prop.test[Int]("div3", _ % 3 == 0)
val div5 = Prop.test[Int]("div5", _ % 5 == 0)
val prop = (even & mod3 & mod5).forallF[List] rename "Example"
```
This is a sample failure report:
```
Property [example] failed.

Input: [List(15, 30, 60, 25)].

Root causes:
  2 failed axioms, 2 causes of failure.
  ├─ even
  │  ├─ Invalid input [15]
  │  └─ Invalid input [25]
  └─ div3
     └─ Invalid input [25]

Failure tree:
  Example
  └─ (even ∧ div3 ∧ div5)
     ├─ even
     └─ div3
```


### Runtime Assertion

In cases that we can't encode constraints using types, we generally write property-based tests.
Just as the compiler cannot prove these constraints, nor can it prove that we've written data
generators that will cover all cases. Thus as an additional safety check I like to validate
the assumed laws at runtime using real data. I (may) also want these checks omitted from
production code so as not to impact performance. Nyaya covers these scenarios.


* Use the same code to validate real data at runtime, and test with random data in unit tests.
* Runtime validation can be removed by specifying a scalac flag `-Xelide-below`.

##### Example
```scala
object MyProps {
  val foo: Prop[Foo] = ...
}

case class Foo(a: Int, b: String) {
  import shipreq.prop._
  this assertSatisfies MyProps.foo
}
```

### random testing

* When building random data generators, uses combinators in NICTA/rng which I find much better.

### proving



- special features

* uniqueness: Validate uniqueness within data. Easily generate data with complex uniquess constraints.

* cycle detection: Easily detect cycles in recursive data.


There are a number of features that make Nyaya special:


* optionally not at runtime in production.






What?
=====

### nyaya-core

* Create propositions (≈ properties).
* Assert them in dev (elided in prod).
* Get a detailed report about what sub-propositions failed and why.
* Proposition composition.

### nyaya-test

* Test propositions with random data (like ScalaCheck).
  * No implicits.
  * Better combinators.
* Prove propositions.
* Testing and generation of data with uniqueness constraints.


### Licence

Copyright (C) 2014-2015 David Barri

This library is free software; you can redistribute it and/or
modify it under the terms of the GNU Lesser General Public
License as published by the Free Software Foundation; either
version 2.1 of the License, or (at your option) any later version.

This library is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
Lesser General Public License for more details.

