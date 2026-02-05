# ADVANCED JAVA MASTERY GUIDE
## Complete Backend Java Development Reference with Advanced Topics

---

## TABLE OF CONTENTS
1. Java Basics & Fundamentals
2. JVM, JRE, JDK Architecture
3. Memory Management in Java
4. Strings & String Pool
5. Static vs Non-Static & Access Modifiers
6. Java Class Hierarchies
7. Autoboxing & Unboxing
8. Object-Oriented Programming (OOP)
9. Inheritance - Types, Problems & Solutions
10. Inner Classes
11. Dynamic Binding
12. Design Patterns
13. SOLID Principles
14. Data Structures & Collections Framework
15. Sorting Algorithms
16. Generics & Type System
17. Lambdas & Functional Interfaces
18. Streams API
19. Collectors
20. Exception Handling
21. I/O Programming
22. Java 8 Features
23. Advanced Topic 1: Concurrency & Multithreading
24. Advanced Topic 2: Memory Management & JVM Internals
25. Advanced Topic 3: Advanced I/O & NIO.2
26. Advanced Topic 4: Reflection & Annotations
27. Advanced Topic 5: Generics (Advanced)
28. Advanced Topic 6: Design Patterns & SOLID Principles (Extended)
29. Advanced Topic 7: Java Networking
30. Advanced Topic 8: Security in Java
31. Advanced Topic 9: Java EE / Jakarta EE (Enterprise Features)
32. Advanced Topic 10: Modules (Java 9+)
33. Advanced Topic 11: Reactive & Functional Programming
34. Advanced Topic 12: Testing & Build Tools

---

# SECTION 1: JAVA BASICS & FUNDAMENTALS

## 1.1 Hello World Program

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

**Explanation:**
- `public`: Access modifier - accessible from anywhere
- `class`: Keyword to define a class
- `main(String[] args)`: Entry point of Java program
- `System.out.println()`: Prints text to console

---

## 1.2 Variables & Data Types

```java
public class VariablesExample {
    public static void main(String[] args) {
        // Primitive data types (8 types)
        byte byteVar = 10;              // 8-bit integer
        short shortVar = 100;           // 16-bit integer
        int intVar = 1000;              // 32-bit integer
        long longVar = 10000L;          // 64-bit integer
        float floatVar = 10.5f;         // 32-bit decimal
        double doubleVar = 20.5;        // 64-bit decimal
        char charVar = 'A';             // 16-bit Unicode character
        boolean boolVar = true;         // true or false

        // Reference data types
        String name = "Pushpendra";     // String
        int[] arr = {1, 2, 3};          // Array

        System.out.println("Int: " + intVar);
        System.out.println("Name: " + name);
    }
}
```

**Data Type Ranges:**
- `byte`: -128 to 127
- `short`: -32,768 to 32,767
- `int`: -2,147,483,648 to 2,147,483,647
- `long`: -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807
- `float`: 32-bit IEEE 754
- `double`: 64-bit IEEE 754

---

## 1.3 Control Flow - If-Else

```java
public class IfElseExample {
    public static void main(String[] args) {
        int marks = 85;

        if (marks >= 90) {
            System.out.println("Grade: A");
        } else if (marks >= 80) {
            System.out.println("Grade: B");
        } else if (marks >= 70) {
            System.out.println("Grade: C");
        } else {
            System.out.println("Grade: F");
        }
        
        // Ternary operator
        String result = marks >= 60 ? "Pass" : "Fail";
        System.out.println(result);
    }
}
```

---

## 1.4 Loops

```java
public class LoopExample {
    public static void main(String[] args) {
        // For loop
        System.out.println("For loop:");
        for (int i = 1; i <= 5; i++) {
            System.out.println("Iteration: " + i);
        }

        // While loop
        System.out.println("\nWhile loop:");
        int counter = 1;
        while (counter <= 5) {
            System.out.println("Counter: " + counter);
            counter++;
        }

        // Do-while loop
        System.out.println("\nDo-while loop:");
        int num = 1;
        do {
            System.out.println("Num: " + num);
            num++;
        } while (num <= 5);

        // Enhanced for loop
        System.out.println("\nEnhanced for loop:");
        int[] arr = {10, 20, 30, 40};
        for (int n : arr) {
            System.out.println("Value: " + n);
        }
    }
}
```

**Key Points:**
- `for`: Known iterations
- `while`: Unknown iterations
- `do-while`: Executes at least once
- Enhanced `for`: Iterating collections/arrays

---

## 1.5 Methods

```java
public class MethodExample {
    // Method with parameters and return value
    static int add(int a, int b) {
        return a + b;
    }

    // Method with parameters but no return
    static void greet(String name) {
        System.out.println("Hello, " + name);
    }

    // Method with no parameters or return
    static void printWelcome() {
        System.out.println("Welcome!");
    }

    public static void main(String[] args) {
        int sum = add(10, 20);
        System.out.println("Sum: " + sum);

        greet("Pushpendra");
        printWelcome();
    }
}
```

**Method Signature:** `returnType methodName(parameterType parameterName)`

---

# SECTION 2: JVM, JRE, JDK ARCHITECTURE

## 2.1 Components Overview

**JDK (Java Development Kit)**
- Complete package for development
- Includes: JRE + Compiler (javac) + Tools (debugger, profiler, etc.)

**JRE (Java Runtime Environment)**
- Runtime package for executing Java programs
- Includes: JVM + Runtime Libraries + Other utilities

**JVM (Java Virtual Machine)**
- Abstract computing machine
- Executes bytecode
- Makes Java platform-independent

---

## 2.2 Compilation & Execution Flow

```
┌─────────────────┐
│  Source Code    │
│  HelloWorld.java│
└────────┬────────┘
         │
         ▼
   ┌──────────┐
   │  javac   │  (Compiler)
   │(Compiler)│
   └────┬─────┘
        │
        ▼
┌──────────────────┐
│  Bytecode File   │
│ HelloWorld.class │
└────────┬─────────┘
         │
    ┌────▼────┐
    │ java    │  (Launcher)
    └────┬────┘
         │
    ┌────▼──────────┐
    │  ClassLoader  │  Loads .class into memory
    └────┬──────────┘
         │
    ┌────▼──────────┐
    │  Bytecode     │  Verifies bytecode
    │  Verifier     │
    └────┬──────────┘
         │
    ┌────▼──────────────────┐
    │  Execution Engine      │
    │  (Interpreter/JIT)     │
    └────┬───────────────────┘
         │
    ┌────▼─────────────────┐
    │  Native Machine Code  │
    │  (Platform-specific)  │
    └──────────────────────┘
```

---

## 2.3 ClassLoader Hierarchy

```
Bootstrap ClassLoader (C/C++)
    └── Loads: java.lang, java.util (rt.jar)

        ▼

Extension ClassLoader
    └── Loads: javax.* packages (jre/lib/ext)

        ▼

Application ClassLoader
    └── Loads: User-defined classes
```

```java
public class ClassLoaderExample {
    public static void main(String[] args) {
        // Get ClassLoader hierarchy
        ClassLoader cl = String.class.getClassLoader();
        System.out.println("String ClassLoader: " + cl);  // null (Bootstrap)

        ClassLoader cl2 = ClassLoaderExample.class.getClassLoader();
        System.out.println("App ClassLoader: " + cl2);

        // Get parent ClassLoader
        ClassLoader parent = cl2.getParent();
        System.out.println("Parent ClassLoader: " + parent);
    }
}
```

---

## 2.4 Compiler (javac)

```bash
# Compile single file
javac HelloWorld.java

# Compile with classpath
javac -cp ./lib/*:. MyClass.java

# Compile all Java files
javac *.java

# Generate bytecode with debug info
javac -g HelloWorld.java
```

---

## 2.5 Interpreter vs JIT Compiler

**Interpreter (First Phase)**
- Reads bytecode line-by-line
- Converts to machine code on-the-fly
- Slower but immediate execution

**JIT (Just-In-Time) Compiler**
- Monitors frequently used code paths
- Compiles hot methods to native code
- Caches compiled code
- Subsequent calls execute native code (faster)

```java
public class JITExample {
    static int compute(int n) {
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += i;
        }
        return sum;
    }

    public static void main(String[] args) {
        // First calls: Interpreted
        for (int i = 0; i < 100; i++) {
            compute(10000);
        }
        
        // After threshold: JIT compiled
        long start = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            compute(10000);
        }
        long end = System.nanoTime();
        System.out.println("Time: " + (end - start) / 1_000_000 + "ms");
    }
}
```

---

# SECTION 3: MEMORY MANAGEMENT IN JAVA

## 3.1 Memory Areas

**Heap Memory**
- Stores all objects and arrays
- Shared across all threads
- Garbage collected
- Larger but slower access

```java
public class HeapExample {
    public static void main(String[] args) {
        String name = "Pushpendra";  // Object allocated in heap
        int[] arr = {1, 2, 3};       // Array allocated in heap
        Student student = new Student();  // Object in heap
    }
}

class Student {
    String name;
    int age;
    // Instance variables allocated in heap with object
}
```

**Stack Memory**
- Stores method calls and local variables
- Each thread has its own stack
- Last-In-First-Out (LIFO)
- Automatically freed when method exits
- Smaller but faster access

```java
public class StackExample {
    public static void main(String[] args) {
        int x = 10;         // x stored in stack
        int y = 20;         // y stored in stack
        int sum = x + y;    // sum stored in stack
        
        myMethod(5);
    }
    
    static void myMethod(int param) {
        int localVar = param * 2;  // localVar in stack
        // All stack variables freed when method exits
    }
}
```

**Metaspace (Java 8+)**
- Stores class definitions and metadata
- Replaced PermGen from earlier versions
- Dynamically grows from native memory
- Tunable via `-XX:MetaspaceSize` and `-XX:MaxMetaspaceSize`

```java
public class MetaspaceExample {
    public static void main(String[] args) {
        // Each class loaded → metadata stored in Metaspace
        loadClasses();
    }
    
    static void loadClasses() {
        try {
            // Dynamically load classes
            for (int i = 0; i < 1000; i++) {
                Class.forName("java.lang.String");
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 3.2 Garbage Collection (GC)

### GC Algorithms

**1. Serial GC**
- Single thread
- Good for small heaps (<100MB)
- Long pause times
- Flag: `-XX:+UseSerialGC`

**2. Parallel GC (Throughput GC)**
- Multiple threads
- Good for multi-processor systems
- Higher throughput, longer pauses
- Flag: `-XX:+UseParallelGC`

**3. G1 GC (Garbage First)**
- Divides heap into regions
- Collects regions with most garbage first
- Predictable pause times
- Default in Java 9+
- Flag: `-XX:+UseG1GC`

**4. ZGC (Z Garbage Collector)**
- Ultra-low latency (<10ms pause time)
- Java 11+
- Good for large heaps (>10GB)
- Flag: `-XX:+UseZGC`

---

## 3.3 Garbage Collection Example

```java
public class GCExample {
    static class Node {
        int value;
        Node(int value) { this.value = value; }
    }
    
    public static void main(String[] args) {
        long beforeMem = Runtime.getRuntime().totalMemory() - 
                        Runtime.getRuntime().freeMemory();
        System.out.println("Memory before: " + beforeMem / 1024 / 1024 + "MB");
        
        // Create and discard objects
        for (int i = 0; i < 1_000_000; i++) {
            Node node = new Node(i);
            // node goes out of scope, eligible for GC
        }
        
        // Suggest garbage collection
        System.gc();
        
        long afterMem = Runtime.getRuntime().totalMemory() - 
                       Runtime.getRuntime().freeMemory();
        System.out.println("Memory after: " + afterMem / 1024 / 1024 + "MB");
    }
}
```

---

## 3.4 JVM Tuning Parameters

```bash
# Set heap size
java -Xms1024m -Xmx2048m MyApp

# Set GC type
java -XX:+UseG1GC -Xms1024m -Xmx2048m MyApp

# Monitor GC
java -XX:+PrintGCDetails -XX:+PrintGCTimeStamps MyApp

# ZGC with tuning
java -XX:+UseZGC -XX:ZUncommitDelay=300 MyApp

# Print GC logs to file
java -Xloggc:gc.log -XX:+PrintGCDetails MyApp
```

---

# SECTION 4-22: [Previous Sections from Complete Java Guide]
# (Strings & String Pool, OOP, Design Patterns, Collections, Sorting, Generics, Lambdas, Streams, Exceptions, I/O, Java 8 Features)
# [Include all previous content as shown in Complete-Java-Guide.md]

---

# ADVANCED TOPIC 1: CONCURRENCY & MULTITHREADING

## 1.1 Thread Lifecycle

**States:**
1. **NEW**: Thread created but not started
2. **RUNNABLE**: Thread started, ready or running
3. **BLOCKED**: Waiting for monitor lock
4. **WAITING**: Waiting indefinitely for another thread
5. **TIMED_WAITING**: Waiting for specified time
6. **TERMINATED**: Thread finished execution

```
    ┌──────────────┐
    │     NEW      │ (Created)
    └──────┬───────┘
           │ start()
    ┌──────▼────────────────┐
    │    RUNNABLE           │
    ├──────┬────────────────┤
    │      │                │
    │      │              run() completes
    │      │                │
    │      │         ┌──────▼─────────┐
    │      │         │   TERMINATED   │
    │      │         └────────────────┘
    │      │
    │   Blocked on monitor
    │      │
    │  ┌───▼────────────┐
    │  │   BLOCKED      │
    │  └───┬────────────┘
    │      │ Lock acquired
    │      └──────────┐
    │                 │
    │   wait()/join() │
    │    or sleep()   │
    │      │          │
    │  ┌───▼──────────┴─────┐
    │  │ WAITING/TIMED_WAIT │
    │  └───┬────────────────┘
    │      │ notify() or timeout
    └──────┘
```

---

## 1.2 Creating Threads

### Approach 1: Extend Thread Class

```java
public class ThreadExample1 extends Thread {
    private String name;
    
    public ThreadExample1(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + " - " + i);
            try {
                Thread.sleep(1000);  // Sleep 1 second
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public static void main(String[] args) {
        ThreadExample1 t1 = new ThreadExample1("Thread 1");
        ThreadExample1 t2 = new ThreadExample1("Thread 2");
        
        t1.start();  // Must call start(), not run()
        t2.start();
    }
}
```

**Output (may vary):**
```
Thread 1 - 0
Thread 2 - 0
Thread 1 - 1
Thread 2 - 1
...
```

---

### Approach 2: Implement Runnable (Preferred)

```java
public class ThreadExample2 implements Runnable {
    private String name;
    
    public ThreadExample2(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + " - " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public static void main(String[] args) {
        Thread t1 = new Thread(new ThreadExample2("Thread 1"));
        Thread t2 = new Thread(new ThreadExample2("Thread 2"));
        
        t1.start();
        t2.start();
    }
}
```

**Why Runnable is preferred:**
- Java supports single inheritance
- Class can still extend other classes
- Cleaner separation of concerns

---

### Approach 3: Using Lambda (Java 8+)

```java
public class ThreadExample3 {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread 1 - " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread 2 - " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        t1.start();
        t2.start();
    }
}
```

---

## 1.3 Callable & Future

```java
import java.util.concurrent.*;

public class CallableExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        // Callable returns a value
        Callable<Integer> task = () -> {
            System.out.println("Computing...");
            Thread.sleep(2000);
            return 42;
        };
        
        // Submit and get Future
        Future<Integer> future = executor.submit(task);
        
        System.out.println("Waiting for result...");
        
        // Check if done
        while (!future.isDone()) {
            System.out.println("Still computing...");
            Thread.sleep(500);
        }
        
        // Get result (blocking)
        Integer result = future.get();
        System.out.println("Result: " + result);
        
        executor.shutdown();
    }
}
```

---

## 1.4 Synchronization

### Race Condition Problem

```java
public class RaceConditionExample {
    static class Counter {
        int count = 0;
        
        // PROBLEM: Not thread-safe
        void increment() {
            count++;  // Three steps: read, increment, write
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.count);  // Expected: 2000, Actual: < 2000
    }
}
```

---

### Solution 1: Synchronized Method

```java
public class SynchronizedMethodExample {
    static class Counter {
        int count = 0;
        
        // Synchronized method - only one thread at a time
        synchronized void increment() {
            count++;
        }
        
        synchronized int getCount() {
            return count;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.getCount());  // 2000
    }
}
```

---

### Solution 2: Synchronized Block

```java
public class SynchronizedBlockExample {
    static class Counter {
        int count = 0;
        Object lock = new Object();
        
        void increment() {
            synchronized (lock) {  // Synchronize on specific object
                count++;
            }
        }
        
        int getCount() {
            synchronized (lock) {
                return count;
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        
        System.out.println("Count: " + counter.getCount());  // 20000
    }
}
```

---

### Solution 3: AtomicInteger (Recommended)

```java
import java.util.concurrent.atomic.*;

public class AtomicExample {
    public static void main(String[] args) throws InterruptedException {
        AtomicInteger count = new AtomicInteger(0);
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                count.incrementAndGet();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                count.incrementAndGet();
            }
        });
        
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        
        System.out.println("Count: " + count.get());  // 20000
    }
}
```

---

### Locks (More Control)

```java
import java.util.concurrent.locks.*;

public class LockExample {
    static class Counter {
        int count = 0;
        Lock lock = new ReentrantLock();
        
        void increment() {
            lock.lock();
            try {
                count++;
            } finally {
                lock.unlock();  // Must unlock in finally
            }
        }
        
        int getCount() {
            lock.lock();
            try {
                return count;
            } finally {
                lock.unlock();
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                counter.increment();
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                counter.increment();
            }
        });
        
        long start = System.nanoTime();
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        long end = System.nanoTime();
        
        System.out.println("Count: " + counter.getCount());
        System.out.println("Time: " + (end - start) / 1_000_000 + "ms");
    }
}
```

---

## 1.5 ExecutorService & Thread Pools

```java
import java.util.concurrent.*;

public class ExecutorServiceExample {
    public static void main(String[] args) {
        // Create thread pool with 3 threads
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        System.out.println("Submitting tasks...");
        
        // Submit tasks
        for (int i = 1; i <= 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " started on " + 
                                 Thread.currentThread().getName());
                try {
                    Thread.sleep(2000);  // Simulate work
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("Task " + taskId + " completed");
            });
        }
        
        System.out.println("All tasks submitted");
        
        // Shutdown executor
        executor.shutdown();
        
        // Wait for completion
        try {
            if (!executor.awaitTermination(30, TimeUnit.SECONDS)) {
                executor.shutdownNow();  // Force shutdown
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
        
        System.out.println("All tasks completed");
    }
}
```

**Output:**
```
Submitting tasks...
Task 1 started on pool-1-thread-1
Task 2 started on pool-1-thread-2
Task 3 started on pool-1-thread-3
All tasks submitted
Task 4 started on pool-1-thread-1
Task 1 completed
...
```

---

### Types of Thread Pools

```java
import java.util.concurrent.*;

public class ThreadPoolTypes {
    public static void main(String[] args) {
        // 1. Fixed Thread Pool
        ExecutorService fixedPool = Executors.newFixedThreadPool(3);
        // Best for: Known workload, preventing resource exhaustion
        
        // 2. Cached Thread Pool
        ExecutorService cachedPool = Executors.newCachedThreadPool();
        // Best for: Many short-lived tasks, elastic scaling
        
        // 3. Single Thread Executor
        ExecutorService singlePool = Executors.newSingleThreadExecutor();
        // Best for: Sequential task execution, guaranteed order
        
        // 4. Scheduled Thread Pool
        ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(2);
        // Best for: Delayed/periodic tasks
        
        // Example: Scheduled task
        scheduledPool.scheduleAtFixedRate(() -> {
            System.out.println("Running periodically");
        }, 0, 2, TimeUnit.SECONDS);  // Run immediately, then every 2 seconds
        
        // Wait before shutdown
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        scheduledPool.shutdown();
    }
}
```

---

## 1.6 Fork/Join Framework

```java
import java.util.concurrent.*;

public class ForkJoinExample extends RecursiveTask<Long> {
    private static final int THRESHOLD = 1000;
    private long[] arr;
    private int start;
    private int end;
    
    public ForkJoinExample(long[] arr, int start, int end) {
        this.arr = arr;
        this.start = start;
        this.end = end;
    }
    
    @Override
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // Base case: compute directly
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += arr[i];
            }
            return sum;
        } else {
            // Split into subtasks
            int mid = (start + end) / 2;
            ForkJoinExample left = new ForkJoinExample(arr, start, mid);
            ForkJoinExample right = new ForkJoinExample(arr, mid, end);
            
            // Fork: submit subtasks
            left.fork();
            right.fork();
            
            // Join: wait for results
            long leftResult = left.join();
            long rightResult = right.join();
            
            return leftResult + rightResult;
        }
    }
    
    public static void main(String[] args) {
        long[] arr = new long[10_000_000];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
        
        ForkJoinPool pool = ForkJoinPool.commonPool();
        
        long start = System.nanoTime();
        long result = pool.invoke(new ForkJoinExample(arr, 0, arr.length));
        long end = System.nanoTime();
        
        System.out.println("Sum: " + result);
        System.out.println("Time: " + (end - start) / 1_000_000 + "ms");
    }
}
```

---

## 1.7 CompletableFuture (Async Programming)

```java
import java.util.concurrent.*;

public class CompletableFutureExample {
    public static void main(String[] args) {
        // 1. Simple async operation
        System.out.println("=== Simple Async ===");
        CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
            System.out.println("Computing on " + Thread.currentThread().getName());
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return 42;
        });
        
        future.thenAccept(result -> {
            System.out.println("Result: " + result);
        });
        
        // 2. Chaining operations
        System.out.println("\n=== Chaining ===");
        CompletableFuture.supplyAsync(() -> "Hello")
                        .thenApply(s -> s + " World")
                        .thenApply(String::toUpperCase)
                        .thenAccept(System.out::println);  // HELLO WORLD
        
        // 3. Combining futures
        System.out.println("\n=== Combining Futures ===");
        CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 5);
        CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 3);
        
        f1.thenCombine(f2, (a, b) -> {
            System.out.println(a + " + " + b + " = " + (a + b));
            return a + b;
        });
        
        // 4. Exception handling
        System.out.println("\n=== Exception Handling ===");
        CompletableFuture.supplyAsync(() -> {
            throw new RuntimeException("Oops!");
        })
        .exceptionally(ex -> {
            System.out.println("Exception: " + ex.getMessage());
            return 0;
        });
        
        // Wait for all to complete
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 1.8 Producer-Consumer Pattern

```java
import java.util.concurrent.*;

public class ProducerConsumerExample {
    static class Buffer {
        private BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(5);
        
        void produce(int value) throws InterruptedException {
            System.out.println("Producing: " + value);
            queue.put(value);  // Blocks if queue is full
        }
        
        int consume() throws InterruptedException {
            int value = queue.take();  // Blocks if queue is empty
            System.out.println("Consuming: " + value);
            return value;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Buffer buffer = new Buffer();
        
        // Producer thread
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    buffer.produce(i);
                    Thread.sleep(500);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        // Consumer thread
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    buffer.consume();
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        producer.start();
        consumer.start();
        
        producer.join();
        consumer.join();
    }
}
```

---

## 1.9 Semaphores

```java
import java.util.concurrent.*;

public class SemaphoreExample {
    static class PooledConnection {
        private static Semaphore semaphore = new Semaphore(3);  // Max 3 concurrent
        
        void connect() throws InterruptedException {
            semaphore.acquire();
            try {
                System.out.println("Connection established on " + 
                                 Thread.currentThread().getName() + 
                                 ", Available: " + semaphore.availablePermits());
                Thread.sleep(2000);
            } finally {
                semaphore.release();
                System.out.println("Connection released");
            }
        }
    }
    
    public static void main(String[] args) {
        PooledConnection connection = new PooledConnection();
        
        // Try to connect from 5 threads
        for (int i = 0; i < 5; i++) {
            new Thread(() -> {
                try {
                    connection.connect();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

---

## Use Case: Web Server Request Handling

```java
import java.util.concurrent.*;

public class WebServerSimulation {
    static class Request {
        int id;
        Request(int id) { this.id = id; }
    }
    
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4);  // 4 worker threads
        
        // Simulate incoming requests
        for (int i = 1; i <= 10; i++) {
            final Request request = new Request(i);
            executor.submit(() -> {
                System.out.println("Processing Request " + request.id + " on " + 
                                 Thread.currentThread().getName());
                try {
                    Thread.sleep(2000);  // Simulate processing
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("Completed Request " + request.id);
            });
        }
        
        executor.shutdown();
        try {
            executor.awaitTermination(30, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

---

# ADVANCED TOPIC 2: MEMORY MANAGEMENT & JVM INTERNALS

## 2.1 Heap vs Stack Memory (Detailed)

**Stack:**
- Size: Fixed at thread creation (~1MB per thread)
- Access: Fast (pointer increment)
- Thread-safe: Each thread has own stack
- Cleanup: Automatic (function return)

**Heap:**
- Size: Configurable (-Xms, -Xmx)
- Access: Slower (pointer following)
- Thread-safe: Shared, needs synchronization
- Cleanup: Garbage collection

```java
public class HeapStackComparison {
    public static void main(String[] args) {
        // Stack: local variables
        int age = 25;           // Stack
        double salary = 50000;  // Stack
        
        // Heap: objects
        String name = new String("Pushpendra");  // Reference on stack, object on heap
        int[] arr = {1, 2, 3};                   // Reference on stack, array on heap
        
        Person person = new Person("Rahul");     // Reference on stack, object on heap
        
        // Stack memory diagram
        /*
        Stack (Thread Stack):
        ┌────────────────┐
        │ age: 25        │
        │ salary: 50000  │
        │ name: 0x2F3D   │─────┐
        │ arr: 0x4F5E    │──┐  │
        │ person: 0x5F6E│─┐│  │
        └────────────────┘ ││  │
                          ││  │
        Heap:             ││  │
        ┌──────────────────────────────┐
        │ 0x4F5E: [1,2,3]              │
        │ 0x5F6E: Person object        │
        │ 0x2F3D: String("Pushpendra") │
        └──────────────────────────────┘
        */
    }
}

class Person {
    String name;
    Person(String name) { this.name = name; }
}
```

---

## 2.2 Garbage Collection Algorithms (Detailed)

### Serial GC
```
Compact everything:
Before: [Live | Dead | Live | Dead | Dead | Live]
After:  [Live | Live | Live | Empty | Empty | Empty]

Time: ~500ms for 1GB heap
Use: Single-threaded apps, small heaps
```

### Parallel GC
```
Multiple threads collecting garbage simultaneously
Heap divided into regions, each processed by thread

Time: ~100ms for 1GB heap
Use: Multi-core systems, throughput-focused
```

### G1 GC
```
Divides heap into 2048 equal regions

Before:
┌───┬───┬───┬───┬───┬───┬───┬───┐
│ Y │ O │ Y │ O │ O │ Y │ O │ Y │
└───┴───┴───┴───┴───┴───┴───┴───┘

First collect regions with most dead objects:
┌───┬───┬───┬───┬───┬───┬───┬───┐
│ Y │ - │ Y │ - │ - │ Y │ O │ Y │  (Empty regions reused)
└───┴───┴───┴───┴───┴───┴───┴───┘

Time: Predictable pause time (~200ms)
Use: Large heaps (>4GB), latency-sensitive apps
```

### ZGC
```
Doesn't stop application
Uses colored pointers and load barriers

Pause time: <10ms always
Use: Large heaps (>10GB), ultra-low latency
Overhead: 15-30% CPU
```

---

```java
public class GCTuningExample {
    public static void main(String[] args) {
        // Monitor GC performance
        Runtime runtime = Runtime.getRuntime();
        
        long beforeGC = runtime.totalMemory() - runtime.freeMemory();
        System.out.println("Memory before: " + beforeGC / 1024 / 1024 + "MB");
        
        // Create garbage
        byte[][] data = new byte[100000][];
        for (int i = 0; i < 100000; i++) {
            data[i] = new byte[1024];  // 1KB each
        }
        
        long beforeCollection = runtime.totalMemory() - runtime.freeMemory();
        System.out.println("Memory peak: " + beforeCollection / 1024 / 1024 + "MB");
        
        // Discard references
        data = null;
        
        // Force GC (educational purposes only)
        System.gc();
        
        try {
            Thread.sleep(1000);  // Allow GC time
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        long afterGC = runtime.totalMemory() - runtime.freeMemory();
        System.out.println("Memory after: " + afterGC / 1024 / 1024 + "MB");
    }
}
```

---

## 2.3 JVM Tuning Parameters

```bash
# Heap size tuning
# -Xms: Initial heap size
# -Xmx: Maximum heap size
java -Xms512m -Xmx2048m MyApp

# GC selection
java -XX:+UseG1GC MyApp              # G1 GC
java -XX:+UseParallelGC MyApp        # Parallel GC
java -XX:+UseSerialGC MyApp          # Serial GC
java -XX:+UseZGC MyApp               # ZGC (Java 11+)

# GC logging
java -Xloggc:gc.log \
     -XX:+PrintGCDetails \
     -XX:+PrintGCTimeStamps \
     MyApp

# Metaspace tuning
java -XX:MetaspaceSize=256m \
     -XX:MaxMetaspaceSize=512m \
     MyApp

# Thread stack size
java -Xss1m MyApp  # 1MB stack per thread

# Young generation size
java -XX:NewRatio=2 MyApp  # Heap = 1:2 (young:old)
```

---

## 2.4 ClassLoader Mechanism (Deep Dive)

```java
import java.lang.reflect.Field;

public class ClassLoaderDeepDive {
    public static void main(String[] args) throws Exception {
        // 1. Get ClassLoader hierarchy
        ClassLoader appCL = ClassLoaderDeepDive.class.getClassLoader();
        ClassLoader extCL = appCL.getParent();
        ClassLoader bootCL = extCL.getParent();
        
        System.out.println("App ClassLoader: " + appCL);
        System.out.println("Extension ClassLoader: " + extCL);
        System.out.println("Bootstrap ClassLoader: " + bootCL);  // null
        
        // 2. ClassLoader delegation model
        /*
        When loading a class:
        1. Ask parent (Extension)
        2. Extension asks parent (Bootstrap)
        3. Bootstrap checks its own paths
        4. If not found, Extension checks
        5. If not found, Application checks
        6. If not found, throw ClassNotFoundException
        */
        
        // 3. Dynamic class loading
        Class<?> stringClass = Class.forName("java.lang.String");
        System.out.println("String loader: " + stringClass.getClassLoader());
    }
}
```

---

## 2.5 JIT Compiler Optimizations

```java
public class JITOptimizations {
    // Hotspot detection and optimization
    static long computeSum(int n) {
        long sum = 0;
        for (int i = 0; i < n; i++) {
            sum += i;
        }
        return sum;
    }
    
    public static void main(String[] args) {
        // First 10,000 calls: Interpreted
        for (int i = 0; i < 10000; i++) {
            computeSum(10000);
        }
        
        // After threshold: JIT compiled to native code
        long start = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            computeSum(10000);
        }
        long end = System.nanoTime();
        
        System.out.println("Time: " + (end - start) / 1_000_000 + "ms");
        
        /*
        JIT optimizations:
        1. Method inlining: Replace method call with actual code
        2. Dead code elimination: Remove unreachable code
        3. Loop unrolling: Reduce loop iterations
        4. Escape analysis: Stack allocation instead of heap
        5. Speculative optimization: Assume patterns for fast path
        */
    }
}
```

---

# ADVANCED TOPIC 3: ADVANCED I/O & NIO.2

## 3.1 Channels & Buffers (Non-blocking I/O)

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.*;

public class NIOExample {
    public static void main(String[] args) throws IOException {
        // 1. FileChannel with ByteBuffer
        RandomAccessFile file = new RandomAccessFile("data.txt", "rw");
        FileChannel channel = file.getChannel();
        
        // Allocate buffer
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        
        // Read from channel into buffer
        int bytesRead = channel.read(buffer);
        System.out.println("Bytes read: " + bytesRead);
        
        // Switch from write to read mode
        buffer.flip();
        
        // Read from buffer
        while (buffer.hasRemaining()) {
            System.out.print((char) buffer.get());
        }
        
        // Clear buffer for reuse
        buffer.clear();
        
        // Write to buffer
        buffer.put("New data".getBytes());
        buffer.flip();
        channel.write(buffer);
        
        channel.close();
        file.close();
    }
}
```

---

## 3.2 Selectors (Multiplexed I/O)

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.*;
import java.util.Iterator;
import java.util.Set;

public class SelectorExample {
    public static void main(String[] args) throws IOException {
        // Create selector
        Selector selector = Selector.open();
        
        // Create server channel
        ServerSocketChannel serverChannel = ServerSocketChannel.open();
        serverChannel.bind(new java.net.InetSocketAddress(8080));
        serverChannel.configureBlocking(false);
        
        // Register with selector
        serverChannel.register(selector, SelectionKey.OP_ACCEPT);
        
        System.out.println("Server started on port 8080");
        
        while (true) {
            // Wait for events
            int readyChannels = selector.select();
            
            if (readyChannels == 0) {
                continue;
            }
            
            // Get ready channels
            Set<SelectionKey> selectedKeys = selector.selectedKeys();
            Iterator<SelectionKey> iterator = selectedKeys.iterator();
            
            while (iterator.hasNext()) {
                SelectionKey key = iterator.next();
                
                if (key.isAcceptable()) {
                    // Accept new connection
                    SocketChannel socketChannel = serverChannel.accept();
                    socketChannel.configureBlocking(false);
                    socketChannel.register(selector, SelectionKey.OP_READ);
                    System.out.println("New client connected");
                }
                else if (key.isReadable()) {
                    // Read data
                    SocketChannel socketChannel = (SocketChannel) key.channel();
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    socketChannel.read(buffer);
                    System.out.println("Data: " + new String(buffer.array()));
                }
                
                iterator.remove();
            }
        }
    }
}
```

---

## 3.3 Memory-Mapped Files

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.FileChannel;
import java.nio.file.*;

public class MemoryMappedFileExample {
    public static void main(String[] args) throws IOException {
        // Create file
        Path path = Paths.get("largefile.dat");
        
        // Open file for reading and writing
        RandomAccessFile file = new RandomAccessFile(path.toFile(), "rw");
        FileChannel channel = file.getChannel();
        
        // Map entire file into memory
        long fileSize = channel.size();
        MappedByteBuffer buffer = channel.map(
            FileChannel.MapMode.READ_WRITE, 
            0, 
            fileSize
        );
        
        // Read/write directly from mapped memory
        buffer.put("Data at position 0".getBytes());
        
        // Read data
        buffer.position(0);
        byte[] data = new byte[19];
        buffer.get(data);
        System.out.println(new String(data));
        
        channel.close();
        file.close();
    }
}
```

---

## 3.4 File Walking & WatchService

```java
import java.nio.file.*;
import java.nio.file.attribute.*;

public class FileWatchServiceExample {
    public static void main(String[] args) throws IOException, InterruptedException {
        // Create WatchService
        WatchService watchService = FileSystems.getDefault().newWatchService();
        
        // Register directory
        Path dir = Paths.get(".");
        dir.register(watchService, 
                    StandardWatchEventKinds.ENTRY_CREATE,
                    StandardWatchEventKinds.ENTRY_DELETE,
                    StandardWatchEventKinds.ENTRY_MODIFY);
        
        System.out.println("Watching directory: " + dir);
        
        while (true) {
            // Wait for events
            WatchKey key = watchService.take();
            
            // Process events
            for (WatchEvent<?> event : key.pollEvents()) {
                WatchEvent.Kind<?> kind = event.kind();
                Path filename = (Path) event.context();
                
                System.out.println("Event: " + kind + " on " + filename);
            }
            
            // Reset key
            if (!key.reset()) {
                break;  // Key no longer valid
            }
        }
    }
}
```

---

## 3.5 File Walking

```java
import java.nio.file.*;
import java.nio.file.attribute.BasicFileAttributes;
import java.io.IOException;

public class FileWalkingExample {
    public static void main(String[] args) throws IOException {
        // Walk directory tree
        Files.walk(Paths.get("."))
             .filter(Files::isRegularFile)
             .filter(path -> path.toString().endsWith(".java"))
             .forEach(System.out::println);
        
        System.out.println("\n=== Large files ===");
        
        // Find files larger than 1MB
        Files.walk(Paths.get("."))
             .filter(Files::isRegularFile)
             .filter(path -> {
                 try {
                     return Files.size(path) > 1_000_000;
                 } catch (IOException e) {
                     return false;
                 }
             })
             .forEach(System.out::println);
    }
}
```

---

# ADVANCED TOPIC 4: REFLECTION & ANNOTATIONS

## 4.1 Reflection API

```java
import java.lang.reflect.*;

public class ReflectionExample {
    static class Student {
        private String name;
        private int age;
        
        public Student(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        public String getName() { return name; }
        public int getAge() { return age; }
        
        public void display() {
            System.out.println("Name: " + name + ", Age: " + age);
        }
    }
    
    public static void main(String[] args) throws Exception {
        // 1. Get class object
        Class<?> clazz = Class.forName("ReflectionExample$Student");
        System.out.println("Class: " + clazz.getName());
        
        // 2. Get constructors
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        System.out.println("\nConstructors:");
        for (Constructor<?> c : constructors) {
            System.out.println("  " + c);
        }
        
        // 3. Get methods
        Method[] methods = clazz.getDeclaredMethods();
        System.out.println("\nMethods:");
        for (Method m : methods) {
            System.out.println("  " + m.getName());
        }
        
        // 4. Get fields
        Field[] fields = clazz.getDeclaredFields();
        System.out.println("\nFields:");
        for (Field f : fields) {
            System.out.println("  " + f.getName() + " (" + f.getType().getSimpleName() + ")");
        }
        
        // 5. Create instance using reflection
        Constructor<?> constructor = clazz.getConstructor(String.class, int.class);
        Object obj = constructor.newInstance("Pushpendra", 22);
        
        // 6. Call method using reflection
        Method displayMethod = clazz.getMethod("display");
        displayMethod.invoke(obj);
        
        // 7. Access field
        Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true);
        System.out.println("Name field value: " + nameField.get(obj));
    }
}
```

---

## 4.2 Custom Annotations

```java
import java.lang.annotation.*;
import java.lang.reflect.Method;

// 1. Define custom annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface TestMethod {
    String description() default "";
    int timeout() default 1000;
}

// 2. Define annotation for fields
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Validate {
    String pattern() default "";
    String message() default "";
}

// 3. Use annotations
class UserValidator {
    @Validate(pattern = "^[a-zA-Z]+$", message = "Only letters allowed")
    String name;
    
    @Validate(pattern = "^\\d{3}-\\d{4}$", message = "Format: XXX-XXXX")
    String phone;
    
    @TestMethod(description = "Test user creation", timeout = 5000)
    void testCreateUser() {
        System.out.println("Testing user creation...");
    }
}

// 4. Process annotations at runtime
public class AnnotationProcessing {
    public static void main(String[] args) {
        Class<?> clazz = UserValidator.class;
        
        // Process field annotations
        System.out.println("=== Field Validations ===");
        for (java.lang.reflect.Field field : clazz.getDeclaredFields()) {
            if (field.isAnnotationPresent(Validate.class)) {
                Validate validate = field.getAnnotation(Validate.class);
                System.out.println(field.getName() + ":");
                System.out.println("  Pattern: " + validate.pattern());
                System.out.println("  Message: " + validate.message());
            }
        }
        
        // Process method annotations
        System.out.println("\n=== Test Methods ===");
        for (Method method : clazz.getDeclaredMethods()) {
            if (method.isAnnotationPresent(TestMethod.class)) {
                TestMethod testMethod = method.getAnnotation(TestMethod.class);
                System.out.println(method.getName() + ":");
                System.out.println("  Description: " + testMethod.description());
                System.out.println("  Timeout: " + testMethod.timeout() + "ms");
            }
        }
    }
}
```

---

## 4.3 Annotation Processor Pattern

```java
import java.lang.annotation.*;
import java.lang.reflect.Field;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.CLASS)
public @interface Entity {
    String name() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Column {
    String name();
}

@Entity(name = "users_table")
class User {
    @Column(name = "user_id")
    int id;
    
    @Column(name = "user_name")
    String name;
    
    @Column(name = "user_email")
    String email;
}

// Annotation processor (like Hibernate does)
public class ORMProcessor {
    static void processEntity(Class<?> clazz) {
        if (!clazz.isAnnotationPresent(Entity.class)) {
            return;
        }
        
        Entity entity = clazz.getAnnotation(Entity.class);
        String tableName = entity.name().isEmpty() ? 
                          clazz.getSimpleName() : entity.name();
        
        StringBuilder sql = new StringBuilder();
        sql.append("CREATE TABLE ").append(tableName).append(" (");
        
        Field[] fields = clazz.getDeclaredFields();
        for (int i = 0; i < fields.length; i++) {
            Field field = fields[i];
            if (field.isAnnotationPresent(Column.class)) {
                Column column = field.getAnnotation(Column.class);
                sql.append(column.name()).append(" ");
                sql.append(field.getType().getSimpleName());
                
                if (i < fields.length - 1) {
                    sql.append(", ");
                }
            }
        }
        
        sql.append(")");
        System.out.println("Generated SQL: " + sql.toString());
    }
    
    public static void main(String[] args) {
        processEntity(User.class);
        // Output: CREATE TABLE users_table (user_id int, user_name String, user_email String)
    }
}
```

---

# ADVANCED TOPIC 5: GENERICS (ADVANCED)

## 5.1 Type Erasure

```java
import java.util.*;

public class TypeErasureExample {
    public static void main(String[] args) {
        // At compile-time: Type information preserved
        List<String> stringList = new ArrayList<String>();
        List<Integer> intList = new ArrayList<Integer>();
        
        // At runtime: Type information erased
        System.out.println("String list class: " + stringList.getClass());  
        // java.util.ArrayList

        System.out.println("Int list class: " + intList.getClass());        
        // java.util.ArrayList (SAME!)
        
        System.out.println("Are classes equal? " + 
                         stringList.getClass().equals(intList.getClass()));
        // true
        
        // This is why generic type information is lost at runtime:
        /*
        Compile-time:
        List<String> → Type checking done
        List<Integer> → Type checking done
        
        Runtime:
        List → Both become List (type erased)
        No runtime type checking for generics
        */
    }
}
```

---

## 5.2 Bounded Wildcards - PECS Principle

```java
import java.util.*;

public class PECSExample {
    // Producer Extends (Read-only)
    static void printNumbers(List<? extends Number> list) {
        for (Number num : list) {
            System.out.println(num);
        }
        // Can read, but cannot add (except null)
        // list.add(10);  // Compile error
    }
    
    // Consumer Super (Write-only)
    static void addNumbers(List<? super Integer> list) {
        list.add(10);
        list.add(20);
        list.add(30);
        
        // Can add, but cannot reliably read
        // Integer n = list.get(0);  // Compile error (type unknown)
    }
    
    public static void main(String[] args) {
        // Produce: Reading from subtypes
        List<Integer> intList = Arrays.asList(1, 2, 3);
        printNumbers(intList);
        
        // Consume: Writing to supertypes
        List<Number> numList = new ArrayList<>();
        addNumbers(numList);
        System.out.println("Added to numList: " + numList);
    }
}
```

---

## 5.3 Generic Methods & Classes

```java
public class GenericAdvanced {
    // Generic class with type variable
    static class Pair<K, V> {
        K key;
        V value;
        
        Pair(K key, V value) {
            this.key = key;
            this.value = value;
        }
        
        public K getKey() { return key; }
        public V getValue() { return value; }
    }
    
    // Generic method
    static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.println(element);
        }
    }
    
    // Generic method with bounded type
    static <T extends Number> double toDouble(T number) {
        return number.doubleValue();
    }
    
    // Generic method with multiple bounds
    static <T extends Number & Comparable<T>> T max(T a, T b) {
        return a.compareTo(b) > 0 ? a : b;
    }
    
    public static void main(String[] args) {
        // Using generic class
        Pair<String, Integer> pair = new Pair<>("Age", 25);
        System.out.println(pair.getKey() + ": " + pair.getValue());
        
        // Using generic method
        Integer[] ints = {1, 2, 3};
        printArray(ints);
        
        String[] strs = {"A", "B", "C"};
        printArray(strs);
        
        // Bounded generic method
        System.out.println("Double: " + toDouble(5));
        System.out.println("Max: " + max(10, 20));
    }
}
```

---

# ADVANCED TOPIC 6: DESIGN PATTERNS & SOLID (EXTENDED)

## 6.1 Builder Pattern

```java
public class BuilderPattern {
    static class House {
        String foundation;
        String walls;
        String roof;
        String windows;
        String doors;
        
        // Private constructor
        private House(HouseBuilder builder) {
            this.foundation = builder.foundation;
            this.walls = builder.walls;
            this.roof = builder.roof;
            this.windows = builder.windows;
            this.doors = builder.doors;
        }
        
        @Override
        public String toString() {
            return "House{" +
                    "foundation='" + foundation + '\'' +
                    ", walls='" + walls + '\'' +
                    ", roof='" + roof + '\'' +
                    ", windows='" + windows + '\'' +
                    ", doors='" + doors + '\'' +
                    '}';
        }
        
        // Builder class
        static class HouseBuilder {
            String foundation;
            String walls;
            String roof;
            String windows;
            String doors;
            
            HouseBuilder withFoundation(String foundation) {
                this.foundation = foundation;
                return this;
            }
            
            HouseBuilder withWalls(String walls) {
                this.walls = walls;
                return this;
            }
            
            HouseBuilder withRoof(String roof) {
                this.roof = roof;
                return this;
            }
            
            HouseBuilder withWindows(String windows) {
                this.windows = windows;
                return this;
            }
            
            HouseBuilder withDoors(String doors) {
                this.doors = doors;
                return this;
            }
            
            House build() {
                return new House(this);
            }
        }
    }
    
    public static void main(String[] args) {
        House house = new House.HouseBuilder()
                        .withFoundation("Concrete")
                        .withWalls("Brick")
                        .withRoof("Tile")
                        .withWindows("Wood")
                        .withDoors("Metal")
                        .build();
        
        System.out.println(house);
    }
}
```

**Use Case:** Complex objects with many optional parameters (e.g., HTTP requests, configurations).

---

## 6.2 Proxy Pattern

```java
interface Image {
    void display();
}

class RealImage implements Image {
    String filename;
    
    RealImage(String filename) {
        this.filename = filename;
        loadImage();
    }
    
    void loadImage() {
        System.out.println("Loading " + filename + " (expensive operation)");
    }
    
    @Override
    public void display() {
        System.out.println("Displaying " + filename);
    }
}

class ProxyImage implements Image {
    String filename;
    RealImage realImage;
    
    ProxyImage(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);  // Lazy loading
        }
        realImage.display();
    }
}

public class ProxyPatternExample {
    public static void main(String[] args) {
        Image image = new ProxyImage("photo.jpg");
        
        System.out.println("Image created (not loaded yet)");
        
        // First display: loads image
        image.display();
        
        // Second display: already loaded
        image.display();
    }
}
```

**Use Case:** Lazy loading, access control, logging, caching.

---

# ADVANCED TOPIC 7: JAVA NETWORKING

## 7.1 TCP Client-Server

```java
import java.io.*;
import java.net.*;

// Server
public class EchoServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8080);
        System.out.println("Server listening on port 8080...");
        
        while (true) {
            Socket socket = serverSocket.accept();
            System.out.println("Client connected from " + socket.getInetAddress());
            
            BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream())
            );
            PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
            
            String message;
            while ((message = in.readLine()) != null) {
                System.out.println("Received: " + message);
                out.println("Echo: " + message);
            }
            
            socket.close();
        }
    }
}

// Client
public class EchoClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("localhost", 8080);
        System.out.println("Connected to server");
        
        BufferedReader in = new BufferedReader(
            new InputStreamReader(socket.getInputStream())
        );
        PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
        BufferedReader userInput = new BufferedReader(
            new InputStreamReader(System.in)
        );
        
        String input;
        while ((input = userInput.readLine()) != null) {
            out.println(input);
            String response = in.readLine();
            System.out.println("Server: " + response);
        }
        
        socket.close();
    }
}
```

---

## 7.2 HTTP Client

```java
import java.net.*;
import java.io.*;

public class HttpClientExample {
    public static void main(String[] args) throws Exception {
        // Create URL
        URL url = new URL("https://api.github.com/users/torvalds");
        
        // Open connection
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Accept", "application/json");
        
        // Get response
        int responseCode = connection.getResponseCode();
        System.out.println("Response Code: " + responseCode);
        
        BufferedReader in = new BufferedReader(
            new InputStreamReader(connection.getInputStream())
        );
        
        String line;
        StringBuilder response = new StringBuilder();
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();
        
        System.out.println("Response: " + response.toString());
    }
}
```

---

## 7.3 UDP (Datagram)

```java
import java.net.*;

public class UDPSender {
    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket();
        
        String message = "Hello, UDP!";
        byte[] buffer = message.getBytes();
        
        InetAddress address = InetAddress.getByName("localhost");
        DatagramPacket packet = new DatagramPacket(buffer, buffer.length, address, 9876);
        
        socket.send(packet);
        socket.close();
    }
}

public class UDPReceiver {
    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket(9876);
        
        byte[] buffer = new byte[1024];
        DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
        
        socket.receive(packet);
        
        String message = new String(packet.getData(), 0, packet.getLength());
        System.out.println("Received: " + message);
        
        socket.close();
    }
}
```

---

# ADVANCED TOPIC 8: SECURITY IN JAVA

## 8.1 Encryption/Decryption (AES)

```java
import javax.crypto.Cipher;
import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;
import java.util.Base64;

public class AESEncryptionExample {
    public static void main(String[] args) throws Exception {
        // 1. Generate AES key
        KeyGenerator keyGen = KeyGenerator.getInstance("AES");
        keyGen.init(256);  // 256-bit key
        SecretKey key = keyGen.generateKey();
        
        // 2. Create cipher
        Cipher cipher = Cipher.getInstance("AES");
        
        // 3. Encrypt
        String plaintext = "Secret message";
        cipher.init(Cipher.ENCRYPT_MODE, key);
        byte[] encrypted = cipher.doFinal(plaintext.getBytes());
        
        String encodedEncrypted = Base64.getEncoder().encodeToString(encrypted);
        System.out.println("Encrypted: " + encodedEncrypted);
        
        // 4. Decrypt
        cipher.init(Cipher.DECRYPT_MODE, key);
        byte[] decrypted = cipher.doFinal(encrypted);
        String decryptedText = new String(decrypted);
        
        System.out.println("Decrypted: " + decryptedText);
    }
}
```

---

## 8.2 Message Digest (Hash)

```java
import java.security.MessageDigest;
import java.util.Base64;

public class MessageDigestExample {
    public static void main(String[] args) throws Exception {
        String input = "password123";
        
        // SHA-256
        MessageDigest sha256 = MessageDigest.getInstance("SHA-256");
        byte[] hash = sha256.digest(input.getBytes());
        
        String encodedHash = Base64.getEncoder().encodeToString(hash);
        System.out.println("SHA-256: " + encodedHash);
        
        // MD5 (not recommended for security)
        MessageDigest md5 = MessageDigest.getInstance("MD5");
        byte[] md5Hash = md5.digest(input.getBytes());
        String encodedMD5 = Base64.getEncoder().encodeToString(md5Hash);
        System.out.println("MD5: " + encodedMD5);
    }
}
```

---

## 8.3 Spring Security Example

```java
// Spring Security Configuration
/*
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/public/**").permitAll()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .permitAll();
    }
    
    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        return username -> {
            if ("user".equals(username)) {
                return new org.springframework.security.core.userdetails.User(
                    "user",
                    "{noop}password",  // No password encoding
                    AuthorityUtils.createAuthorityList("ROLE_USER")
                );
            }
            throw new UsernameNotFoundException("User not found");
        };
    }
}
*/
```

---

# ADVANCED TOPIC 9: JAVA EE / JAKARTA EE

## 9.1 Servlets

```java
import javax.servlet.*;
import javax.servlet.http.*;

/*
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        response.setContentType("text/html");
        
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello, World!</h1>");
        out.println("</body></html>");
    }
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        String name = request.getParameter("name");
        response.setContentType("text/html");
        
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello, " + name + "!</h1>");
        out.println("</body></html>");
    }
}
*/
```

---

## 9.2 JPA/Hibernate ORM

```java
/*
@Entity
@Table(name = "employees")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    
    @Column(name = "emp_name")
    private String name;
    
    @Column(name = "emp_salary")
    private double salary;
    
    @ManyToOne
    @JoinColumn(name = "dept_id")
    private Department department;
    
    // Getters and setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
    
    public Department getDepartment() { return department; }
    public void setDepartment(Department department) { this.department = department; }
}

@Entity
@Table(name = "departments")
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    
    @Column(name = "dept_name")
    private String name;
    
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL)
    private List<Employee> employees;
    
    // Getters and setters
}
*/
```

---

## 9.3 Spring Boot REST API

```java
/*
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {
    
    @Autowired
    private EmployeeRepository repository;
    
    @GetMapping
    public List<Employee> getAllEmployees() {
        return repository.findAll();
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<Employee> getEmployeeById(@PathVariable int id) {
        Optional<Employee> employee = repository.findById(id);
        return employee.map(ResponseEntity::ok)
                      .orElseGet(() -> ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public ResponseEntity<Employee> createEmployee(@RequestBody Employee employee) {
        Employee saved = repository.save(employee);
        return ResponseEntity.status(HttpStatus.CREATED).body(saved);
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<Employee> updateEmployee(@PathVariable int id, 
                                                   @RequestBody Employee employee) {
        if (!repository.existsById(id)) {
            return ResponseEntity.notFound().build();
        }
        employee.setId(id);
        Employee updated = repository.save(employee);
        return ResponseEntity.ok(updated);
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteEmployee(@PathVariable int id) {
        if (!repository.existsById(id)) {
            return ResponseEntity.notFound().build();
        }
        repository.deleteById(id);
        return ResponseEntity.noContent().build();
    }
}

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    List<Employee> findByDepartmentId(int departmentId);
    Employee findByName(String name);
}
*/
```

---

# ADVANCED TOPIC 10: MODULES (JAVA 9+)

## 10.1 Module System

```java
/*
File: src/module-info.java

module com.example.app {
    requires java.base;
    requires java.sql;
    requires java.logging;
    
    requires transitive com.example.lib;
    
    exports com.example.app.api;
    exports com.example.app.util to com.example.test;
    
    opens com.example.app.impl;  // Allows reflection
}
*/
```

---

# ADVANCED TOPIC 11: REACTIVE & FUNCTIONAL PROGRAMMING

## 11.1 Reactive Streams (Flow API - Java 9+)

```java
import java.util.concurrent.*;

public class ReactiveStreamExample {
    // Publisher interface (Java 9 Flow API)
    static class NumberPublisher implements java.util.concurrent.Flow.Publisher<Integer> {
        @Override
        public void subscribe(java.util.concurrent.Flow.Subscriber<? super Integer> subscriber) {
            subscriber.onSubscribe(new Subscription() {
                int count = 0;
                
                @Override
                public void request(long n) {
                    for (int i = 0; i < n && count < 10; i++) {
                        subscriber.onNext(count++);
                    }
                    if (count >= 10) {
                        subscriber.onComplete();
                    }
                }
                
                @Override
                public void cancel() {}
            });
        }
    }
    
    // Subscriber
    static class NumberSubscriber implements java.util.concurrent.Flow.Subscriber<Integer> {
        @Override
        public void onSubscribe(java.util.concurrent.Flow.Subscription subscription) {
            subscription.request(10);
        }
        
        @Override
        public void onNext(Integer item) {
            System.out.println("Received: " + item);
        }
        
        @Override
        public void onError(Throwable throwable) {
            System.err.println("Error: " + throwable.getMessage());
        }
        
        @Override
        public void onComplete() {
            System.out.println("Stream complete");
        }
    }
    
    public static void main(String[] args) {
        NumberPublisher publisher = new NumberPublisher();
        NumberSubscriber subscriber = new NumberSubscriber();
        
        publisher.subscribe(subscriber);
    }
}
```

---

## 11.2 Project Reactor Example

```java
/*
// Add dependency: io.projectreactor:reactor-core

import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;
import java.time.Duration;

public class ReactorExample {
    public static void main(String[] args) {
        // Flux: Multiple values
        Flux<Integer> numbers = Flux.range(1, 5)
                                    .map(n -> n * 2)
                                    .filter(n -> n > 5);
        
        numbers.subscribe(System.out::println);
        // Output: 6, 8, 10
        
        // Mono: Single value
        Mono<String> message = Mono.just("Hello Reactor")
                                   .map(String::toUpperCase);
        
        message.subscribe(System.out::println);
        // Output: HELLO REACTOR
        
        // Async with delay
        Flux<Integer> delayed = Flux.range(1, 3)
                                    .delayElement(Duration.ofSeconds(1));
        
        delayed.subscribe(System.out::println);
        
        try {
            Thread.sleep(4000);  // Wait for async operations
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
*/
```

---

# ADVANCED TOPIC 12: TESTING & BUILD TOOLS

## 12.1 JUnit 5

```java
/*
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import static org.junit.jupiter.api.Assertions.*;

@DisplayName("Calculator Tests")
class CalculatorTest {
    private Calculator calculator;
    
    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }
    
    @Test
    @DisplayName("Should add two numbers correctly")
    void testAddition() {
        int result = calculator.add(5, 3);
        assertEquals(8, result);
    }
    
    @Test
    @DisplayName("Should multiply two numbers correctly")
    void testMultiplication() {
        int result = calculator.multiply(4, 5);
        assertEquals(20, result);
    }
    
    @Test
    @DisplayName("Should throw exception on division by zero")
    void testDivisionByZero() {
        assertThrows(IllegalArgumentException.class, () -> {
            calculator.divide(10, 0);
        });
    }
}

public class Calculator {
    public int add(int a, int b) { return a + b; }
    public int multiply(int a, int b) { return a * b; }
    public int divide(int a, int b) {
        if (b == 0) throw new IllegalArgumentException("Cannot divide by zero");
        return a / b;
    }
}
*/
```

---

## 12.2 Mockito

```java
/*
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import static org.mockito.Mockito.*;

class UserServiceTest {
    @Test
    void testGetUser() {
        UserRepository mockRepo = mock(UserRepository.class);
        User user = new User(1, "John");
        
        when(mockRepo.findById(1)).thenReturn(user);
        
        UserService service = new UserService(mockRepo);
        User result = service.getUser(1);
        
        assertEquals("John", result.getName());
        verify(mockRepo, times(1)).findById(1);
    }
}

class UserService {
    private UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
    
    public User getUser(int id) {
        return repository.findById(id);
    }
}

interface UserRepository {
    User findById(int id);
}

class User {
    int id;
    String name;
    
    User(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    public String getName() { return name; }
}
*/
```

---

## 12.3 Maven

```xml
<!-- pom.xml -->
<!--
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0</version>
    
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
    
    <dependencies>
        <!-- JUnit 5 -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.9.2</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Mockito -->
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <version>5.2.0</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Slf4j for logging -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>2.0.5</version>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.10.1</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
-->
```

---

## 12.4 Gradle

```gradle
// build.gradle
/*
plugins {
    id 'java'
}

group = 'com.example'
version = '1.0'
sourceCompatibility = '11'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.2'
    testImplementation 'org.mockito:mockito-core:5.2.0'
    implementation 'org.slf4j:slf4j-api:2.0.5'
}

test {
    useJUnitPlatform()
}
*/
```

---

## 12.5 Common Maven Commands

```bash
# Create new project
mvn archetype:generate -DgroupId=com.example -DartifactId=my-app

# Compile
mvn clean compile

# Run tests
mvn test

# Package
mvn clean package

# Install to local repo
mvn install

# Deploy
mvn deploy

# Skip tests
mvn clean package -DskipTests

# Run specific test
mvn test -Dtest=CalculatorTest
```

---

# QUICK REFERENCE

## Thread States Transition

```
NEW → RUNNABLE → BLOCKED/WAITING → RUNNABLE → TERMINATED
```

## Atomic Operations

```
AtomicInteger, AtomicLong, AtomicReference, AtomicBoolean
AtomicIntegerArray, AtomicReferenceArray
```

## Concurrency Collections

```
ConcurrentHashMap, ConcurrentLinkedQueue, BlockingQueue
CopyOnWriteArrayList, ConcurrentSkipListMap
```

## CompleteableFuture Methods

```
supplyAsync()      - Async with return value
thenApply()        - Transform result
thenCombine()      - Combine two futures
thenCompose()      - Chain dependent futures
exceptionally()    - Exception handling
whenComplete()     - Always execute after completion
```

## Memory Tuning Summary

```
Young Generation: Default 1/3 of heap
Old Generation:   Default 2/3 of heap

GC Pause time inversely proportional to throughput
Larger heap → Longer pause time
Smaller heap → Shorter pause time, more GC cycles
```

---

# END OF ADVANCED JAVA MASTERY GUIDE

**This comprehensive guide covers:**
✓ Java Fundamentals (1-22 sections from basic guide)
✓ Concurrency & Multithreading (Thread lifecycle, synchronization, ExecutorService, Fork/Join, CompletableFuture)
✓ Memory Management & JVM Internals (Heap/Stack, GC algorithms, ClassLoader, JIT optimization)
✓ Advanced I/O & NIO.2 (Channels, Selectors, Memory-mapped files, WatchService)
✓ Reflection & Annotations (Reflection API, custom annotations, annotation processing)
✓ Advanced Generics (Type erasure, bounded wildcards, PECS principle)
✓ Design Patterns (Builder, Proxy, and previous patterns)
✓ Java Networking (TCP/UDP, HTTP clients, RMI)
✓ Security (Encryption, message digests, Spring Security)
✓ Java EE/Jakarta EE (Servlets, JPA/Hibernate, Spring Boot)
✓ Module System (Java 9+)
✓ Reactive Programming (Flow API, Project Reactor, RxJava patterns)
✓ Testing & Build Tools (JUnit, Mockito, Maven, Gradle)

**Ready for:**
- Advanced Java interviews
- Backend service development
- Microservices architecture
- High-performance applications
- Enterprise system design
