# 19. COLLECTORS

## Basic Collectors

**Terminal operations that accumulate stream elements into collections - transform stream result into desired data structure**

### toList(), toSet(), toCollection()

**Convert stream to specific collection type**

```java
// toList() - creates ArrayList
List<Integer> list = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.toList());  // [1, 2, 3, 4, 5]

// toSet() - creates HashSet, removes duplicates
Set<Integer> set = Stream.of(1, 2, 2, 3)
    .collect(Collectors.toSet());  // [1, 2, 3] (order not guaranteed)

// toCollection() - specify exact collection type
TreeSet<Integer> treeSet = Stream.of(5, 2, 8, 1)
    .collect(Collectors.toCollection(TreeSet::new));  // [1, 2, 5, 8] (sorted)
```

---

### toMap()

**Convert stream to Map - specify key and value transformations**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Simple: Key = name, Value = length
Map<String, Integer> map = names.stream()
    .collect(Collectors.toMap(
        name -> name,        // Key function
        String::length       // Value function
    ));
System.out.println(map);  // {Alice=5, Bob=3, Charlie=7}

// With duplicate key handling - merge function
Map<Integer, String> byLength = names.stream()
    .collect(Collectors.toMap(
        String::length,                    // Key: length
        name -> name,                      // Value: name
        (existing, replacement) -> existing + ", " + replacement  // Merge duplicates
    ));
System.out.println(byLength);  // {5=Alice, 3=Bob, 7=Charlie}
```

---

### joining()

**Concatenate stream elements into single String**

```java
String result = Stream.of("A", "B", "C")
    .collect(Collectors.joining());  // ABC (no separator)

String withDelimiter = Stream.of("A", "B", "C")
    .collect(Collectors.joining(", "));  // A, B, C

String withPrefixSuffix = Stream.of("A", "B", "C")
    .collect(Collectors.joining(", ", "[", "]"));  // [A, B, C]
```

---

## Numerical Collectors

**Specialized collectors for aggregation operations on numbers**

### counting(), summingInt(), averagingInt()

**Aggregate numeric values - count, sum, average**

```java
// counting() - returns long (can use with groupingBy)
long count = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.counting());  // 5

// summingInt() - sum all values (also summingLong, summingDouble)
int sum = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.summingInt(Integer::intValue));  // 15

// averagingInt() - average value (also averagingLong, averagingDouble)
double avg = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.averagingInt(Integer::intValue));  // 3.0
```

### maxBy(), minBy()

**Find maximum or minimum element with comparator**

```java
// maxBy() - returns Optional
Optional<Integer> max = Stream.of(5, 2, 8, 1, 9)
    .collect(Collectors.maxBy(Integer::compareTo));  // Optional[9]

// minBy() - returns Optional
Optional<Integer> min = Stream.of(5, 2, 8, 1, 9)
    .collect(Collectors.minBy(Integer::compareTo));  // Optional[1]

// Useful: returns Optional because stream could be empty
if (max.isPresent()) {
    System.out.println("Max: " + max.get());
}
```

---

## Grouping Collectors

**Partition stream into multiple groups based on classification function - returns Map**

### groupingBy()

**Group elements by classification function - creates Map<K, List<V>>**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Eve");

// Simple grouping - group by string length
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
System.out.println(byLength);
// {3=[Bob, Eve], 5=[Alice, David], 7=[Charlie]}
// Key: length | Value: list of names with that length

// Group and count occurrences - count how many names per length
Map<Integer, Long> countByLength = names.stream()
    .collect(Collectors.groupingBy(
        String::length,              // Classification function (key)
        Collectors.counting()        // Downstream collector (aggregate values)
    ));
System.out.println(countByLength);  // {3=2, 5=2, 7=1}
```

### groupingBy with downstream collector

**Complex grouping - combine grouping with other collectors**

```java
class Student {
    String name;
    String dept;
    int age;
    
    Student(String name, String dept, int age) {
        this.name = name;
        this.dept = dept;
        this.age = age;
    }
}

List<Student> students = Arrays.asList(
    new Student("Alice", "CS", 22),
    new Student("Bob", "CS", 23),
    new Student("Charlie", "EC", 22)
);

// Group by dept, collect names (mapping)
Map<String, List<String>> namesByDept = students.stream()
    .collect(Collectors.groupingBy(
        s -> s.dept,                                    // Key: department
        Collectors.mapping(s -> s.name, Collectors.toList())  // Value: list of names
    ));
System.out.println(namesByDept);  // {CS=[Alice, Bob], EC=[Charlie]}

// Group by dept, calculate average age
Map<String, Double> avgAgeByDept = students.stream()
    .collect(Collectors.groupingBy(
        s -> s.dept,                                    // Key: department
        Collectors.averagingInt(s -> s.age)             // Value: average age
    ));
System.out.println(avgAgeByDept);  // {CS=22.5, EC=22.0}
```

---

## Partitioning Collectors

**Divides stream into exactly two groups based on boolean predicate - returns Map<Boolean, List<T>>**

### partitioningBy()

**Binary classification - true/false buckets**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Partition into even and odd
Map<Boolean, List<Integer>> partition = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));

System.out.println(partition.get(true));   // [2, 4, 6, 8, 10]  (even)
System.out.println(partition.get(false));  // [1, 3, 5, 7, 9]   (odd)

// Partition and count occurrences
Map<Boolean, Long> countPartition = numbers.stream()
    .collect(Collectors.partitioningBy(
        n -> n % 2 == 0,                  // Predicate: even or odd
        Collectors.counting()             // Downstream: count each partition
    ));
System.out.println(countPartition);  // {false=5, true=5}

// Key difference from groupingBy: always exactly 2 groups (true/false)
```

---

## Advanced Collectors

**Combine collectors for complex transformations**

### mapping()

**Transform elements before collecting - map then collect**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Traditional: map then collect
List<Integer> lengths1 = names.stream()
    .map(String::length)
    .collect(Collectors.toList());

// Using mapping collector (same result)
List<Integer> lengths2 = names.stream()
    .collect(Collectors.mapping(
        String::length,              // Transform function
        Collectors.toList()          // Downstream collector
    ));
System.out.println(lengths2);  // [5, 3, 7]

// Useful in groupingBy context
Map<Integer, List<String>> namesByLength = names.stream()
    .collect(Collectors.groupingBy(
        String::length,
        Collectors.mapping(
            String::toUpperCase,     // Transform names to uppercase
            Collectors.toList()
        )
    ));
// {5=[ALICE], 3=[BOB], 7=[CHARLIE]}
```

### filtering() (Java 9+)

**Filter elements before collecting - filter then collect**

```java
// Traditional: filter then collect
List<Integer> evenNumbers1 = Stream.of(1, 2, 3, 4, 5, 6)
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());

// Using filtering collector (useful in grouping)
List<Integer> evenNumbers2 = Stream.of(1, 2, 3, 4, 5, 6)
    .collect(Collectors.filtering(
        n -> n % 2 == 0,             // Predicate
        Collectors.toList()          // Downstream collector
    ));
System.out.println(evenNumbers2);  // [2, 4, 6]

// Useful in groupingBy to filter per group
Map<Boolean, List<Integer>> filteredPartition = Stream.of(1, 2, 3, 4, 5, 6)
    .collect(Collectors.partitioningBy(
        n -> n < 4,
        Collectors.filtering(n -> n % 2 == 0, Collectors.toList())
    ));
// {true=[2], false=[4, 6]}
```

### flatMapping() (Java 9+)

**Flatten nested streams before collecting - flatten then collect**

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4)
);

// Traditional: flatMap then collect
List<Integer> flat1 = nested.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList());

// Using flatMapping collector
List<Integer> flat2 = nested.stream()
    .collect(Collectors.flatMapping(
        List::stream,                // Function returning stream
        Collectors.toList()          // Downstream collector
    ));
System.out.println(flat2);  // [1, 2, 3, 4]
```

---

## Custom Collectors

**Create custom collectors for specialized aggregation needs**

```java
// Custom collector to concatenate strings with separator
Collector<String, StringBuilder, String> customCollector = 
    Collector.of(
        StringBuilder::new,              // Supplier: creates accumulator
        (sb, s) -> sb.append(s).append("|"),  // Accumulator: how to add element
        (sb1, sb2) -> sb1.append(sb2),   // Combiner: merge accumulators (for parallel)
        sb -> sb.substring(0, sb.length() - 1)  // Finisher: transform to result
    );

String result = Stream.of("A", "B", "C")
    .collect(customCollector);  // A|B|C

// Simpler example: concatenate without separator
Collector<String, StringBuilder, String> simpleConcat = 
    Collector.of(
        StringBuilder::new,
        StringBuilder::append,
        StringBuilder::append,
        StringBuilder::toString
    );

String simple = Stream.of("X", "Y", "Z")
    .collect(simpleConcat);  // XYZ
```

---

## Practical Example

```java
class Transaction {
    String type;
    double amount;
    
    Transaction(String type, double amount) {
        this.type = type;
        this.amount = amount;
    }
}

List<Transaction> transactions = Arrays.asList(
    new Transaction("CREDIT", 100),
    new Transaction("DEBIT", 50),
    new Transaction("CREDIT", 200),
    new Transaction("DEBIT", 75)
);

// Group by type and sum amounts
Map<String, Double> totalByType = transactions.stream()
    .collect(Collectors.groupingBy(
        t -> t.type,
        Collectors.summingDouble(t -> t.amount)
    ));
System.out.println(totalByType);  // {CREDIT=300.0, DEBIT=125.0}
```

---

## Key Takeaways

✓ **toList/toSet:** Basic collection
✓ **joining:** String concatenation
✓ **groupingBy:** Group elements by criteria
✓ **partitioningBy:** Split into two groups
✓ **mapping/filtering:** Transform/filter before collecting
✓ Use **downstream collectors** for complex aggregations

**Next:** [20. Exception Handling](20-Exception-Handling.md)
