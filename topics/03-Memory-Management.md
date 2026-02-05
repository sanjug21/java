# Memory Management in Java

## JVM Memory Structure

```
┌────────────────────────────┐
│ HEAP (Shared, GC)          │
│ ├─ Young Gen (Eden+S0+S1)  │
│ └─ Old Gen (Tenured)       │
├────────────────────────────┤
│ METASPACE (Class metadata) │
├────────────────────────────┤
│ STACK (Per-thread, LIFO)   │
├────────────────────────────┤
│ PC REGISTER (Per-thread)   │
└────────────────────────────┘
```

---

## Heap Memory

### Young Generation (Minor GC - Frequent)
- **Eden**: New objects created here (large area)
- **Survivor 0 & 1 (S0, S1)**: Objects that survived GC move here (smaller areas)
- **Process**: New objects → Eden → S0 or S1 → Old Gen (if still alive)

### Old Generation (Major/Full GC - Rare, Slow)
- Long-lived objects that survived multiple Young Gen GCs
- Takes much longer to collect → causes "stop-the-world" pause
- If full → OutOfMemoryError

```java
String str = new String("Hello");  // In heap (Eden initially)
int[] arr = {1, 2, 3};             // In heap (Eden initially)
```

**Configure Heap Size**:
```bash
java -Xms512m -Xmx2g MyApp
# -Xms: Initial heap = 512 MB
# -Xmx: Maximum heap = 2 GB (max recommended: 75% of available RAM)
```

---

## Stack Memory

- **Per-thread**: Each thread gets its own separate stack
- **LIFO (Last In, First Out)**: Last method called is first to exit
- **Stores**: Method calls (call stack frames) + local variables
- **Auto-freed**: When method exits, its stack frame is removed
- **Fixed Size**: ~1MB per thread (can cause StackOverflowError if exceeded)

```java
void method() {
    int x = 10;              // Stack (primitive)
    String s = "Hi";         // Stack (reference) → points to heap (object)
    ArrayList<String> list = new ArrayList<>();  // Stack (reference) → heap (object)
}  // All stack memory freed when method exits

void recursion(int n) {
    recursion(n+1);  // Each call adds frame to stack
}  // Too deep → StackOverflowError
```

---

## Stack vs Heap

| Feature | Stack | Heap |
|---------|-------|------|
| Stores | Method calls, local vars | Objects, arrays |
| Scope | Per-thread | Shared |
| Lifecycle | Method exit | GC |
| Speed | Faster | Slower |
| Size | Smaller (~1MB/thread) | Larger (GBs) |
| Error | StackOverflowError | OutOfMemoryError |

---

## Metaspace (Java 8+)

- **Replaces PermGen** (Java 7 and earlier)
- **Stores**: Class definitions, method data, constant pools, static variables
- **Location**: Native memory (outside JVM heap), shared by all threads
- **Growth**: Dynamically expands as needed (limited by OS, not JVM)
- **GC**: Automatically collects unused class metadata
- **When It Fills**: Causes `OutOfMemoryError: Metaspace` (rare, usually with classloaders)

```bash
java -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=256m MyApp
# MetaspaceSize: Initial size
# MaxMetaspaceSize: Maximum allowed (defaults to unlimited)

# Example problem: Loading many classes dynamically → Metaspace grows
for (int i = 0; i < 100000; i++) {
    ClassLoader.defineClass(...);  // Creates new class → Metaspace grows
}
```

---

## Garbage Collection

### When is Object Eligible for GC?
When **no references** point to it (unreachable from root objects).

```java
String str = new String("Hello");  // Object created
str = null;                         // Lost reference → eligible for GC
System.gc();                        // Suggests GC (not guaranteed)
// Next GC will free the String object
```

### GC Types

**Minor GC**: Cleans Young Generation
  - Frequent (every few seconds)
  - Fast (usually < 50ms)
  - Moves surviving objects to Old Gen
  
**Major/Full GC**: Cleans Old Generation + Metaspace
  - Rare but critical
  - Slow (can take seconds)
  - "Stop-the-world" pause: entire application pauses

### GC Algorithms Comparison

| Algorithm | Threads | Pause Time | Best For | Flag |
|-----------|---------|-----------|----------|------|
| **Serial** | 1 | Variable | Small apps (<1GB) | `-XX:+UseSerialGC` |
| **Parallel** | Multiple | Moderate | High throughput (batch jobs) | `-XX:+UseParallelGC` |
| **G1** | Multiple | Predictable | Large heaps (>4GB), default Java 9+ | `-XX:+UseG1GC` |
| **ZGC** | Multiple | Ultra-low (<10ms) | Real-time (trading, responsive apps) | `-XX:+UseZGC` |

---

## Common Memory Errors

### OutOfMemoryError: Java Heap Space
**Cause**: Application creates more objects than heap can hold

```bash
java -Xmx4g MyApp  # Increase heap to 4GB
```

**Common Sources**:
- Reading huge file into memory at once
- Collections growing without limit
- Memory leaks (see below)

### StackOverflowError
**Cause**: Stack exhausted by too many nested method calls (usually infinite recursion)

```java
void infinite() {
    infinite();  // Calls itself → stack grows → StackOverflowError
}
```

**Solution**:
```bash
java -Xss2m MyApp  # Increase stack to 2MB (but doesn't fix bad code!)
```

### Memory Leak
**Cause**: Holding references to objects that are no longer needed

```java
static List<Object> cache = new ArrayList<>();  // Static = lives forever
public void processData(Object obj) {
    cache.add(obj);  // Objects accumulate, never removed
}  // Eventually heap fills up → OutOfMemoryError

// Fix: Remove old entries or use weak references
```

---

## Memory Tuning Best Practices

1. **Right Heap Size**
   - Start: 50-75% of available RAM
   - Monitor actual usage → adjust
   - Too small → frequent full GC pauses
   - Too large → long GC pause times

2. **Monitor GC Behavior**
   ```bash
   java -Xlog:gc* -Xlog:gc*:file=gc.log MyApp
   # Shows which GCs run, pause time, objects collected
   ```

3. **Choose Right GC Algorithm**
   - **Small app** (<2GB): Parallel GC (default)
   - **Large app** (>4GB): G1 GC (default Java 9+)
   - **Low latency needed**: ZGC (Java 11+)

4. **Avoid Excessive Object Creation**
   ```java
   // Bad: Creates many String objects
   for (int i = 0; i < 1000000; i++) {
       result += data[i];  // Creates new String each time
   }
   
   // Good: Reuse StringBuilder
   StringBuilder sb = new StringBuilder();
   for (int i = 0; i < 1000000; i++) {
       sb.append(data[i]);  // Reuses same buffer
   }
   ```

5. **Profile Before Optimizing**
   - Use tools like JProfiler, YourKit
   - Identify actual bottlenecks
   - Don't guess!

---

## Next: [Strings & String Pool](04-Strings-StringPool.md)
