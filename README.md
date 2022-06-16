# sparrowhawk
A simple property testing framework in Scala

# Ideas
 * Typeclasses for generators
 * Typeclasses for shrink
 * `implicit` configuration for key test parameters
   * random seed
   * minimum invocations
   * maximum invocations
 * Print random seed by default on test failures
 * Print summary of initial failure before shrinking
 * `cats` typeclasses for generators and shink

## Typeclasses for generators
```scala
trait Gen[A] {
  // todo: use reader for random?
  def summon[A](rand: scala.util.Random): A
}
```

## Typeclass for shrinking
```scala
trait Shrink[A: Gen] {
  def smaller: Option[Gen[A]]
}
```

## Configuration for test parameters
```scala
final class Config(
  randomSeed: Long,
  minimumInvocations: Int,
  maximumInvocations: Int
  // maxFailuresAllowed: Int - for flaky tests?
) {
  // TODO: figure out scope of Random - it is mutable so it needs to live somewhere where initializing the random seed 
  //       results in repeatable tests
  private val rand: Random = Random(randomSeed)
}

forAll[A: Gen](f: A => Unit)(implicit conf: Config) = {
  
  Either.catchNonFatal(f(Gen[A].summon(conf.rand))
}

forAllS[A: Gen: Shrink](f: A => Unit)(implicit conf: Config) = {
}
```
