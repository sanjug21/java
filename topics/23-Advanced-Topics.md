# 23. ADVANCED TOPICS

## 1. Multithreading & Concurrency

### Thread Basics

```java
// Method 1: Extend Thread
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}

// Method 2: Implement Runnable (preferred)
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable running");
    }
}

// Usage
new MyThread().start();
new Thread(new MyRunnable()).start();
new Thread(() -> System.out.println("Lambda thread")).start();
```

---

### Synchronization

```java
class Counter {
    private int count = 0;
    
    // Synchronized method
    synchronized void increment() {
        count++;
    }
    
    // Synchronized block
    void decrement() {
        synchronized(this) {
            count--;
        }
    }
    
    synchronized int getCount() {
        return count;
    }
}
```

---

### ExecutorService & Thread Pools

```java
import java.util.concurrent.*;

// Fixed thread pool
ExecutorService executor = Executors.newFixedThreadPool(3);

for (int i = 1; i <= 5; i++) {
    int taskId = i;
    executor.submit(() -> {
        System.out.println("Task " + taskId + " by " + Thread.currentThread().getName());
    });
}

executor.shutdown();
executor.awaitTermination(10, TimeUnit.SECONDS);
```

---

## 2. Reflection

**Inspect and manipulate classes at runtime**

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

// Get class
Class<?> clazz = Class.forName("Person");

// Get constructor and create instance
Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
Object obj = constructor.newInstance("Pushpendra", 22);

// Get and invoke method
Method method = clazz.getMethod("display");
method.invoke(obj);  // Name: Pushpendra, Age: 22

// Get fields
Field[] fields = clazz.getDeclaredFields();
for (Field field : fields) {
    System.out.println("Field: " + field.getName());
}

// Access private field
Field nameField = clazz.getDeclaredField("name");
nameField.setAccessible(true);
String name = (String) nameField.get(obj);
```

---

## 3. Annotations

### Built-in Annotations

```java
class Parent {
    void display() { }
}

class Child extends Parent {
    @Override
    void display() { }  // Ensures method is overriding
    
    @Deprecated
    void oldMethod() { }  // Marks as deprecated
    
    @SuppressWarnings("unchecked")
    void genericMethod() { }  // Suppresses warnings
}
```

### Custom Annotations

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
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

```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

// Usage
Day today = Day.MONDAY;
System.out.println(today);  // MONDAY

// Switch with enum
switch (today) {
    case MONDAY:
        System.out.println("Start of week");
        break;
    case FRIDAY:
        System.out.println("End of week");
        break;
}

// Enum with fields and methods
enum Planet {
    EARTH(6371), MARS(3389), JUPITER(69911);
    
    private int radius;
    
    Planet(int radius) {
        this.radius = radius;
    }
    
    int getRadius() {
        return radius;
    }
}

System.out.println(Planet.EARTH.getRadius());  // 6371
```

---

## 5. Security & Encryption

```java
import javax.crypto.*;
import java.security.*;

// Generate AES key
KeyGenerator keyGen = KeyGenerator.getInstance("AES");
keyGen.init(256, new SecureRandom());
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
