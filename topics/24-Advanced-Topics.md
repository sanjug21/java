# 24. ADVANCED TOPICS

**Mastery of advanced Java concepts: concurrency, reflection, annotations, security, and modern language features**

## 1. Multithreading & Concurrency

### Thread Basics

**Creating and starting threads; two approaches for implementing concurrent execution**

**Creating and starting threads; two approaches for implementing concurrent execution**

```java
// Method 1: Extend Thread class (less flexible - single inheritance)
class MyThread extends Thread {
    @Override
    public void run() {  // Override run() method
        System.out.println("Thread running on: " + Thread.currentThread().getName());
    }
}

// Method 2: Implement Runnable interface (preferred - allows other inheritance)
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable running on: " + Thread.currentThread().getName());
    }
}

// Usage - start() not run()
new MyThread().start();  // Starts new thread, calls run() in that thread
new Thread(new MyRunnable()).start();  // Wrap Runnable in Thread
new Thread(() -> System.out.println("Lambda thread")).start();  // Java 8+

// Important: Call start() not run()
// thread.run();  // WRONG: Executes in current thread, no parallelism
// thread.start();  // RIGHT: Creates new thread, executes run() there
```

**Key points:**
- `Thread.start()` creates new thread, calls `run()` in that thread
- `thread.run()` executes `run()` in current thread (no parallelism!)
- Runnable preferred (single responsibility, allows other inheritance)
- Modern code: Use ExecutorService or CompletableFuture instead of raw threads
- `Thread.currentThread()` gets current thread object
- Thread names useful for debugging and logging

---

### Synchronization

**Controlling access to shared resources; prevents race conditions when multiple threads access same data**

```java
class Counter {
    private int count = 0;  // Shared resource
    
    // Synchronized method - entire method is critical section
    // Only one thread can execute at a time for this object
    synchronized void increment() {
        count++;  // count++ is NOT atomic: read, increment, write
    }
    
    // Synchronized block - only lock around critical code
    // More granular control, better performance than method lock
    void decrement() {
        synchronized(this) {  // Lock on current object
            count--;  // Critical section
        }
        // Other code here runs without lock
    }
    
    // Only getter, thread-safe (reading single value)
    synchronized int getCount() {
        return count;
    }
}

// Without synchronization - race condition
Counter c = new Counter();
Thread t1 = new Thread(() -> {
    for (int i = 0; i < 1000; i++) {
        c.increment();  // Potential race condition
    }
});
Thread t2 = new Thread(() -> {
    for (int i = 0; i < 1000; i++) {
        c.increment();
    }
});

t1.start();
t2.start();
t1.join();  // Wait for t1
t2.join();  // Wait for t2
System.out.println(c.getCount());  // Could be < 2000 (race condition!)
```

**Common concurrency constructs:**
```java
// volatile - ensures visibility across threads (not atomicity)
private volatile int counter = 0;  // All threads see latest value

// AtomicInteger - atomic operations (thread-safe without explicit sync)
AtomicInteger counter = new AtomicInteger(0);
counter.incrementAndGet();  // Atomic operation
counter.addAndGet(5);

// Lock (more flexible than synchronized)
Lock lock = new ReentrantLock();
lock.lock();
try {
    // Critical section
} finally {
    lock.unlock();  // Always unlock in finally
}
```

**Race condition example:**
```java
private int x = 0;

// WRONG - not thread-safe
if (x == 0) {  // Thread 1 checks, x is 0
    x = 1;     // Thread 2 gets here first, x becomes 1
    doSomething();  // Thread 1 does something unexpected
}

// RIGHT - atomic check-and-act
synchronized void checkAndSet() {
    if (x == 0) {
        x = 1;  // Locked - no interference
        doSomething();
    }
}
```

---

### ExecutorService & Thread Pools

**Reusable thread pool for task execution; modern way to handle concurrent tasks (prefer over manual threads)**

```java
import java.util.concurrent.*;

// Create fixed-size thread pool (reuses threads)
ExecutorService executor = Executors.newFixedThreadPool(3);  // Max 3 concurrent threads

for (int i = 1; i <= 5; i++) {
    final int taskId = i;
    executor.submit(() -> {  // Submit task (Runnable)
        System.out.println("Task " + taskId + " running on " + 
            Thread.currentThread().getName());
    });
}
// Output: Tasks 1,2,3 run, then tasks 4,5 wait for threads to free up

// Alternative: submit Callable (returns result)
ExecutorService executor2 = Executors.newFixedThreadPool(3);
Future<Integer> future = executor2.submit(() -> {
    return 42;  // Callable returns value
});
Integer result = future.get();  // Blocks until result available
boolean done = future.isDone();  // Check if completed

// Shutdown thread pool
executor.shutdown();  // No new tasks accepted, existing tasks complete
// executor.shutdownNow();  // Force shutdown, interrupt running tasks

// Wait for completion
boolean terminated = executor.awaitTermination(10, TimeUnit.SECONDS);
if (!terminated) {
    System.out.println("Some tasks still running");
}

// Thread pool types
ExecutorService fixed = Executors.newFixedThreadPool(5);  // Fixed size, reuse
ExecutorService cached = Executors.newCachedThreadPool();  // Create as needed, reuse
ExecutorService scheduled = Executors.newScheduledThreadPool(2);  // Delayed/periodic
ExecutorService single = Executors.newSingleThreadExecutor();  // One thread

// ScheduledExecutorService - delayed execution
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
scheduler.schedule(() -> System.out.println("Delayed"), 5, TimeUnit.SECONDS);
scheduler.scheduleAtFixedRate(() -> System.out.println("Periodic"), 
    1, 2, TimeUnit.SECONDS);  // Initial 1s delay, repeat every 2s
```

**Best practices:**
- Use ExecutorService for concurrent tasks (not raw threads)
- Use CompletableFuture for chained asynchronous operations
- Always call shutdown() to clean up resources
- Use try-with-resources (if available) or try-finally for shutdown

**Example with try-with-resources (Java 19+):**
```java
try (ExecutorService executor = Executors.newFixedThreadPool(2)) {
    executor.submit(() -> System.out.println("Task"));
}  // Automatically shutdown
```

---

## 2. Reflection

**Inspect and manipulate classes, methods, fields at runtime; enables frameworks and dynamic programming**

```java
import java.lang.reflect.*;

class Person {
    private String name;
    private int age;
    
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    void display() {
        System.out.println("Name: " + name + ", Age: " + age);
    }
}

// Get class in multiple ways
Class<?> clazz1 = Class.forName("Person");  // By name (string, runtime)
Class<?> clazz2 = Person.class;  // Direct reference (compile-time)
Class<?> clazz3 = person.getClass();  // From instance

// Get class information
String name = clazz1.getSimpleName();  // "Person"
String fullName = clazz1.getName();  // Package + class name
Class<?> superclass = clazz1.getSuperclass();  // Parent class

// Get constructors
Constructor<?>[] constructors = clazz1.getConstructors();
Constructor<?> constructor = clazz1.getConstructor(String.class, int.class);
Object person = constructor.newInstance("Pushpendra", 22);  // Create instance

// Get and invoke methods
Method[] methods = clazz1.getMethods();  // Public methods including inherited
Method displayMethod = clazz1.getMethod("display");  // Public method
displayMethod.invoke(person);  // Name: Pushpendra, Age: 22

// Invoke method with parameters
Method someMethod = clazz1.getMethod("methodName", String.class, int.class);
someMethod.invoke(person, "param1", 42);

// Get fields (public only)
Field[] publicFields = clazz1.getFields();

// Get all fields including private
Field[] allFields = clazz1.getDeclaredFields();
for (Field field : allFields) {
    System.out.println("Field: " + field.getName() + ", Type: " + field.getType());
}

// Access private field
Field nameField = clazz1.getDeclaredField("name");
nameField.setAccessible(true);  // Bypass access control
String nameValue = (String) nameField.get(person);
System.out.println(nameValue);  // Pushpendra

// Modify private field
nameField.set(person, "John");
System.out.println(nameField.get(person));  // John
```

**Real-world reflection use cases:**
```java
// Spring: Dependency injection - find annotations and instantiate
@Autowired
private UserService userService;  // Spring uses reflection to inject

// JUnit: Find @Test methods and execute
for (Method method : testClass.getMethods()) {
    if (method.isAnnotationPresent(Test.class)) {
        method.invoke(new TestInstance());  // Execute test
    }
}

// Serialization: Convert object to JSON
public String toJson(Object obj) {
    Class<?> clazz = obj.getClass();
    StringBuilder json = new StringBuilder("{");
    for (Field field : clazz.getDeclaredFields()) {
        field.setAccessible(true);
        json.append("\"").append(field.getName()).append("\":");
        json.append(field.get(obj)).append(",");
    }
    return json.toString();
}
```

**Performance note:**
- Reflection 10-100x slower than direct calls
- Use for framework code, not hot paths
- Cache reflected objects (Method, Field, Constructor) for repeated use
- Consider bytecode generation (ASM, Javassist) for performance-critical dynamic code

---

## 3. Annotations

**Metadata that doesn't directly affect code execution; enables compile-time and runtime processing**

### Built-in Annotations

```java
class Parent {
    void display() { }
}

class Child extends Parent {
    @Override  // Compiler check: ensure method exists in parent
    void display() { }  // ERROR if parent doesn't have display()
    
    @Deprecated  // Compiler warning: method is outdated
    void oldMethod() { }  // Callers get warning in IDE
    
    @SuppressWarnings("unchecked")  // Suppress specific warning
    void genericMethod() { 
        List list = new ArrayList();  // No warning for unchecked
    }
    
    @FunctionalInterface  // For interfaces - check single abstract method
    interface MyInterface {
        void doSomething();
    }
}
```

### Custom Annotations

```java
import java.lang.annotation.*;

// Define annotation
@Retention(RetentionPolicy.RUNTIME)  // Available at runtime (reflection)
@Target(ElementType.METHOD)  // Can only annotate methods
@interface Test {
    String value() default "test";  // Annotation element
    int timeout() default 5000;  // With default value
}

// Define multiple targets
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.FIELD, ElementType.PARAMETER})
@interface MyAnnotation { }

// Use annotation
class MyTest {
    @Test("LoginTest")  // Set annotation element
    void testUserLogin() {
        System.out.println("Testing login");
    }
    
    @Test  // Use default value
    void testUserLogout() { }
}

// Process annotations at runtime
public static void runTests(Class<?> testClass) {
    for (Method method : testClass.getMethods()) {
        if (method.isAnnotationPresent(Test.class)) {
            Test testAnnotation = method.getAnnotation(Test.class);
            System.out.println("Running: " + testAnnotation.value());
            
            try {
                method.invoke(new MyTest());
                System.out.println("PASS");
            } catch (Exception e) {
                System.out.println("FAIL: " + e.getMessage());
            }
        }
    }
}
```

**Annotation meta-annotations:**
- `@Retention`: How long annotation retained (SOURCE, CLASS, RUNTIME)
- `@Target`: Where annotation can be used (TYPE, METHOD, FIELD, PARAMETER, etc.)
- `@Documented`: Include in JavaDoc
- `@Inherited`: Subclasses inherit annotation from parent

**Common framework annotations:**
```java
// Spring
@Component  // Register as Spring bean
@Autowired  // Inject dependency
@Transactional  // Database transaction

// JPA
@Entity  // Map to database table
@Column(name = "col_name")  // Map to column
@Id  // Primary key

// JUnit
@Test  // Mark as test method
@Before  // Run before each test
@After  // Run after each test
```
@interface Test {
    String value() default "";
}

class MyTest {
    @Test("Sample test")
    void testMethod() {
        System.out.println("Testing");
    }
}

// Process annotations
Method method = MyTest.class.getMethod("testMethod");
if (method.isAnnotationPresent(Test.class)) {
    Test test = method.getAnnotation(Test.class);
    System.out.println("Test value: " + test.value());
}
```

---

## 4. Enums

**Type-safe constants; prevents invalid values and improves code readability**

```java
// Basic enum
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

// Usage
Day today = Day.MONDAY;
System.out.println(today);  // MONDAY
System.out.println(today.ordinal());  // 0 (position in enum)
System.out.println(today.name());  // "MONDAY"

// Enum in switch
switch (today) {
    case MONDAY:
        System.out.println("Start of week");
        break;
    case FRIDAY:
        System.out.println("End of week");
        break;
    case SATURDAY, SUNDAY:  // Java 14+ - multiple cases
        System.out.println("Weekend");
        break;
}

// Enum with fields and methods
enum Planet {
    EARTH(6371, 5.972),     // Constructor call
    MARS(3389, 0.642),
    JUPITER(69911, 1898),
    SATURN(58232, 568);
    
    private final int radius;  // All fields must be final
    private final double mass;
    
    Planet(int radius, double mass) {
        this.radius = radius;
        this.mass = mass;
    }
    
    public int getRadius() { return radius; }
    public double getMass() { return mass; }
    
    public double surfaceGravity() {
        return mass / (radius * radius);
    }
}

System.out.println(Planet.EARTH.getRadius());  // 6371
System.out.println(Planet.EARTH.surfaceGravity());  // ~9.8

// Enum with abstract methods
enum Operation {
    PLUS("ADD") {
        @Override
        double apply(double x, double y) { return x + y; }
    },
    MINUS("SUBTRACT") {
        @Override
        double apply(double x, double y) { return x - y; }
    },
    TIMES("MULTIPLY") {
        @Override
        double apply(double x, double y) { return x * y; }
    };
    
    private final String label;
    
    Operation(String label) { this.label = label; }
    
    abstract double apply(double x, double y);
}

System.out.println(Operation.PLUS.apply(5, 3));  // 8
```

**Enum advantages:**
- Type-safe: Can't assign invalid value
- Singleton: Each enum constant is single instance
- Comparable: Enum constants have natural ordering
- Printable: toString() gives constant name

**Common patterns:**
```java
// Status enum
enum Status {
    PENDING, ACTIVE, INACTIVE, DELETED
}

// Direction enum with coordinates
enum Direction {
    NORTH(0, 1),
    SOUTH(0, -1),
    EAST(1, 0),
    WEST(-1, 0);
    
    final int dx, dy;
    Direction(int dx, int dy) { this.dx = dx; this.dy = dy; }
}

// Enum from string
Status status = Status.valueOf("ACTIVE");  // ACTIVE
Status status2 = Status.values()[0];  // PENDING (first constant)

// Check if contains
if (Stream.of(Status.values()).anyMatch(s -> s == Status.ACTIVE)) { }
```

---

## 5. Security & Encryption

**Cryptographic operations for data protection**

```java
import javax.crypto.*;
import java.security.*;

// Generate AES key
KeyGenerator keyGen = KeyGenerator.getInstance("AES");
keyGen.init(256, new SecureRandom());  // 256-bit key
SecretKey key = keyGen.generateKey();

// Encrypt
Cipher cipher = Cipher.getInstance("AES");
cipher.init(Cipher.ENCRYPT_MODE, key);
String plaintext = "Secret message";
byte[] encrypted = cipher.doFinal(plaintext.getBytes());

// Decrypt
cipher.init(Cipher.DECRYPT_MODE, key);
byte[] decrypted = cipher.doFinal(encrypted);
System.out.println(new String(decrypted));  // Secret message
```

---

## 6. Networking

### Socket Programming

```java
// Server
import java.io.*;
import java.net.*;

ServerSocket serverSocket = new ServerSocket(8080);
Socket socket = serverSocket.accept();

BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
PrintWriter out = new PrintWriter(socket.getOutputStream(), true);

String message = in.readLine();
out.println("Echo: " + message);

socket.close();
serverSocket.close();

// Client
Socket socket = new Socket("localhost", 8080);
PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));

out.println("Hello Server");
String response = in.readLine();
System.out.println(response);

socket.close();
```

---

## 7. Garbage Collection

```java
// Request GC (not guaranteed)
System.gc();

// Get memory info
Runtime runtime = Runtime.getRuntime();
long totalMemory = runtime.totalMemory();
long freeMemory = runtime.freeMemory();
long usedMemory = totalMemory - freeMemory;

System.out.println("Used: " + usedMemory / 1024 + "KB");
```

**GC Algorithms:**
- Serial GC: Single-threaded
- Parallel GC: Multi-threaded
- G1 GC: Region-based, low latency
- ZGC: Ultra-low latency (Java 11+)

---

## 8. JVM Options

```bash
# Memory settings
java -Xms512m -Xmx2g MyApp

# GC selection
java -XX:+UseG1GC MyApp

# Print GC logs
java -XX:+PrintGCDetails MyApp

# Heap dump on OutOfMemoryError
java -XX:+HeapDumpOnOutOfMemoryError MyApp
```

---

## 9. Modules (Java 9+)

```java
// module-info.java
module my.module {
    requires java.sql;
    exports com.example.mypackage;
}
```

---

## 10. Records (Java 14+)

**Immutable data carriers**

```java
// Traditional class
class Person {
    private final String name;
    private final int age;
    
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String name() { return name; }
    public int age() { return age; }
}

// Record (concise)
record Person(String name, int age) { }

// Usage
Person p = new Person("Alice", 22);
System.out.println(p.name());  // Alice
```

---

## 11. Pattern Matching (Java 16+)

```java
// instanceof pattern matching
Object obj = "Hello";

if (obj instanceof String s) {
    System.out.println(s.toUpperCase());  // HELLO
}

// Switch expressions with pattern matching (Java 17+)
String result = switch (obj) {
    case String s -> s.toUpperCase();
    case Integer i -> "Number: " + i;
    default -> "Unknown";
};
```

---

## 12. Sealed Classes (Java 17+)

**Restrict which classes can extend/implement**

```java
sealed interface Shape permits Circle, Rectangle { }

final class Circle implements Shape { }
final class Rectangle implements Shape { }
// class Triangle implements Shape { }  // ERROR: not permitted
```

---

## Key Takeaways

✓ **Multithreading:** ExecutorService for thread pools
✓ **Reflection:** Runtime class inspection
✓ **Annotations:** Metadata for code
✓ **Enums:** Type-safe constants
✓ **Networking:** Socket programming
✓ **Modern Java:** Records, pattern matching, sealed classes
✓ **GC:** Automatic memory management

---

## Complexity Reference

| Collection | Access | Insert | Delete | Search |
|------------|--------|--------|--------|--------|
| ArrayList | O(1) | O(n) | O(n) | O(n) |
| LinkedList | O(n) | O(1) | O(1) | O(n) |
| HashSet | - | O(1) | O(1) | O(1) |
| TreeSet | - | O(log n) | O(log n) | O(log n) |
| HashMap | O(1) | O(1) | O(1) | O(1) |

---

## Sorting Complexity

| Algorithm | Best | Average | Worst | Stable |
|-----------|------|---------|-------|--------|
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | No |

---

**Congratulations!** You've completed the Complete Java Guide covering 23 comprehensive topics.

**Next Steps:**
1. Practice implementing each concept
2. Build real projects
3. Study frameworks: Spring Boot, Hibernate
4. Prepare for interviews with coding challenges

**Previous:** [22. Java 8 Features](22-Java-8-Features.md) | **Home:** [README](README.md)
