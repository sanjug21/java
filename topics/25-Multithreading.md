# 25. MULTITHREADING

Multithreading enables concurrent execution of multiple tasks within a single process. Java provides built-in threading support through the Thread class and comprehensive synchronization mechanisms.

---

## THREAD BASICS

### Creating Threads

**Two main approaches: extending Thread class or implementing Runnable**

```java
// Approach 1: Extend Thread class
class MyThread extends Thread {
    private String name;
    
    public MyThread(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + " - Count: " + i);
            try {
                Thread.sleep(1000);  // Sleep for 1 second
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}

// Usage
MyThread t1 = new MyThread("Thread-1");
MyThread t2 = new MyThread("Thread-2");
t1.start();  // Starts new thread
t2.start();  // Starts another new thread
// Both run concurrently

// Approach 2: Implement Runnable (preferred)
class MyRunnable implements Runnable {
    private String name;
    
    public MyRunnable(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(name + " - Count: " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}

// Usage - Preferred because allows extending other classes
Thread t1 = new Thread(new MyRunnable("Thread-1"));
Thread t2 = new Thread(new MyRunnable("Thread-2"));
t1.start();
t2.start();

// Lambda expression (Java 8+)
Thread t3 = new Thread(() -> {
    for (int i = 0; i < 5; i++) {
        System.out.println("Thread-3 - Count: " + i);
    }
});
t3.start();
```

**Key points:**
- Call `start()` not `run()` - start() creates new thread, run() executes in current thread
- Runnable is preferred (single inheritance limitation)
- Implement Runnable interface for better design flexibility

---

## THREAD LIFECYCLE

### Thread States

```
NEW → RUNNABLE ↔ RUNNING → TERMINATED
            ↓
        WAITING/BLOCKED
```

```java
public class ThreadLifecycleDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            System.out.println("Thread executing");
        });
        
        // State: NEW
        System.out.println("State: " + thread.getState());  // NEW
        
        thread.start();
        
        // State: RUNNABLE/RUNNING
        System.out.println("State: " + thread.getState());  // RUNNABLE
        
        thread.join();  // Wait for thread to complete
        
        // State: TERMINATED
        System.out.println("State: " + thread.getState());  // TERMINATED
    }
}
```

**State transitions:**
- **NEW**: Thread created but not started
- **RUNNABLE**: Thread started, waiting for CPU time
- **RUNNING**: Thread executing
- **WAITING**: Thread waiting for notification (wait(), join())
- **BLOCKED**: Thread blocked on monitor lock
- **TERMINATED**: Thread execution complete

---

## THREAD CONTROL METHODS

### sleep(), wait(), join()

```java
// sleep() - pause thread for specified time
Thread.sleep(2000);  // Sleep for 2 seconds
// Note: Doesn't release lock, can throw InterruptedException

// join() - wait for thread to complete
Thread t = new Thread(() -> {
    System.out.println("Working...");
    try {
        Thread.sleep(3000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
    System.out.println("Done");
});

t.start();
t.join();  // Main thread waits here for t to finish
System.out.println("Main continues after thread finishes");

// yield() - hint scheduler that current thread can yield to others
Thread.yield();  // Not guaranteed, just a hint
```

**Difference between methods:**

| Method | Releases Lock | Wakes Up When | Use Case |
|--------|---------------|---------------|----------|
| sleep() | No | Time expires | Delay execution |
| wait() | Yes | notify()/notifyAll() | Synchronization |
| join() | N/A | Thread completes | Wait for completion |
| yield() | No | Immediately | Cooperative scheduling |

---

## SYNCHRONIZATION

### Synchronized Methods & Blocks

**Problems without synchronization:**

```java
class Counter {
    private int count = 0;
    
    public void increment() {
        count++;  // Race condition: read-modify-write
    }
    
    public int getCount() {
        return count;
    }
}

// Without synchronization
Counter counter = new Counter();
Thread t1 = new Thread(() -> {
    for (int i = 0; i < 100000; i++) {
        counter.increment();
    }
});
Thread t2 = new Thread(() -> {
    for (int i = 0; i < 100000; i++) {
        counter.increment();
    }
});

t1.start();
t2.start();
t1.join();
t2.join();

System.out.println("Count: " + counter.getCount());
// Expected: 200000, Actual: ~165000 (unpredictable)
```

**Solution 1: Synchronized methods**

```java
class SynchronizedCounter {
    private int count = 0;
    
    public synchronized void increment() {
        count++;  // Only one thread can execute at a time
    }
    
    public synchronized int getCount() {
        return count;
    }
}

// Usage
SynchronizedCounter counter = new SynchronizedCounter();
Thread t1 = new Thread(() -> {
    for (int i = 0; i < 100000; i++) {
        counter.increment();
    }
});
Thread t2 = new Thread(() -> {
    for (int i = 0; i < 100000; i++) {
        counter.increment();
    }
});

t1.start();
t2.start();
t1.join();
t2.join();

System.out.println("Count: " + counter.getCount());  // Always 200000
```

**Solution 2: Synchronized blocks (fine-grained control)**

```java
class OptimizedCounter {
    private int count = 0;
    private final Object lock = new Object();
    
    public void increment() {
        synchronized (lock) {
            count++;  // Only lock this critical section
        }
    }
    
    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
    
    public void nonCriticalWork() {
        System.out.println("Non-critical work");  // No synchronization needed
        synchronized (lock) {
            count++;  // Critical section
        }
        System.out.println("More non-critical work");
    }
}

// Usage - same as before
OptimizedCounter counter = new OptimizedCounter();
```

**Key synchronization points:**
- Only one thread can execute synchronized method/block at a time
- Other threads wait for lock to be released
- Synchronization on method locks entire object
- Synchronization on block locks specific object

---

## WAIT & NOTIFY

### Producer-Consumer Pattern

**Without proper synchronization:**

```java
class BoundedBuffer {
    private int[] buffer = new int[5];
    private int count = 0;
    
    public synchronized void put(int value) {
        if (count == buffer.length) {
            System.out.println("Buffer full");
            return;
        }
        buffer[count++] = value;
    }
    
    public synchronized int get() {
        if (count == 0) {
            System.out.println("Buffer empty");
            return -1;
        }
        return buffer[--count];
    }
}
```

**With wait() & notify():**

```java
class ProducerConsumerBuffer {
    private int[] buffer = new int[5];
    private int count = 0;
    
    public synchronized void put(int value) throws InterruptedException {
        while (count == buffer.length) {
            System.out.println("Buffer full, producer waiting");
            wait();  // Release lock and wait
        }
        buffer[count++] = value;
        System.out.println("Produced: " + value);
        notifyAll();  // Wake up waiting threads
    }
    
    public synchronized int get() throws InterruptedException {
        while (count == 0) {
            System.out.println("Buffer empty, consumer waiting");
            wait();  // Release lock and wait
        }
        int value = buffer[--count];
        System.out.println("Consumed: " + value);
        notifyAll();  // Wake up waiting threads
        return value;
    }
}

// Usage
ProducerConsumerBuffer buffer = new ProducerConsumerBuffer();

// Producer thread
Thread producer = new Thread(() -> {
    try {
        for (int i = 0; i < 10; i++) {
            buffer.put(i);
            Thread.sleep(500);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

// Consumer thread
Thread consumer = new Thread(() -> {
    try {
        for (int i = 0; i < 10; i++) {
            buffer.get();
            Thread.sleep(800);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

producer.start();
consumer.start();
producer.join();
consumer.join();
```

**Key points:**
- `wait()`: Release lock and wait for notification
- `notify()`: Wake one waiting thread (unpredictable which)
- `notifyAll()`: Wake all waiting threads (safer, one proceeds)
- Always use `while` loops with wait (spurious wakeups)

---

## VOLATILE KEYWORD

**For simple visibility without full synchronization overhead:**

```java
class VolatileExample {
    private volatile boolean flag = false;
    
    public void setFlag() {
        flag = true;  // Visible to all threads immediately
    }
    
    public void printFlag() {
        if (flag) {  // Sees the latest value
            System.out.println("Flag is true");
        }
    }
}

// Usage
VolatileExample example = new VolatileExample();

Thread t1 = new Thread(() -> {
    try {
        Thread.sleep(1000);
        example.setFlag();  // Set flag after 1 second
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

Thread t2 = new Thread(() -> {
    while (true) {
        example.printFlag();  // Check flag continuously
    }
});

t1.start();
t2.start();
```

**volatile vs synchronized:**
- **volatile**: Visibility only, no atomicity (faster)
- **synchronized**: Visibility + mutual exclusion (slower but safer)
- Use volatile for flag variables, synchronized for complex operations

---

## ATOMIC CLASSES

**For atomic operations without synchronization overhead:**

```java
import java.util.concurrent.atomic.*;

// Atomic variables
AtomicInteger counter = new AtomicInteger(0);

Thread t1 = new Thread(() -> {
    for (int i = 0; i < 100000; i++) {
        counter.incrementAndGet();  // Atomic operation
    }
});

Thread t2 = new Thread(() -> {
    for (int i = 0; i < 100000; i++) {
        counter.incrementAndGet();  // Atomic operation
    }
});

t1.start();
t2.start();
t1.join();
t2.join();

System.out.println("Final count: " + counter.get());  // Always 200000

// Other atomic operations
AtomicInteger atom = new AtomicInteger(10);
atom.getAndIncrement();  // Get old value then increment
atom.incrementAndGet();  // Increment then get new value
atom.compareAndSet(11, 20);  // If value is 11, set to 20
atom.getAndAdd(5);  // Add 5 and return old value
```

**Common atomic classes:**
- `AtomicInteger`, `AtomicLong`, `AtomicBoolean`
- `AtomicReference` for object references
- Non-blocking and faster than synchronized

---

## THREAD POOLS & EXECUTORS

**Managing multiple threads efficiently:**

```java
import java.util.concurrent.*;

// ExecutorService - manages thread pool
ExecutorService executor = Executors.newFixedThreadPool(3);  // 3 threads

// Submit tasks
for (int i = 0; i < 10; i++) {
    executor.submit(() -> {
        System.out.println("Task executed by " + Thread.currentThread().getName());
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    });
}

executor.shutdown();  // No new tasks accepted
executor.awaitTermination(5, TimeUnit.SECONDS);  // Wait for all tasks to complete

// Common ExecutorService types
ExecutorService fixedPool = Executors.newFixedThreadPool(5);  // 5 threads
ExecutorService cachedPool = Executors.newCachedThreadPool();  // Dynamic pool
ExecutorService singlePool = Executors.newSingleThreadExecutor();  // 1 thread
ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(2);  // Scheduled tasks

// ScheduledExecutorService - execute tasks after delay or periodically
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

// Execute once after delay
scheduler.schedule(() -> {
    System.out.println("Executed after 2 seconds");
}, 2, TimeUnit.SECONDS);

// Execute periodically
scheduler.scheduleAtFixedRate(() -> {
    System.out.println("Executed every 1 second");
}, 0, 1, TimeUnit.SECONDS);

// Execute with fixed delay between completions
scheduler.scheduleWithFixedDelay(() -> {
    System.out.println("Executed with 1 second delay");
}, 1, 1, TimeUnit.SECONDS);
```

**Best practices:**
- Use thread pools instead of creating threads manually
- Properly shut down executors with `shutdown()` and `awaitTermination()`
- Choose appropriate pool size (CPU cores for compute, larger for I/O)

---

## THREAD SAFETY FOR COLLECTIONS

**Collections are not thread-safe by default:**

```java
import java.util.*;
import java.util.concurrent.*;

// Unsafe - ConcurrentModificationException likely
List<Integer> unsafeList = new ArrayList<>();
Thread t1 = new Thread(() -> {
    for (int i = 0; i < 100; i++) {
        unsafeList.add(i);
    }
});
Thread t2 = new Thread(() -> {
    for (Integer i : unsafeList) {  // May throw exception
        System.out.println(i);
    }
});

// Solution 1: Synchronized collections
List<Integer> syncList = Collections.synchronizedList(new ArrayList<>());
Set<Integer> syncSet = Collections.synchronizedSet(new HashSet<>());
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// Solution 2: Concurrent collections (preferred)
List<Integer> concurrentList = new CopyOnWriteArrayList<>();
Set<Integer> concurrentSet = Collections.newSetFromMap(new ConcurrentHashMap<>());
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
Queue<Integer> concurrentQueue = new ConcurrentLinkedQueue<>();

// Usage
concurrentList.add(10);
concurrentList.add(20);
for (Integer i : concurrentList) {  // Safe to iterate
    System.out.println(i);
}
```

**Thread-safe collection comparison:**

| Collection | Thread-Safe | Performance | Best For |
|-----------|-----------|-----------|----------|
| ArrayList | No | Fast | Single-threaded |
| Collections.syncList | Yes | Slow | Legacy code |
| CopyOnWriteArrayList | Yes | Fast reads | Read-heavy |
| ConcurrentHashMap | Yes | Fast | High concurrency |
| SynchronizedMap | Yes | Slow | Legacy code |

---

## LOCKS & REENTRANT LOCKS

**Fine-grained locking control:**

```java
import java.util.concurrent.locks.*;

// ReentrantLock - explicit lock management
ReentrantLock lock = new ReentrantLock();

class BankAccount {
    private double balance = 1000;
    private ReentrantLock lock = new ReentrantLock();
    
    public void withdraw(double amount) {
        lock.lock();
        try {
            if (balance >= amount) {
                balance -= amount;
                System.out.println("Withdrawn: " + amount);
            } else {
                System.out.println("Insufficient funds");
            }
        } finally {
            lock.unlock();  // Always unlock in finally
        }
    }
    
    public double getBalance() {
        lock.lock();
        try {
            return balance;
        } finally {
            lock.unlock();
        }
    }
}

// Usage
BankAccount account = new BankAccount();
Thread t1 = new Thread(() -> {
    for (int i = 0; i < 100; i++) {
        account.withdraw(1);
    }
});
Thread t2 = new Thread(() -> {
    for (int i = 0; i < 100; i++) {
        account.withdraw(1);
    }
});

t1.start();
t2.start();
t1.join();
t2.join();

System.out.println("Final balance: " + account.getBalance());
```

**ReadWriteLock - separate locks for read and write:**

```java
import java.util.concurrent.locks.*;

class CacheWithReadWriteLock {
    private Map<String, String> cache = new HashMap<>();
    private ReadWriteLock lock = new ReentrantReadWriteLock();
    
    public String get(String key) {
        lock.readLock().lock();  // Multiple readers allowed
        try {
            return cache.get(key);
        } finally {
            lock.readLock().unlock();
        }
    }
    
    public void put(String key, String value) {
        lock.writeLock().lock();  // Exclusive writer access
        try {
            cache.put(key, value);
        } finally {
            lock.writeLock().unlock();
        }
    }
}
```

**Lock vs Synchronized:**
- Lock provides explicit control
- ReadWriteLock for read-heavy scenarios
- Synchronized is simpler for most cases

---

## CALLABLE & FUTURE

**Getting return values from threads:**

```java
import java.util.concurrent.*;

// Callable - returns a value
class FactorialCallable implements Callable<Integer> {
    private int number;
    
    public FactorialCallable(int number) {
        this.number = number;
    }
    
    @Override
    public Integer call() throws Exception {
        int result = 1;
        for (int i = 2; i <= number; i++) {
            result *= i;
        }
        return result;
    }
}

// Usage
ExecutorService executor = Executors.newSingleThreadExecutor();

Future<Integer> future = executor.submit(new FactorialCallable(5));

try {
    Integer result = future.get();  // Wait for result
    System.out.println("Factorial of 5: " + result);
    
    // Check if done
    if (future.isDone()) {
        System.out.println("Task completed");
    }
    
    // Get result with timeout
    Integer resultWithTimeout = future.get(5, TimeUnit.SECONDS);
    
} catch (InterruptedException | ExecutionException | TimeoutException e) {
    e.printStackTrace();
}

executor.shutdown();

// Lambda expression with Callable
Future<String> future2 = executor.submit(() -> {
    Thread.sleep(2000);
    return "Result after 2 seconds";
});
```

**Runnable vs Callable:**
- **Runnable**: No return value, no checked exceptions
- **Callable**: Returns value, throws checked exceptions

---

## COMMON CONCURRENCY ISSUES

### Deadlock

```java
class DeadlockExample {
    private static Object lock1 = new Object();
    private static Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("T1: Acquired lock1");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                synchronized (lock2) {
                    System.out.println("T1: Acquired lock2");
                }
            }
        });
        
        Thread t2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("T2: Acquired lock2");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                synchronized (lock1) {
                    System.out.println("T2: Acquired lock1");
                }
            }
        });
        
        t1.start();
        t2.start();
        // DEADLOCK: T1 waits for lock2, T2 waits for lock1
    }
}

// Solution: Acquire locks in same order
class DeadlockSolution {
    private static Object lock1 = new Object();
    private static Object lock2 = new Object();
    
    public static void acquireBothLocks() {
        // Same order everywhere
        synchronized (lock1) {
            synchronized (lock2) {
                // Safe code here
            }
        }
    }
}
```

### Race Condition

```java
class RaceCondition {
    private int count = 0;
    
    // Unsafe - race condition
    public void unsafeIncrement() {
        count++;  // Not atomic: read, add, write
    }
    
    // Safe - synchronized
    public synchronized void safeIncrement() {
        count++;
    }
}
```

### Starvation

```java
class StarvationExample {
    private final Object lock = new Object();
    
    public int readCounter() throws InterruptedException {
        synchronized (lock) {
            Thread.sleep(1);  // Simulate work
            return 1;
        }
    }
    
    public void incrementCounter() {
        synchronized (lock) {
            // Many writers can cause readers to starve
        }
    }
    
    // Solution: Use ReadWriteLock
}
```

---

## THREAD INTERRUPTION

**Graceful thread termination:**

```java
class InterruptibleTask implements Runnable {
    @Override
    public void run() {
        try {
            while (!Thread.currentThread().isInterrupted()) {
                System.out.println("Task running");
                Thread.sleep(1000);  // Can throw InterruptedException
            }
        } catch (InterruptedException e) {
            System.out.println("Task interrupted");
            Thread.currentThread().interrupt();  // Restore flag
        }
    }
}

// Usage
Thread t = new Thread(new InterruptibleTask());
t.start();

Thread.sleep(3500);
t.interrupt();  // Request interruption
t.join();  // Wait for thread to finish
```

**Interruption points:**
- `Thread.sleep()`
- `Object.wait()`
- `Thread.join()`
- `BlockingQueue` operations

---

## BEST PRACTICES

✓ **Prefer immutable objects** - No synchronization needed
✓ **Use thread pools** - Don't create threads manually
✓ **Keep synchronized blocks small** - Minimize lock contention
✓ **Avoid nested locks** - Prevents deadlock
✓ **Use concurrent collections** - Better than synchronized collections
✓ **Handle InterruptedException properly** - Respect thread interruption
✓ **Test with tools** - ThreadSanitizer, JCStress
✓ **Use higher-level abstractions** - ExecutorService, BlockingQueue

---

## THREAD SAFETY SUMMARY TABLE

| Technique | Thread-Safe | Performance | Use Case |
|-----------|-----------|-----------|----------|
| Synchronized | Yes | Slow | Simple shared state |
| Volatile | Visibility | Fast | Flags, visibility |
| Atomic | Yes | Fast | Single variable |
| Lock | Yes | Medium | Complex locking |
| ReadWriteLock | Yes | Fast reads | Read-heavy |
| ConcurrentHashMap | Yes | Fast | Shared map |
| CopyOnWriteArrayList | Yes | Fast reads | Read-heavy lists |
| BlockingQueue | Yes | Medium | Producer-consumer |

---

## KEY TAKEAWAYS

✓ **Thread creation**: Implement Runnable, call start()
✓ **Synchronization**: Use smallest synchronized blocks
✓ **wait/notify**: Use while loops, notifyAll() preferred
✓ **volatile**: Simpler alternative for visibility
✓ **Atomic classes**: Use for simple operations
✓ **Thread pools**: Manage thread creation efficiently
✓ **Collections**: Use concurrent variants for shared access
✓ **Interruption**: Check and respect interruption flag

---

**Previous:** [24. Advanced Topics](24-Advanced-Topics.md) | **Next:** [26. Regular-Expressions](26-Regular-Expressions.md)
