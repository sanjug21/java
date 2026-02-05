# 18. STREAM API

## Stream Basics

**Sequence of elements supporting sequential/parallel operations - functional approach to data processing**

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

// Create stream from collection
Stream<Integer> stream = nums.stream();

// Process with pipeline: filter (intermediate) → forEach (terminal)
nums.stream()
    .filter(n -> n % 2 == 0)           // Intermediate: keeps only even numbers
    .forEach(System.out::println);     // Terminal: executes and produces result
// Output: 2, 4
```

**Key concept:**
- Streams are **not collections** - they don't store data
- Streams use **lazy evaluation** - intermediate operations don't execute until terminal operation
- Streams support **functional programming** - pass behavior (lambdas) rather than implementing loops

---

## Stream Creation

**Multiple ways to create streams from different data sources**

```java
// From collection - most common
List<String> list = Arrays.asList("A", "B", "C");
Stream<String> stream1 = list.stream();

// From array
String[] arr = {"A", "B", "C"};
Stream<String> stream2 = Arrays.stream(arr);

// Using Stream.of() - varargs
Stream<Integer> stream3 = Stream.of(1, 2, 3, 4, 5);

// Infinite streams - generates unlimited elements on demand
Stream<Integer> infinite = Stream.iterate(0, n -> n + 1);      // 0, 1, 2, 3, ...
Stream<Double> random = Stream.generate(Math::random);          // Random numbers

// Primitive ranges - optimized for integers
IntStream range = IntStream.range(1, 10);          // 1 to 9 (excludes 10)
IntStream rangeClosed = IntStream.rangeClosed(1, 10);  // 1 to 10 (includes 10)

// Note: Infinite streams must be limited with .limit() before terminal operation
Stream.iterate(0, n -> n + 1).limit(5).forEach(System.out::println);  // 0, 1, 2, 3, 4
```

---

## Intermediate Operations (Lazy)

**Don't execute immediately - wait for terminal operation - return Stream for chaining**

### filter()

**Keep elements matching condition**

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5, 6);
nums.stream()
    .filter(n -> n % 2 == 0)  // Keep only if n % 2 == 0
    .forEach(System.out::println);  // 2, 4, 6
```

### map()

**Transform each element to another value**

```java
List<String> names = Arrays.asList("alice", "bob", "charlie");
names.stream()
     .map(String::toUpperCase)  // Transform: "alice" → "ALICE"
     .forEach(System.out::println);  // ALICE, BOB, CHARLIE
```

### flatMap()

**Flatten nested collections - map then flatten**

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5, 6)
);

nested.stream()
      .flatMap(List::stream)  // Convert List<List<Integer>> → Stream<Integer>
      .forEach(System.out::println);  // 1, 2, 3, 4, 5, 6
// Without flatMap, you'd get 3 items (the lists), not 6 (the integers)
```

### distinct()

**Remove duplicates - based on equals()**

```java
Arrays.asList(1, 2, 2, 3, 3, 4).stream()
      .distinct()  // Keeps first occurrence of each unique element
      .forEach(System.out::println);  // 1, 2, 3, 4
```

### sorted()

**Sort elements - natural order or custom comparator**

```java
Arrays.asList(5, 2, 8, 1, 9).stream()
      .sorted()  // Natural order (ascending)
      .forEach(System.out::println);  // 1, 2, 5, 8, 9

// Custom comparator
Arrays.asList("Charlie", "Alice", "Bob").stream()
      .sorted(Comparator.reverseOrder())  // Reverse order
      .forEach(System.out::println);  // Charlie, Bob, Alice
```

### limit() & skip()

**limit: take first N | skip: skip first N**

```java
Stream.iterate(1, n -> n + 1)
      .limit(5)  // Take only first 5 elements
      .forEach(System.out::println);  // 1, 2, 3, 4, 5

Arrays.asList(1, 2, 3, 4, 5).stream()
      .skip(2)  // Skip first 2 elements
      .forEach(System.out::println);  // 3, 4, 5
```

---

## Terminal Operations (Eager)

**Execute the pipeline - produce final result - can only be called once per stream**

### forEach()

**Execute action for each element - no return value**

```java
Arrays.asList(1, 2, 3).stream()
      .forEach(System.out::println);  // 1, 2, 3
// Use for: side effects (printing, database writes, etc.)
```

### collect()

**Accumulate stream elements into collection - most flexible terminal op**

```java
List<Integer> result = Arrays.asList(1, 2, 3, 4).stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());  // [2, 4]

Set<Integer> set = Arrays.asList(1, 2, 2, 3).stream()
    .collect(Collectors.toSet());  // [1, 2, 3]

// Useful collectors: toList(), toSet(), toMap(), joining(), groupingBy(), etc.
```

### reduce()

**Combine stream elements into single value - accumulation**

```java
// Sum - reduce to single integer
int sum = Arrays.asList(1, 2, 3, 4, 5).stream()
    .reduce(0, (accumulator, current) -> accumulator + current);  // 15
// 0 + 1 + 2 + 3 + 4 + 5 = 15

// Product - multiply all
int product = Arrays.asList(1, 2, 3, 4).stream()
    .reduce(1, (a, b) -> a * b);  // 24

// Max - find largest
Optional<Integer> max = Arrays.asList(5, 2, 8, 1).stream()
    .reduce((a, b) -> a > b ? a : b);  // Optional[8]
// Returns Optional since stream could be empty
```

### count()

**Return number of elements - long type**

```java
long count = Arrays.asList(1, 2, 3, 4, 5).stream()
    .filter(n -> n % 2 == 0)  // Filter: 2, 4
    .count();  // 2
```

### anyMatch(), allMatch(), noneMatch()

**Boolean matching - short-circuit operations**

```java
boolean hasEven = Arrays.asList(1, 2, 3).stream()
    .anyMatch(n -> n % 2 == 0);  // true (2 is even)

boolean allEven = Arrays.asList(2, 4, 6).stream()
    .allMatch(n -> n % 2 == 0);  // true (all are even)

boolean noneNegative = Arrays.asList(1, 2, 3).stream()
    .noneMatch(n -> n < 0);  // true (no negatives)

// Short-circuit: stops evaluating as soon as result is determined
```

### findFirst(), findAny()

**Find element matching condition - Optional return**

```java
Optional<Integer> first = Arrays.asList(1, 2, 3).stream()
    .filter(n -> n > 1)
    .findFirst();  // Optional[2] - deterministic

Optional<Integer> any = Arrays.asList(1, 2, 3).parallelStream()
    .filter(n -> n > 1)
    .findAny();  // Could be 2 or 3 - non-deterministic in parallel
```

---

## Practical Examples

### Filter & Map

**Common pattern: filter condition, then transform**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

List<String> result = names.stream()
    .filter(name -> name.length() > 3)    // Keep names with length > 3
    .map(String::toUpperCase)             // Transform to uppercase
    .collect(Collectors.toList());        // Collect to List

System.out.println(result);  // [ALICE, CHARLIE, DAVID]
// Bob excluded (length = 3)
```

### GroupingBy

**Partition stream into groups - returns Map**

```java
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));

System.out.println(byLength);
// {3=[Bob], 5=[Alice, David], 7=[Charlie]}
// Groups strings by their length
```

### Joining

**Concatenate stream elements into single String**

```java
String joined = names.stream()
    .collect(Collectors.joining(", "));

System.out.println(joined);  // Alice, Bob, Charlie, David
// Useful for converting list to readable string output
```

---

## Parallel Streams

**Process elements concurrently across multiple CPU cores - for performance optimization**

```java
// Sequential - single thread processes all elements
long sum1 = IntStream.range(1, 1000000)
    .sum();  // Processes in one thread

// Parallel - multiple threads process in parallel
long sum2 = IntStream.range(1, 1000000)
    .parallel()  // Convert to parallel stream
    .sum();      // Much faster for large data

// Both give same result, but parallel is typically faster
```

**When to use parallel streams:**
- **CPU-intensive operations:** Complex calculations that take significant time
- **Large datasets:** Typically 10,000+ elements (overhead of parallelization not worth it for small data)
- **Independent operations:** No shared mutable state (lambdas should be stateless)
- **Ordered operations not critical:** Order may be unpredictable in parallel

**When NOT to use:**
- Small datasets (overhead > benefit)
- I/O-bound operations (doesn't help, Thread I/O blocks)
- Stateful lambdas or shared mutable data (race conditions)

---

## Stream vs Collection

**When to use each - different paradigms**

| Feature | Stream | Collection |
|---------|--------|------------|
| Storage | No storage (pipeline) | Stores all elements in memory |
| Operations | Functional (declarative - what to do) | Imperative (how to do) |
| Iteration | Internal (handled by stream) | External (you control loop) |
| Consumption | One-time use (terminal op exhausts) | Reusable (iterate multiple times) |
| Performance | Lazy evaluation (skips unnecessary work) | Eager (all operations execute) |
| Use case | Transformations, filtering, aggregation | Data storage, random access |

**Example showing difference:**
```java
// Collection - imperative (HOW)
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> even = new ArrayList<>();
for (Integer n : nums) {
    if (n % 2 == 0) {
        even.add(n);
    }
}

// Stream - declarative (WHAT)
List<Integer> even = nums.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
// Same result, but Stream approach is cleaner, more readable
```

---

## Key Takeaways

✓ **Intermediate operations:** Lazy evaluation, return Stream, chainable (filter, map, sorted, limit)

✓ **Terminal operations:** Eager execution, produce final result, consume stream (collect, reduce, forEach, findFirst)

✓ **Streams are not reusable:** Once terminal operation is called, stream is exhausted - must create new stream

✓ **Lazy evaluation benefit:** Operations skip work when not needed (e.g., limit stops processing after N elements)

✓ **Parallel streams:** Use for CPU-intensive operations on large datasets (10,000+ elements)

✓ **Functional approach:** Declare WHAT you want (filter, map, collect), not HOW to loop

✓ **Stream creation:** From collections (.stream()), arrays (Arrays.stream()), ranges, or infinite sources

**Next:** [19. Collectors](19-Collectors.md)
