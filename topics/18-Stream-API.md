# 18. STREAM API

## Stream Basics

**Sequence of elements supporting sequential/parallel operations**

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

// Create stream
Stream<Integer> stream = nums.stream();

// Process stream
nums.stream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);  // 2, 4
```

---

## Stream Creation

```java
// From collection
List<String> list = Arrays.asList("A", "B", "C");
Stream<String> stream1 = list.stream();

// From array
String[] arr = {"A", "B", "C"};
Stream<String> stream2 = Arrays.stream(arr);

// Using Stream.of()
Stream<Integer> stream3 = Stream.of(1, 2, 3, 4, 5);

// Infinite streams
Stream<Integer> infinite = Stream.iterate(0, n -> n + 1);
Stream<Double> random = Stream.generate(Math::random);

// Range
IntStream range = IntStream.range(1, 10);  // 1 to 9
IntStream rangeClosed = IntStream.rangeClosed(1, 10);  // 1 to 10
```

---

## Intermediate Operations (Lazy)

### filter()

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5, 6);
nums.stream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);  // 2, 4, 6
```

### map()

```java
List<String> names = Arrays.asList("alice", "bob", "charlie");
names.stream()
     .map(String::toUpperCase)
     .forEach(System.out::println);  // ALICE, BOB, CHARLIE
```

### flatMap()

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5, 6)
);

nested.stream()
      .flatMap(List::stream)
      .forEach(System.out::println);  // 1, 2, 3, 4, 5, 6
```

### distinct()

```java
Arrays.asList(1, 2, 2, 3, 3, 4).stream()
      .distinct()
      .forEach(System.out::println);  // 1, 2, 3, 4
```

### sorted()

```java
Arrays.asList(5, 2, 8, 1, 9).stream()
      .sorted()
      .forEach(System.out::println);  // 1, 2, 5, 8, 9

// Custom comparator
Arrays.asList("Charlie", "Alice", "Bob").stream()
      .sorted(Comparator.reverseOrder())
      .forEach(System.out::println);  // Charlie, Bob, Alice
```

### limit() & skip()

```java
Stream.iterate(1, n -> n + 1)
      .limit(5)
      .forEach(System.out::println);  // 1, 2, 3, 4, 5

Arrays.asList(1, 2, 3, 4, 5).stream()
      .skip(2)
      .forEach(System.out::println);  // 3, 4, 5
```

---

## Terminal Operations (Eager)

### forEach()

```java
Arrays.asList(1, 2, 3).stream()
      .forEach(System.out::println);
```

### collect()

```java
List<Integer> result = Arrays.asList(1, 2, 3, 4).stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());  // [2, 4]

Set<Integer> set = Arrays.asList(1, 2, 2, 3).stream()
    .collect(Collectors.toSet());  // [1, 2, 3]
```

### reduce()

```java
// Sum
int sum = Arrays.asList(1, 2, 3, 4, 5).stream()
    .reduce(0, (a, b) -> a + b);  // 15

// Product
int product = Arrays.asList(1, 2, 3, 4).stream()
    .reduce(1, (a, b) -> a * b);  // 24

// Max
Optional<Integer> max = Arrays.asList(5, 2, 8, 1).stream()
    .reduce((a, b) -> a > b ? a : b);  // 8
```

### count()

```java
long count = Arrays.asList(1, 2, 3, 4, 5).stream()
    .filter(n -> n % 2 == 0)
    .count();  // 2
```

### anyMatch(), allMatch(), noneMatch()

```java
boolean hasEven = Arrays.asList(1, 2, 3).stream()
    .anyMatch(n -> n % 2 == 0);  // true

boolean allEven = Arrays.asList(2, 4, 6).stream()
    .allMatch(n -> n % 2 == 0);  // true

boolean noneNegative = Arrays.asList(1, 2, 3).stream()
    .noneMatch(n -> n < 0);  // true
```

### findFirst(), findAny()

```java
Optional<Integer> first = Arrays.asList(1, 2, 3).stream()
    .filter(n -> n > 1)
    .findFirst();  // 2

Optional<Integer> any = Arrays.asList(1, 2, 3).parallelStream()
    .filter(n -> n > 1)
    .findAny();  // 2 or 3 (non-deterministic)
```

---

## Practical Examples

### Filter & Map

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

List<String> result = names.stream()
    .filter(name -> name.length() > 3)
    .map(String::toUpperCase)
    .collect(Collectors.toList());
System.out.println(result);  // [ALICE, CHARLIE, DAVID]
```

### GroupingBy

```java
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
System.out.println(byLength);  // {3=[Bob], 5=[Alice, David], 7=[Charlie]}
```

### Joining

```java
String joined = names.stream()
    .collect(Collectors.joining(", "));
System.out.println(joined);  // Alice, Bob, Charlie, David
```

---

## Parallel Streams

```java
// Sequential
long sum1 = IntStream.range(1, 1000000)
    .sum();

// Parallel (faster for large datasets)
long sum2 = IntStream.range(1, 1000000)
    .parallel()
    .sum();
```

**Use parallel streams for:**
- CPU-intensive operations
- Large datasets
- Independent operations

---

## Stream vs Collection

| Feature | Stream | Collection |
|---------|--------|------------|
| Storage | No storage | Stores elements |
| Operations | Functional (declarative) | Imperative |
| Iteration | Internal | External |
| Consumption | Once (terminal operation) | Multiple times |

---

## Key Takeaways

✓ **Intermediate operations:** Lazy, return stream (filter, map, sorted)
✓ **Terminal operations:** Eager, produce result (collect, reduce, forEach)
✓ Streams are **not reusable**
✓ Use **parallel streams** for performance
✓ Prefer streams for **declarative** data processing

**Next:** [19. Collectors](19-Collectors.md)
