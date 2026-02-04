# JVM, JRE, JDK Architecture

## The Three Pillars

**JDK = JRE + Development Tools (javac, debugger, javadoc)**  
**JRE = JVM + Runtime Libraries**  
**JVM = Execution Engine + Memory Manager**

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

```
Source (.java) → [javac] → Bytecode (.class) → [ClassLoader] → 
[JVM Memory] → [Execution Engine] → Native Code → Output
```

```bash
javac HelloWorld.java  # Compile
java HelloWorld        # Execute
```

---

## ClassLoader Hierarchy

1. **Bootstrap**: Core libraries (`java.lang.*`)
2. **Extension**: Extension libraries (`javax.*`)
3. **Application**: Your classes

**Phases**: Loading → Linking → Initialization

---

## JVM Memory

| Area | Purpose | Shared? | GC? |
|------|---------|---------|-----|
| **Heap** | Objects, arrays | Yes | Yes |
| **Stack** | Method calls, local vars | No (per-thread) | No |
| **Metaspace** | Class metadata | Yes | No |
| **PC Register** | Current instruction | No (per-thread) | No |

---

## Execution Engine

- **Interpreter**: Fast startup, line-by-line execution
- **JIT Compiler**: Compiles hot code to native, caches it
- **Hybrid**: Start with interpreter, JIT optimizes frequently used code

---

## Platform Independence

**Write Once, Run Anywhere (WORA)**

Bytecode (.class) is platform-independent → JVM is platform-specific

---

## GC Algorithms

- **Serial**: Single thread (small apps)
- **Parallel**: Multiple threads (throughput)
- **G1**: Predictable pauses (default Java 9+)
- **ZGC**: Ultra-low latency (Java 11+)

---

## Next: [Memory Management](03-Memory-Management.md)
