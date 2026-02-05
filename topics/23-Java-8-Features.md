# 23. JAVA 8 FEATURES

**Java 8 introduced functional programming paradigm to Java - lambda expressions, streams, and method references transformed how we write concurrent and data-processing code**

## 1. Lambda Expressions

**Concise syntax for anonymous functions; enables functional programming without verbose anonymous classes**

**See:** [17. Lambda Expressions](17-Lambda-Expressions.md) for comprehensive coverage

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

**Interface with exactly one abstract method (SAM - Single Abstract Method); enables lambda expression usage**

```java
@FunctionalInterface  // Optional but recommended - compiler validates
interface Calculator {
    int calculate(int a, int b);  // Single abstract method
    
    // Can have default methods (don't count as abstract)
    default int calculateWithLog(int a, int b) {
        int result = calculate(a, b);
        System.out.println("Result: " + result);
        return result;
    }
}

// Lambda expression replaces anonymous class
Calculator add = (a, b) -> a + b;  // Type inferred from interface
Calculator multiply = (a, b) -> a * b;

System.out.println(add.calculate(5, 3));  // 8
System.out.println(multiply.calculate(5, 3));  // 15
```

**Benefits of @FunctionalInterface annotation:**
- Compiler enforces single abstract method rule
- Documents intent that interface is for lambda/functional use
- Prevents accidental violation when adding methods later

**Built-in functional interfaces (java.util.function):**
```java
Predicate<T>: T -> boolean (test)
Function<T, R>: T -> R (apply)
Consumer<T>: T -> void (accept)
Supplier<T>: () -> T (get)
BiFunction<T, U, R>: (T, U) -> R (apply)
```

---

## 3. Stream API

**Functional data processing with declarative operations; enables elegant functional transformations**

**See:** [18. Stream API](18-Stream-API.md) for comprehensive coverage

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

int sum = nums.stream()
    .filter(n -> n % 2 == 0)
    .mapToInt(Integer::intValue)
    .sum();  // 6
```

---

## 4. Default Methods in Interfaces

**Interfaces can have method implementations; enables backward-compatible API evolution**

```java
interface Vehicle {
    void start();  // Abstract method - must implement
    
    default void honk() {  // Default method - has implementation
        System.out.println("Honk honk!");
    }
    
    default void stop() {  // Multiple default methods allowed
        System.out.println("Vehicle stopped");
    }
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
    // honk() and stop() inherited with default implementation
    
    @Override
    public void honk() {  // Can override default if needed
        System.out.println("Car honk: Beep beep!");
    }
}

Car car = new Car();
car.start();  // Car started
car.honk();   // Car honk: Beep beep!
car.stop();   // Vehicle stopped
```

**Why default methods matter:**
- **API Evolution:** Add methods to interface without breaking existing implementations
- **Example:** List interface added forEach() default method in Java 8 - all existing List implementations automatically got forEach()
- **Code reuse:** Common functionality in interface instead of duplicated across implementations
- **Multiple inheritance of type:** Class implements multiple interfaces with default methods

**Diamond problem resolution:**
```java
interface A { default void test() { System.out.println("A"); } }
interface B { default void test() { System.out.println("B"); } }

class C implements A, B {
    @Override
    public void test() {
        // MUST override - ambiguous which default to use
        A.super.test();  // Explicitly call A's version
    }
}
```

---

## 5. Static Methods in Interfaces

**Interfaces can have static methods; enables utility methods without separate utility classes**

```java
interface MathUtils {
    // Static method - belongs to interface itself, not instance
    static int add(int a, int b) {
        return a + b;
    }
    
    static double average(int... numbers) {
        int sum = 0;
        for (int n : numbers) sum += n;
        return (double) sum / numbers.length;
    }
}

// Call via interface name (not instance)
int result = MathUtils.add(5, 3);  // 8
double avg = MathUtils.average(10, 20, 30);  // 20.0

// Cannot call from implementing class
class Calculator implements MathUtils { }
Calculator calc = new Calculator();
// calc.add(5, 3);  // ERROR: not inherited
MathUtils.add(5, 3);  // Still works
```

**Key differences from default methods:**
- Static methods NOT inherited by implementing classes
- Called via interface name only (MathUtils.add not instance.add)
- Cannot be overridden (static binding)
- Use case: Utility/helper methods related to interface purpose
- Example: Comparator.comparing() is a static method for creating Comparators

---

## 6. Method References

**Shorthand syntax for lambda expressions that call single method; more readable than lambda**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Lambda - functional but verbose
names.forEach(name -> System.out.println(name));

// Method reference - concise, same behavior
names.forEach(System::out::println);
```

**Four types of method references:**

### 1. Static Method Reference
```java
interface StringConverter {
    Integer convert(String s);
}

List<String> nums = Arrays.asList("1", "2", "3");

// Lambda
List<Integer> ints1 = nums.stream()
    .map(s -> Integer.parseInt(s))
    .collect(Collectors.toList());

// Method reference to static method
List<Integer> ints2 = nums.stream()
    .map(Integer::parseInt)  // ClassName::staticMethod
    .collect(Collectors.toList());
```

### 2. Instance Method Reference
```java
String str = "hello";

// Lambda
Supplier<String> upper1 = () -> str.toUpperCase();

// Method reference to instance method
Supplier<String> upper2 = str::toUpperCase;  // instance::method

System.out.println(upper2.get());  // HELLO
```

### 3. Instance Method of Parameter
```java
List<String> words = Arrays.asList("apple", "banana", "cherry");

// Lambda - first parameter is object, call its method
words.stream()
    .map(s -> s.toUpperCase())
    .forEach(System.out::println);

// Method reference - more elegant
words.stream()
    .map(String::toUpperCase)  // Type::instanceMethod (use type not instance)
    .forEach(System.out::println);
```

### 4. Constructor Reference
```java
Supplier<List<String>> listSupplier1 = () -> new ArrayList<>();
Supplier<List<String>> listSupplier2 = ArrayList::new;  // ClassName::new

List<String> list1 = listSupplier1.get();
List<String> list2 = listSupplier2.get();

// Constructor with parameters
interface Factory {
    String create(int value);
}

Factory factory = String::valueOf;  // Calls String.valueOf(int)
String result = factory.create(42);  // "42"
```

**Benefits:**
- More readable than lambda when calling existing method
- Compiler infers types
- Common in Stream API operations (map, filter, forEach)

---

## 7. Optional Class

**Container for value that may or may not be present; avoids null pointer exceptions with explicit handling**

```java
// Create Optional
Optional<String> opt = Optional.of("Hello");  // Must not be null
Optional<String> maybe = Optional.ofNullable(value);  // Can be null
Optional<String> empty = Optional.empty();  // Empty container

// Check presence and act
if (opt.isPresent()) {
    System.out.println(opt.get());  // Unsafe - throws if empty
}

// Functional approach - safer
opt.ifPresent(System.out::println);  // Only calls if present
opt.ifPresentOrElse(
    val -> System.out.println("Value: " + val),
    () -> System.out.println("No value"));

// Provide default
String result = empty.orElse("Default");  // Returns "Default"
String result2 = empty.orElseGet(() -> computeDefault());  // Lazy computation
String result3 = empty.orElseThrow(() -> new IllegalArgumentException());  // Throw if empty

// Chaining - functional transformation pipeline
Optional<Integer> length = Optional.of("Java")
    .map(String::length)  // Transform present value
    .filter(len -> len > 3);  // Filter by condition
    
System.out.println(length.orElse(0));  // 4

// Complex example
Optional<User> user = Optional.ofNullable(findUser(id))
    .filter(u -> u.isActive())  // Only if active
    .map(u -> new UserDTO(u));  // Transform to DTO

user.ifPresentOrElse(
    dto -> sendToClient(dto),
    () -> sendErrorResponse("User not found"));
```

**Best practices:**
- Use Optional return type to signal value might be absent
- Prefer ifPresent() over isPresent() + get()
- Avoid Optional.get() without checking isPresent() first
- Use Optional in method returns, not parameters
- Don't nest Optional (Optional<Optional<T>> wrong)

**When NOT to use Optional:**
```java
// DON'T: Optional as parameter
void processUser(Optional<User> user) { }  // Wrong - caller must wrap
// DO: Check at method entrance
void processUser(User user) { }  // Caller responsible, method handles null

// DON'T: Optional in collections
List<Optional<String>> list = ...; // Wrong - use Optional<List<String>>
// DO: Filter nulls instead
List<String> list = stream.filter(Objects::nonNull).collect(...);
```

---

## 8. Date & Time API (java.time)

**Modern, immutable, thread-safe date/time classes; replaces buggy Calendar/Date classes**

```java
import java.time.*;

// LocalDate - date without time
LocalDate today = LocalDate.now();  // 2024-02-05
LocalDate birthday = LocalDate.of(2003, 5, 15);  // Specific date
Period age = Period.between(birthday, today);
System.out.println("Age: " + age.getYears());  // Age: 20

// LocalTime - time without date
LocalTime now = LocalTime.now();  // 14:30:45.123
LocalTime meeting = LocalTime.of(14, 30);  // 2:30 PM
LocalTime later = now.plusHours(2);  // 2 hours later

// LocalDateTime - date and time (no timezone)
LocalDateTime dateTime = LocalDateTime.now();
LocalDateTime deadline = LocalDateTime.of(2024, 12, 31, 23, 59);

// ZonedDateTime - date, time, and timezone
ZonedDateTime zonedNow = ZonedDateTime.now();
ZonedDateTime sydney = ZonedDateTime.now(ZoneId.of("Australia/Sydney"));
ZonedDateTime newYork = sydney.withZoneSameInstant(ZoneId.of("America/New_York"));

// Duration - time interval (hours, minutes, seconds)
LocalDateTime start = LocalDateTime.now();
LocalDateTime end = start.plusHours(2);
Duration duration = Duration.between(start, end);
System.out.println(duration.getSeconds() + " seconds");  // 7200 seconds

// Period - date interval (years, months, days)
LocalDate d1 = LocalDate.of(2020, 1, 1);
LocalDate d2 = LocalDate.of(2024, 2, 5);
Period period = Period.between(d1, d2);
System.out.println(period);  // P4Y1M4D (4 years, 1 month, 4 days)

// Formatting and parsing
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
String formatted = today.format(formatter);  // 05-02-2024

LocalDate parsed = LocalDate.parse("05-02-2024", formatter);
LocalDateTime dtParsed = LocalDateTime.parse("2024-02-05T14:30:45");

// Common patterns
final String ISO_FORMAT = "2024-02-05T14:30:45Z";  // ISO standard
final String READABLE = "February 5, 2024";  // User-friendly
final String FILENAME = "20240205_143045";  // Sortable
```

**Key improvements over Calendar:**
- Immutable - thread-safe by default
- Clear separation: LocalDate (date only), LocalTime (time only), ZonedDateTime (with timezone)
- Month/DayOfWeek are enums - no more magic numbers
- Fluent API: `date.plusDays(5).withMonth(12)`
- Easy timezone handling without manual calculation

**Common patterns:**
```java
// Start/end of day
LocalDateTime startOfDay = today.atStartOfDay();
LocalDateTime endOfDay = today.atTime(23, 59, 59);

// First/last day of month
LocalDate firstDay = today.withDayOfMonth(1);
LocalDate lastDay = today.withDayOfMonth(today.lengthOfMonth());

// Check if leap year
if (today.isLeapYear()) { }

// Day of week
DayOfWeek dayOfWeek = today.getDayOfWeek();
if (dayOfWeek == DayOfWeek.FRIDAY) { }
```

---

## 9. CompletableFuture

**Asynchronous programming model for non-blocking operations; enables parallel processing without threads**

```java
// Simple async operation
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    try { Thread.sleep(1000); } catch (InterruptedException e) { }
    return 42;  // Returns after 1 second
});

// Register callback - called when result available
future.thenAccept(result -> System.out.println("Result: " + result));

// Chaining operations - functional pipeline
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")  // Transform result
    .thenApply(String::toUpperCase)  // Another transformation
    .thenAccept(System.out::println);  // Final action: HELLO WORLD

// Combining multiple futures
CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 5);
CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 3);

f1.thenCombine(f2, (a, b) -> a + b)  // Wait for both, combine results
  .thenAccept(sum -> System.out.println("Sum: " + sum));  // Sum: 8

// Run multiple tasks in parallel
CompletableFuture.allOf(f1, f2, f3)  // Wait for all to complete
    .thenAccept(__ -> System.out.println("All done"));

// First to complete
CompletableFuture.anyOf(f1, f2, f3)  // Result of first completing
    .thenAccept(result -> System.out.println("First result: " + result));

// Error handling
CompletableFuture.supplyAsync(() -> 1 / 0)  // Throws exception
    .exceptionally(ex -> {
        System.out.println("Error: " + ex.getMessage());
        return 0;  // Default value
    })
    .thenAccept(System.out::println);

// Timeout
future.completeOnTimeout(99, 2, TimeUnit.SECONDS);
```

**Benefits over threads:**
- No thread creation overhead (uses ForkJoinPool)
- Composable operations (pipeline, combine, sequence)
- Built-in error handling
- More readable than callback hell

**Real-world example:**
```java
// Fetch user data and recommendations in parallel
CompletableFuture<User> userFuture = fetchUserAsync(userId);
CompletableFuture<List<Product>> recsFuture = fetchRecommendationsAsync(userId);

userFuture.thenCombine(recsFuture, (user, recs) -> 
    new UserResponse(user, recs))
  .exceptionally(ex -> {
      logger.error("Error fetching data", ex);
      return new UserResponse(null, emptyList());
  })
  .thenAccept(response -> sendResponse(response));
```

---

## 10. forEach() Method

**Internal iteration with lambda; replaces external iteration (for loops) with functional approach**

```java
// Collection forEach
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Traditional loop (external iteration)
for (String name : names) {
    System.out.println(name);
}

// forEach with lambda (internal iteration)
names.forEach(name -> System.out.println(name));

// forEach with method reference (most concise)
names.forEach(System.out::println);

// Map forEach - iterates over entries
Map<String, Integer> map = new HashMap<>();
map.put("Alice", 25);
map.put("Bob", 30);
map.put("Charlie", 35);

map.forEach((key, value) -> System.out.println(key + ": " + value));

// With side effects (mutations)
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
numbers.forEach(n -> {
    if (n % 2 == 0) {
        System.out.println("Even: " + n);
    }
});

// Stream forEach (lazy + filter)
numbers.stream()
    .filter(n -> n % 2 == 0)  // Lazy - only evaluates for side effects
    .forEach(System.out::println);  // Terminal operation
```

**forEach vs Stream forEach:**
```java
// Collection.forEach() - immediate execution
numbers.forEach(n -> { /* do something */ });  // Happens immediately

// Stream.forEach() - lazy evaluation possible with filters
numbers.stream()
    .filter(n -> n > 2)
    .forEach(System.out::println);  // Only iterates filtered elements

// Parallel version
numbers.parallelStream()
    .forEach(System.out::println);  // Multi-threaded iteration
```

**When to use:**
- Simple iteration: forEach is clean and readable
- Transformations: Use Stream API instead
- Need index: Use for loop instead (forEach doesn't provide index)
- Side effects only: forEach; if transforming data, use map()

---

## 11. Base64 Encoding/Decoding

**Encode binary data as printable ASCII text; useful for transmission over text-only channels**

```java
import java.util.Base64;

String original = "Pushpendra Kumar";
byte[] originalBytes = original.getBytes(StandardCharsets.UTF_8);

// Standard encoding (safe for most systems)
String encoded = Base64.getEncoder().encodeToString(originalBytes);
System.out.println("Encoded: " + encoded);  // UHVzaHBlbmRyYSBLdW1hcg==

// Decoding back
byte[] decoded = Base64.getDecoder().decode(encoded);
String result = new String(decoded, StandardCharsets.UTF_8);
System.out.println("Decoded: " + result);  // Pushpendra Kumar

// URL-safe encoding (replaces + with -, / with _)
String urlSafe = Base64.getUrlEncoder().encodeToString(originalBytes);
System.out.println("URL-safe: " + urlSafe);

// MIME encoding (breaks into 76-char lines, adds CRLF)
String mimeEncoded = Base64.getMimeEncoder().encodeToString(originalBytes);

// With no padding
Base64.getEncoder().withoutPadding().encodeToString(originalBytes);
```

**Common use cases:**
- Email attachments (MIME encoding)
- URL parameters (URL-safe encoding)
- JSON data transmission (avoiding binary data)
- Data URIs in HTML/CSS: `data:image/png;base64,iVBORw0KG...`
- Credential storage (though encryption preferred over base64)

**Base64 for image in JSON:**
```java
BufferedImage image = ImageIO.read(new File("image.png"));
ByteArrayOutputStream baos = new ByteArrayOutputStream();
ImageIO.write(image, "png", baos);
String base64Image = Base64.getEncoder().encodeToString(baos.toByteArray());

// Send in JSON
JsonObject json = new JsonObject();
json.put("image", base64Image);
```

---

## 12. Parallel Arrays

**Multi-threaded array processing; faster for large arrays on multi-core systems**

```java
import java.util.Arrays;

int[] arr = {5, 2, 8, 1, 9, 3, 7};

// Parallel sort (faster for large arrays)
Arrays.parallelSort(arr);  // Uses ForkJoinPool - multi-threaded
System.out.println(Arrays.toString(arr));  // [1, 2, 3, 5, 7, 8, 9]

// Parallel prefix (cumulative operation)
int[] nums = {1, 2, 3, 4, 5};
Arrays.parallelPrefix(nums, (a, b) -> a + b);  // Cumulative sum
System.out.println(Arrays.toString(nums));  // [1, 3, 6, 10, 15]
// Index 0: 1
// Index 1: 1+2 = 3
// Index 2: 1+2+3 = 6
// Index 3: 1+2+3+4 = 10
// Index 4: 1+2+3+4+5 = 15

// More complex prefix
String[] words = {"hello", "world", "java"};
Arrays.parallelPrefix(words, (a, b) -> a + "-" + b);
System.out.println(Arrays.toString(words));  // [hello, hello-world, hello-world-java]
```

**Performance considerations:**
- Parallel sort: Faster for arrays > 10,000 elements
- Small arrays: Sequential faster (overhead of thread management)
- Single-threaded machine: Sequential faster
- Already sorted arrays: Sequential faster (merge bottleneck)

**Recommendation:**
```java
if (arr.length < 1000) {
    Arrays.sort(arr);  // Sequential
} else {
    Arrays.parallelSort(arr);  // Parallel
}
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
