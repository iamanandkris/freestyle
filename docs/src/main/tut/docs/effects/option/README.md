---
layout: docs
title: Option
permalink: /docs/effects/option/
---

## OptionM

The option effect allows short circuiting of programs for optional values.
It includes two basic operations: `option` and `non`.

There needs to be implicit evidence of `cats.mtl.FunctorEmpty[M]` for any runtime `M[_]` used in its interpretation due to contraints placed by this effect. Short-circuiting with `none` does not mean that you'll end up with a
`None` value at some point. The final value in case of short-circuiting is determined by the `FunctorEmpty` instance for
your target runtime `M[_]`.

### option

`option` allows a value of type `Option[_]` to be lifted into the context of `FreeS`. If a `None` is found the program will short circuit.


```tut:book
import freestyle._
import freestyle.implicits._
import freestyle.effects.option._
import freestyle.effects.option.implicits._
import cats.implicits._
import cats.mtl.implicits._

def programNone[F[_]: OptionM] =
  for {
    a <- FreeS.pure(1)
    b <- OptionM[F].option[Int](None)
    c <- FreeS.pure(1)
  } yield a + b + c

programNone[OptionM.Op].interpret[Option]
```

If a `Some(_)` is found, the value is extracted and lifted into the context and the programs resumes
normally.

```tut:book
def programSome[F[_]: OptionM] =
  for {
    a <- FreeS.pure(1)
    b <- OptionM[F].option(Some(1))
    c <- FreeS.pure(1)
  } yield a + b + c

programSome[OptionM.Op].interpret[Option]
```

### none

`none` immediately short circuits the program without providing further information as to what the reason is. Handle with
care.

```tut:book
def programNone2[F[_]: OptionM] =
  for {
    a <- FreeS.pure(1)
    b <- OptionM[F].none[Int]
    c <- FreeS.pure(1)
  } yield a + b + c

programNone2[OptionM.Op].interpret[Option]
```
