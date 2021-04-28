# gradle-linear-iorses-add-repro

This repo reproduces a performance issue with Gradle's `IterationOrderRetainingSetElementSource` collection in 6.x
and 7.0 in which the `add` operation takes a linear amount of time, because it [iterates over each element of the set
in order to check if the element is already present](https://github.com/gradle/gradle/blob/v6.8.3/subprojects/core/src/main/java/org/gradle/api/internal/collections/IterationOrderRetainingSetElementSource.java#L50).
This means adding N elements to the set takes O(N^2) time. I'd expect this collection to have O(1) `add` operations
like a `LinkedHashSet`.

As shown in the additional test tasks, this affects the performance of adding a large number of dependency constraints
to a configuration.

Example outputs from my environment (using Gradle 6.8.3):

```
./gradlew testCollectionAdd

> Task :testCollectionAdd
Time to add 500 elements (ns): 86872543
Time to add 500 elements (ns): 228500329
Time to add 500 elements (ns): 371481599
Time to add 500 elements (ns): 520755610
Time to add 500 elements (ns): 659994120
Time to add 500 elements (ns): 808859776
Time to add 500 elements (ns): 967042474
Time to add 500 elements (ns): 1106118712
Time to add 500 elements (ns): 1269669727
Time to add 500 elements (ns): 1421296960
```

```
./gradlew testAddingConstraintsToConfiguration

> Task :testAddingConstraintsToConfiguration
Time to add 1000 elements (ns): 15275018
Time to add 1000 elements (ns): 40684174
Time to add 1000 elements (ns): 65836218
Time to add 1000 elements (ns): 87404317
Time to add 1000 elements (ns): 120712485
Time to add 1000 elements (ns): 127813209
Time to add 1000 elements (ns): 152186362
Time to add 1000 elements (ns): 174331842
Time to add 1000 elements (ns): 233090015
Time to add 1000 elements (ns): 227296291
```

```
./gradlew testAddingConstraintsToConfiguration2

> Task :testAddingConstraintsToConfiguration2
Time to add 1000 elements (ns): 48810013
Time to add 1000 elements (ns): 87523201
Time to add 1000 elements (ns): 127997364
Time to add 1000 elements (ns): 154660822
Time to add 1000 elements (ns): 195184920
Time to add 1000 elements (ns): 225490898
Time to add 1000 elements (ns): 269717531
Time to add 1000 elements (ns): 321584778
Time to add 1000 elements (ns): 386256130
Time to add 1000 elements (ns): 515937008
```