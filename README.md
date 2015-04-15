# Benchmarking JVM model classes

This is a quick study of the relative performance and size of the typical DTO methods generated by:

* Groovy 2.4.3 annotations
* Scala 2.11.6 case classes
* Kotlin 0.11.91.1 data classes
* Lombok and AutoValue annotation generated
* Eclipse and IntelliJ auto-generated

## Notes

* Caching `hashCode()` values for immutable classes is an important optimisation, that can't be inferred and needs to be manually implemented
*We also don't consider the quality of the `hashCode()` implementation. They may take the same time, but result in poor hash distribution
* Groovy 2.3.9 scored over 1000 ns/op on `equals()` with a similar error margin as below, prompting an upgrade to 2.4.3

# Running

To run locally:

* Benchmarks - `./gradlew jmh` (it should take about 30 minutes and the results will be in ./build/reports/jmh/human.txt)
* Memory - `./gradlew measureMemory`

# Benchmarks

    # JMH 1.8 (released 8 days ago)
    # VM invoker: /Library/Java/JavaVirtualMachines/jdk1.8.0_40.jdk/Contents/Home/jre/bin/java
    # VM options: -Dfile.encoding=UTF-8 -Duser.country=US -Duser.language=en -Duser.variant
    # Warmup: 15 iterations, 1 s each
    # Measurement: 30 iterations, 1 s each
    # Timeout: 10 min per iteration
    # Threads: 1 thread, will synchronize iterations
    # Benchmark mode: Average time, time/op

    Model Name: MacBook Pro
    Model Identifier: MacBookPro11,2
    Processor Name: Intel Core i7
    Processor Speed: 2.8 GHz
    Number of Processors: 1
    Total Number of Cores: 4
    L2 Cache (per Core): 256 KB
    L3 Cache: 6 MB
    Memory: 16 GB

## Object instantiation

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.create               intellij  avgt   30    25.473 ±  1.459  ns/op
    ModelBenchmark.create                eclipse  avgt   30    23.686 ±  0.732  ns/op
    ModelBenchmark.create                 groovy  avgt   30    28.310 ±  0.492  ns/op
    ModelBenchmark.create           staticgroovy  avgt   30    28.427 ±  0.652  ns/op
    ModelBenchmark.create                 lombok  avgt   30    22.485 ±  0.536  ns/op
    ModelBenchmark.create                  scala  avgt   30    26.332 ±  0.606  ns/op
    ModelBenchmark.create                 kotlin  avgt   30    23.372 ±  1.376  ns/op
    ModelBenchmark.create              autovalue  avgt   30    26.253 ±  1.604  ns/op

## equals()

Equals against the same model instance:

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.equalsIdentity       intellij  avgt   30     4.632 ±  0.351  ns/op
    ModelBenchmark.equalsIdentity        eclipse  avgt   30     2.655 ±  0.172  ns/op
    ModelBenchmark.equalsIdentity         groovy  avgt   30     6.764 ±  0.351  ns/op
    ModelBenchmark.equalsIdentity   staticgroovy  avgt   30     3.849 ±  0.084  ns/op
    ModelBenchmark.equalsIdentity         lombok  avgt   30     3.940 ±  0.122  ns/op
    ModelBenchmark.equalsIdentity          scala  avgt   30     2.386 ±  0.054  ns/op
    ModelBenchmark.equalsIdentity         kotlin  avgt   30     2.408 ±  0.057  ns/op
    ModelBenchmark.equalsIdentity      autovalue  avgt   30     2.471 ±  0.146  ns/op

Equals null:

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.equalsNull           intellij  avgt   30     3.760 ±  0.053  ns/op
    ModelBenchmark.equalsNull            eclipse  avgt   30     2.478 ±  0.100  ns/op
    ModelBenchmark.equalsNull             groovy  avgt   30     3.760 ±  0.060  ns/op
    ModelBenchmark.equalsNull       staticgroovy  avgt   30     3.710 ±  0.057  ns/op
    ModelBenchmark.equalsNull             lombok  avgt   30     3.731 ±  0.043  ns/op
    ModelBenchmark.equalsNull              scala  avgt   30     2.294 ±  0.018  ns/op
    ModelBenchmark.equalsNull             kotlin  avgt   30     2.274 ±  0.032  ns/op
    ModelBenchmark.equalsNull          autovalue  avgt   30     2.271 ±  0.029  ns/op

Equals against an instance with a different type:

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.equalsOtherType      intellij  avgt   30     4.202 ±  0.056  ns/op
    ModelBenchmark.equalsOtherType       eclipse  avgt   30     3.145 ±  0.039  ns/op
    ModelBenchmark.equalsOtherType        groovy  avgt   30     6.374 ±  0.071  ns/op
    ModelBenchmark.equalsOtherType  staticgroovy  avgt   30     4.406 ±  0.207  ns/op
    ModelBenchmark.equalsOtherType        lombok  avgt   30     4.398 ±  0.043  ns/op
    ModelBenchmark.equalsOtherType         scala  avgt   30     3.175 ±  0.022  ns/op
    ModelBenchmark.equalsOtherType        kotlin  avgt   30     3.200 ±  0.052  ns/op
    ModelBenchmark.equalsOtherType     autovalue  avgt   30     3.363 ±  0.076  ns/op

Equals against an equal, but different instance:

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.equalsWorstCase      intellij  avgt   30    23.240 ±  0.367  ns/op
    ModelBenchmark.equalsWorstCase       eclipse  avgt   30    22.825 ±  0.842  ns/op
    ModelBenchmark.equalsWorstCase        groovy  avgt   30   252.866 ±  3.537  ns/op
    ModelBenchmark.equalsWorstCase  staticgroovy  avgt   30    43.212 ±  0.760  ns/op
    ModelBenchmark.equalsWorstCase        lombok  avgt   30    22.822 ±  0.303  ns/op
    ModelBenchmark.equalsWorstCase         scala  avgt   30    24.309 ±  0.358  ns/op
    ModelBenchmark.equalsWorstCase        kotlin  avgt   30    64.520 ±  0.594  ns/op
    ModelBenchmark.equalsWorstCase     autovalue  avgt   30    21.327 ±  0.361  ns/op

## hashCode()

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.hashCode             intellij  avgt   30    23.937 ±  0.234  ns/op
    ModelBenchmark.hashCode              eclipse  avgt   30    24.573 ±  0.285  ns/op
    ModelBenchmark.hashCode               groovy  avgt   30   587.123 ±  5.270  ns/op
    ModelBenchmark.hashCode         staticgroovy  avgt   30   487.369 ±  4.671  ns/op
    ModelBenchmark.hashCode               lombok  avgt   30    24.275 ±  0.945  ns/op
    ModelBenchmark.hashCode                scala  avgt   30    41.934 ±  0.663  ns/op
    ModelBenchmark.hashCode               kotlin  avgt   30    24.043 ±  0.311  ns/op
    ModelBenchmark.hashCode            autovalue  avgt   30    23.286 ±  0.775  ns/op

## toString()

    Benchmark                            (model)  Mode  Cnt     Score    Error  Units
    ModelBenchmark.toString             intellij  avgt   30   374.599 ± 25.635  ns/op
    ModelBenchmark.toString              eclipse  avgt   30   359.683 ± 45.973  ns/op
    ModelBenchmark.toString               groovy  avgt   30  2195.316 ± 38.843  ns/op
    ModelBenchmark.toString         staticgroovy  avgt   30  2121.808 ± 35.377  ns/op
    ModelBenchmark.toString               lombok  avgt   30   335.303 ± 20.257  ns/op
    ModelBenchmark.toString                scala  avgt   30   635.111 ± 21.715  ns/op
    ModelBenchmark.toString               kotlin  avgt   30   362.515 ± 18.480  ns/op
    ModelBenchmark.toString            autovalue  avgt   30   401.033 ± 17.721  ns/op


# Memory

    intellij : 440 bytes, Footprint{Objects=2, References=1, Primitives=[char x 8, int x 2]}
    eclipse : 440 bytes, Footprint{Objects=2, References=1, Primitives=[char x 7, int x 2]}
    groovy : 448 bytes, Footprint{Objects=2, References=1, Primitives=[char x 6, int x 2]}
    staticgroovy : 448 bytes, Footprint{Objects=2, References=1, Primitives=[char x 12, int x 2]}
    lombok : 440 bytes, Footprint{Objects=2, References=1, Primitives=[char x 6, int x 2]}
    scala : 520 bytes, Footprint{Objects=2, References=1, Primitives=[char x 5, int x 2]}
    kotlin : 408 bytes, Footprint{Objects=2, References=1, Primitives=[char x 6, int x 2]}
    autovalue : 440 bytes, Footprint{Objects=2, References=1, Primitives=[char x 9, int x 2]}

Note: `metaClass` reference is excluded for Groovy.
