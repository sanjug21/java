# 19. COLLECTORS

## Basic Collectors

### toList(), toSet(), toCollection()

```java
List<Integer> list = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.toList());

Set<Integer> set = Stream.of(1, 2, 2, 3)
    .collect(Collectors.toSet());  // [1, 2, 3]

TreeSet<Integer> treeSet = Stream.of(5, 2, 8, 1)
    .collect(Collectors.toCollection(TreeSet::new));  // [1, 2, 5, 8]
```

---

### toMap()

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Key: name, Value: length
Map<String, Integer> map = names.stream()
    .collect(Collectors.toMap(
        name -> name,
        String::length
    ));
System.out.println(map);  // {Alice=5, Bob=3, Charlie=7}

// Handle duplicates with merge function
Map<Integer, String> byLength = names.stream()
    .collect(Collectors.toMap(
        String::length,
        name -> name,
        (existing, replacement) -> existing + ", " + replacement
    ));
```

---

### joining()

```java
String result = Stream.of("A", "B", "C")
    .collect(Collectors.joining());  // ABC

String withDelimiter = Stream.of("A", "B", "C")
    .collect(Collectors.joining(", "));  // A, B, C

String withPrefixSuffix = Stream.of("A", "B", "C")
    .collect(Collectors.joining(", ", "[", "]"));  // [A, B, C]
```

---

## Numerical Collectors

### counting(), summingInt(), averagingInt()

```java
long count = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.counting());  // 5

int sum = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.summingInt(Integer::intValue));  // 15

double avg = Stream.of(1, 2, 3, 4, 5)
    .collect(Collectors.averagingInt(Integer::intValue));  // 3.0
```

### maxBy(), minBy()

```java
Optional<Integer> max = Stream.of(5, 2, 8, 1, 9)
    .collect(Collectors.maxBy(Integer::compareTo));  // 9

Optional<Integer> min = Stream.of(5, 2, 8, 1, 9)
    .collect(Collectors.minBy(Integer::compareTo));  // 1
```

---

## Grouping Collectors

### groupingBy()

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Eve");

// Group by length
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
System.out.println(byLength);
// {3=[Bob, Eve], 5=[Alice, David], 7=[Charlie]}

// Group and count
Map<Integer, Long> countByLength = names.stream()
    .collect(Collectors.groupingBy(
        String::length,
        Collectors.counting()
    ));
System.out.println(countByLength);  // {3=2, 5=2, 7=1}
```

### groupingBy with downstream collector

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

// Group by dept, collect names
Map<String, List<String>> namesByDept = students.stream()
    .collect(Collectors.groupingBy(
        s -> s.dept,
        Collectors.mapping(s -> s.name, Collectors.toList())
    ));
System.out.println(namesByDept);  // {CS=[Alice, Bob], EC=[Charlie]}

// Group by dept, get average age
Map<String, Double> avgAgeByDept = students.stream()
    .collect(Collectors.groupingBy(
        s -> s.dept,
        Collectors.averagingInt(s -> s.age)
    ));
System.out.println(avgAgeByDept);  // {CS=22.5, EC=22.0}
```

---

## Partitioning Collectors

### partitioningBy()

**Divides into two groups based on predicate**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Partition into even and odd
Map<Boolean, List<Integer>> partition = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));

System.out.println(partition.get(true));   // [2, 4, 6, 8, 10]
System.out.println(partition.get(false));  // [1, 3, 5, 7, 9]

// Partition and count
Map<Boolean, Long> countPartition = numbers.stream()
    .collect(Collectors.partitioningBy(
        n -> n % 2 == 0,
        Collectors.counting()
    ));
System.out.println(countPartition);  // {false=5, true=5}
```

---

## Advanced Collectors

### mapping()

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

List<Integer> lengths = names.stream()
    .collect(Collectors.mapping(
        String::length,
        Collectors.toList()
    ));
System.out.println(lengths);  // [5, 3, 7]
```

### filtering() (Java 9+)

```java
List<Integer> evenNumbers = Stream.of(1, 2, 3, 4, 5, 6)
    .collect(Collectors.filtering(
        n -> n % 2 == 0,
        Collectors.toList()
    ));
System.out.println(evenNumbers);  // [2, 4, 6]
```

### flatMapping() (Java 9+)

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4)
);

List<Integer> flat = nested.stream()
    .collect(Collectors.flatMapping(
        List::stream,
        Collectors.toList()
    ));
System.out.println(flat);  // [1, 2, 3, 4]
```

---

## Custom Collectors

```java
// Custom collector to concatenate strings
Collector<String, StringBuilder, String> customCollector = 
    Collector.of(
        StringBuilder::new,              // Supplier
        StringBuilder::append,            // Accumulator
        StringBuilder::append,            // Combiner
        StringBuilder::toString          // Finisher
    );

String result = Stream.of("A", "B", "C")
    .collect(customCollector);  // ABC
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
