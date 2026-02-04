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

**Young Generation** (Minor GC):
- **Eden**: New objects created here
- **Survivor 0 & 1**: Objects surviving GC move here

**Old Generation** (Major GC):
- Long-lived objects after multiple survivals

```java
String str = new String("Hello");  // In heap
int[] arr = {1, 2, 3};             // In heap
```

**Configure**:
```bash
java -Xms512m -Xmx2g MyApp  # Initial/Max heap
```

---

## Stack Memory

- Per-thread, LIFO
- Stores method calls + local variables
- Auto-freed when method exits

```java
void method() {
    int x = 10;  // Stack
    String s = "Hi";  // Reference in stack, object in heap
}  // x freed when method exits
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

- Replaces PermGen
- Stores class metadata, static variables
- Dynamically grows in native memory

```bash
java -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=256m MyApp
```

---

## Garbage Collection

### When is Object Eligible for GC?
When **no references** point to it.

```java
String str = new String("Hello");
str = null;  // Object eligible for GC
System.gc(); // Request GC (not guaranteed)
```

### GC Types

**Minor GC**: Young Generation (frequent, fast)  
**Major/Full GC**: Old Generation (rare, slow, "stop-the-world")

### GC Algorithms

| Algorithm | Threads | Use Case | Flag |
|-----------|---------|----------|------|
| Serial | 1 | Small apps | `-XX:+UseSerialGC` |
| Parallel | Multiple | Throughput | `-XX:+UseParallelGC` |
| G1 | Multiple | Large heaps | `-XX:+UseG1GC` |
| ZGC | Multiple | Low latency | `-XX:+UseZGC` |

---

## Common Memory Errors

### OutOfMemoryError: Java Heap Space
Too many objects in heap.
```bash
java -Xmx4g MyApp  # Increase heap
```

### StackOverflowError
Too deep recursion.
```bash
java -Xss2m MyApp  # Increase stack size
```

### Memory Leak
Holding unnecessary references.
```java
static List<Object> list = new ArrayList<>();
list.add(obj);  // Never cleared → leak
```

---

## Memory Tuning Tips

1. **Right heap size**: 50-75% of available RAM
2. **Monitor GC**: `-Xlog:gc*`
3. **Choose GC algorithm**: G1 for most cases
4. **Avoid object creation in loops**: Use StringBuilder vs String concatenation

---

## Next: [Strings & String Pool](04-Strings-StringPool.md)
