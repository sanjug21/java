# JVM, JRE, JDK Architecture

## The Three Pillars

### 1. **JVM (Java Virtual Machine)**
- The actual **execution engine** that runs Java bytecode
- Platform-specific (different for Windows, Mac, Linux)
- Provides a **virtual environment** that abstracts hardware differences
- Handles memory management, garbage collection, security

### 2. **JRE (Java Runtime Environment)**
- = JVM + **Standard Library Classes** (String, ArrayList, IO, etc.)
- Used for **running** Java programs
- Contains everything needed to execute bytecode
- Does NOT include compiler or development tools
- End-users install JRE to run Java applications

### 3. **JDK (Java Development Kit)**
- = JRE + **Development Tools** (javac compiler, debugger, javadoc)
- Used for **developing** Java programs
- Only developers need to install JDK
- Includes everything in JRE plus compiler and utilities

**Summary**: JDK (for development) > JRE (for running) > JVM (for execution)

```
┌─────────────────────────────────┐
│  JDK (Development Kit)          │
│  ┌───────────────────────────┐  │
│  │  JRE (Runtime)            │  │
│  │  ┌─────────────────────┐  │  │
│  │  │  JVM (Executes)     │  │  │
│  │  └─────────────────────┘  │  │
│  │  + Libraries              │  │
│  └───────────────────────────┘  │
│  + Compiler + Tools             │
└─────────────────────────────────┘
```

---

## Compilation & Execution

**Step-by-step process**:

1. **Compilation** (happens once)
   - Java compiler (javac) converts `.java` source code → `.class` bytecode
   - Bytecode is platform-independent (same .class works everywhere)
   
2. **ClassLoading** (when program starts)
   - ClassLoader reads the .class file from disk
   - Loads it into memory for execution
   
3. **Execution** (by JVM)
   - Execution Engine interprets or JIT-compiles bytecode → native machine code
   - Runs on the specific platform (Windows, Linux, Mac)

```
Source (.java) → [javac Compiler] → Bytecode (.class) → [ClassLoader] → 
[JVM Memory] → [Execution Engine] → Native Code → Output
```

**Example**:
```bash
javac HelloWorld.java  # Creates HelloWorld.class (same on all platforms)
java HelloWorld        # JVM reads .class and executes
```

---

## ClassLoader Hierarchy

ClassLoaders load classes in **3-tier hierarchy**:

1. **Bootstrap ClassLoader** (Highest Priority)
   - Loads core Java classes: `java.lang.*`, `java.util.*`, etc.
   - Part of JVM itself, written in C++
   - Example: String, Integer, Object classes
   
2. **Extension ClassLoader**
   - Loads extension libraries: `javax.*`, `org.w3c.*`
   - Found in `$JAVA_HOME/lib/ext/`
   
3. **Application ClassLoader** (Lowest Priority)
   - Loads your application classes and classes in classpath
   - This is what loads your `HelloWorld.class`

**Loading Process**:
- **Loading**: Read .class file from disk/network
- **Linking**: Verify, prepare, resolve references
- **Initialization**: Execute static blocks and initialize variables

**Delegation Model**: Application → Extension → Bootstrap (checks parent first)

---

## JVM Memory Areas

| Area | Purpose | Shared? | Garbage Collected? |
|------|---------|---------|--------|
| **Heap** | Objects, arrays | Shared by all threads | **Yes** |
| **Stack** | Method calls, local variables | Per-thread (separate for each) | **No** |
| **Metaspace** | Class definitions, method data | Shared by all threads | **No** |
| **Program Counter (PC) Register** | Address of currently executing instruction | Per-thread | **No** |

**Details**:
- **Heap**: Where `new String()`, `new ArrayList()` are created - can cause OutOfMemoryError
- **Stack**: When method is called, a frame is pushed; when it returns, frame is popped - size is limited
- **Metaspace**: Information about classes themselves, not instances - replaced PermGen in Java 8+

**Memory Issues**:
- Stack overflow: Too many nested method calls → `StackOverflowError`
- Heap overflow: Too many objects → `OutOfMemoryError: Java heap space`

---

## Execution Engine (How JVM Executes Bytecode)

### Three Execution Strategies:

1. **Interpreter** (Traditional)
   - Reads bytecode line-by-line and executes
   - **Pros**: Fast startup time, works anywhere
   - **Cons**: Slower execution (repeated code interpreted each time)
   
2. **JIT Compiler (Just-In-Time)**
   - Detects frequently-used code ("hot code")
   - Compiles entire method to native machine code
   - Caches the native code for reuse
   - **Pros**: Much faster execution after compilation
   - **Cons**: Compilation overhead initially
   
3. **Hybrid (Modern JVM Default)**
   - Starts with **Interpreter** (fast startup)
   - JIT compiler monitors which methods are called frequently
   - **Automatically compiles** hot methods to native code
   - **Best of both worlds**: Fast startup + fast execution
   
**Real Example**: First time `fibonacci(50)` runs slower (interpreted), but called 1000 times? JIT compiles it → runs 10-100x faster

---

## Platform Independence: WORA (Write Once, Run Anywhere)

**The Magic**:
- You write `.java` code once
- Compile to `.class` bytecode once (universal format)
- Run on ANY platform with a JVM installed

**Why This Works**:
```
Your Code (.java) 
    ↓
    [Compile once] → Bytecode (.class) ← Same everywhere!
    ↓
Run on Windows JVM  |  Run on Linux JVM  |  Run on Mac JVM
     ↓                      ↓                      ↓
Windows native code  Linux native code   Mac native code
```

**Example**: `HelloWorld.class` compiled on Windows works perfectly on Linux or Mac - no recompilation needed!

**Note**: JVM itself is platform-specific (different for Windows/Linux/Mac), but bytecode is universal

---

## Garbage Collection (GC) Algorithms

Garbage Collector automatically frees memory from unused objects:

| GC Type | Threads | Use Case | Pause Time |
|---------|---------|----------|------------|
| **Serial** | Single | Small applications, laptops | High but acceptable |
| **Parallel** | Multiple | High throughput (batch processing) | Moderate |
| **G1GC** | Multiple | Medium-large apps, balanced latency | Predictable |
| **ZGC** | Multiple | Ultra-low latency needs (trading, real-time) | Ultra-low (<10ms) |

**Simple Explanation**:
- As program runs, unused objects accumulate
- GC identifies "dead" objects (no references pointing to them)
- Automatically frees that memory for reuse
- Prevents memory leaks (unlike C/C++)

**Trade-off**: GC runs = program pauses briefly ("pause time") - faster GC = more predictable, but uses more CPU

---

## Next: [Memory Management](03-Memory-Management.md)
