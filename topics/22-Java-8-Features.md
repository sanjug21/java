# 22. JAVA 8 FEATURES

## 1. Lambda Expressions

**See:** [17. Lambda Expressions](17-Lambda-Expressions.md)

```java
// Before Java 8
Runnable r1 = new Runnable() {
    public void run() { System.out.println("Running"); }
};

// Java 8
Runnable r2 = () -> System.out.println("Running");
```

---

## 2. Functional Interfaces

**Interface with single abstract method**

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}

Calculator add = (a, b) -> a + b;
System.out.println(add.calculate(5, 3));  // 8
```

---

## 3. Stream API

**See:** [18. Stream API](18-Stream-API.md)

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

int sum = nums.stream()
    .filter(n -> n % 2 == 0)
    .mapToInt(Integer::intValue)
    .sum();  // 6
```

---

## 4. Default Methods in Interfaces

**Interfaces can have method implementations**

```java
interface Vehicle {
    void start();  // Abstract method
    
    default void honk() {  // Default method
        System.out.println("Honk honk!");
    }
}

class Car implements Vehicle {
    public void start() {
        System.out.println("Car started");
    }
    // honk() inherited with default implementation
}

Car car = new Car();
car.start();  // Car started
car.honk();   // Honk honk!
```

---

## 5. Static Methods in Interfaces

```java
interface MathUtils {
    static int add(int a, int b) {
        return a + b;
    }
}

// Call static method
int result = MathUtils.add(5, 3);  // 8
```

---

## 6. Method References

**Shorthand for lambda expressions**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Lambda
names.forEach(name -> System.out.println(name));

// Method reference
names.forEach(System.out::println);

// Types:
// 1. Static method: ClassName::staticMethod
List<String> nums = Arrays.asList("1", "2", "3");
List<Integer> ints = nums.stream()
    .map(Integer::parseInt)
    .collect(Collectors.toList());

// 2. Instance method: object::instanceMethod
String str = "hello";
Supplier<String> upper = str::toUpperCase;

// 3. Constructor: ClassName::new
Supplier<List<String>> listSupplier = ArrayList::new;
```

---

## 7. Optional Class

**Container to avoid NullPointerException**

```java
Optional<String> opt = Optional.of("Hello");
opt.ifPresent(System.out::println);  // Hello

Optional<String> empty = Optional.empty();
String result = empty.orElse("Default");  // Default

// Chaining
Optional<Integer> length = Optional.of("Java")
    .map(String::length);
System.out.println(length.orElse(0));  // 4

// Filter
Optional<String> filtered = Optional.of("Hello")
    .filter(s -> s.length() > 3);
System.out.println(filtered.orElse("Not found"));  // Hello
```

---

## 8. Date & Time API (java.time)

**Immutable, thread-safe date/time classes**

```java
// LocalDate
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(2003, 5, 15);
Period age = Period.between(birthday, today);
System.out.println("Age: " + age.getYears());

// LocalTime
LocalTime now = LocalTime.now();
LocalTime meeting = LocalTime.of(14, 30);

// LocalDateTime
LocalDateTime dateTime = LocalDateTime.now();

// Duration
LocalDateTime start = LocalDateTime.now();
LocalDateTime end = start.plusHours(2);
Duration duration = Duration.between(start, end);
System.out.println(duration.getSeconds() + " seconds");

// Formatting
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
String formatted = today.format(formatter);
```

---

## 9. CompletableFuture

**Asynchronous programming**

```java
// Simple async operation
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    return 42;
});

future.thenAccept(result -> System.out.println("Result: " + result));

// Chaining
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenApply(String::toUpperCase)
    .thenAccept(System.out::println);  // HELLO WORLD

// Combining futures
CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 5);
CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 3);

f1.thenCombine(f2, (a, b) -> a + b)
  .thenAccept(sum -> System.out.println("Sum: " + sum));  // Sum: 8
```

---

## 10. forEach() Method

**Available in Iterable interface**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// forEach with lambda
names.forEach(name -> System.out.println(name));

// forEach with method reference
names.forEach(System.out::println);

// Map forEach
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);
map.forEach((key, value) -> System.out.println(key + ": " + value));
```

---

## 11. Base64 Encoding/Decoding

```java
import java.util.Base64;

String original = "Pushpendra Kumar";

// Encoding
String encoded = Base64.getEncoder().encodeToString(original.getBytes());
System.out.println("Encoded: " + encoded);

// Decoding
byte[] decoded = Base64.getDecoder().decode(encoded);
System.out.println("Decoded: " + new String(decoded));

// URL-safe encoding
String urlSafe = Base64.getUrlEncoder().encodeToString(original.getBytes());
```

---

## 12. Parallel Arrays

```java
import java.util.Arrays;

int[] arr = {5, 2, 8, 1, 9, 3, 7};

// Parallel sort (faster for large arrays)
Arrays.parallelSort(arr);

// Parallel prefix
int[] nums = {1, 2, 3, 4, 5};
Arrays.parallelPrefix(nums, (a, b) -> a + b);
System.out.println(Arrays.toString(nums));  // [1, 3, 6, 10, 15]
```

---

## 13. Collectors

**See:** [19. Collectors](19-Collectors.md)

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Joining
String joined = names.stream()
    .collect(Collectors.joining(", "));  // Alice, Bob, Charlie

// GroupingBy
Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
```

---

## Features Summary

| Feature | Description |
|---------|-------------|
| Lambda | Functional programming, concise syntax |
| Stream API | Declarative data processing |
| Default methods | Interface evolution without breaking |
| Method references | Cleaner lambda syntax |
| Optional | Null safety |
| Date/Time API | Modern, immutable date handling |
| CompletableFuture | Async programming |
| forEach | Internal iteration |

---

## Key Takeaways

✓ **Lambda:** Concise functional programming
✓ **Stream API:** Powerful data processing
✓ **Optional:** Avoid NullPointerException
✓ **Date/Time API:** Immutable, thread-safe
✓ **Default methods:** Interface evolution
✓ Java 8 enables **functional programming** paradigm

**Next:** [23. Advanced Topics](23-Advanced-Topics.md)
