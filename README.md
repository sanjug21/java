# Java Topics - Complete Guide

This folder contains **concise, comprehensive** explanations of all **25 Java topics**. Each file covers essential concepts with detailed explanations, 50+ code examples, comparison tables, and best practices. All files follow the "concise-detailed" pattern.

---

## 📚 Table of Contents (25 Topics)

### Core Fundamentals (01-05)

1. **[Java Basics & Fundamentals](topics/01-Java-Basics-Fundamentals.md)**
   - Variables | Data types | Operators | Control flow | Loops | Methods | Arrays

2. **[JVM, JRE, JDK Architecture](topics/02-JVM-JRE-JDK-Architecture.md)**
   - JDK/JRE/JVM | ClassLoader | Execution Engine | Memory areas | Compilation

3. **[Memory Management](topics/03-Memory-Management.md)**
   - Heap vs Stack | GC algorithms | Memory errors | Optimization

4. **[Strings & String Pool](topics/04-Strings-StringPool.md)**
   - String immutability | Pool internals | StringBuilder | Methods

5. **[Static Keyword & Access Modifiers](topics/05-Static-Access-Modifiers.md)**
   - Static members | Access modifiers | Visibility table | Encapsulation

---

### Object-Oriented Programming (06-11)

6. **[Class Hierarchies & Object Class](topics/06-Class-Hierarchies.md)**
   - Object class methods | equals()/hashCode() contract | instanceof | Copy types

7. **[Autoboxing & Wrapper Classes](topics/07-Autoboxing-Wrapper-Classes.md)**
   - Wrapper classes | Autoboxing/unboxing | Integer caching | Comparisons

8. **[OOP Principles](topics/08-OOP-Principles.md)**
   - Encapsulation | Inheritance | Polymorphism | Abstraction

9. **[Inheritance & Polymorphism](topics/09-Inheritance-Polymorphism.md)**
   - Constructor chaining | Method overriding | Dynamic dispatch | Casting

10. **[Inner Classes](topics/10-Inner-Classes.md)**
    - Member inner | Static nested | Local inner | Anonymous inner

11. **[Dynamic Binding](topics/11-Dynamic-Binding.md)**
    - Static vs dynamic binding | Method resolution | Virtual methods

---

### Design & Architecture (12-13)

12. **[Design Patterns](topics/12-Design-Patterns.md)**
    - 23 GoF patterns | Creational | Structural | Behavioral | Architectural patterns

13. **[SOLID Principles](topics/13-SOLID-Principles.md)**
    - SRP | OCP | LSP | ISP | DIP with examples

---

### Collections & Algorithms (14-15)

14. **[Collections Framework](topics/14-Collections-Framework.md)**
    - ArrayList vs LinkedList | HashSet vs TreeSet | HashMap | Queue types | Comparator

15. **[Sorting Algorithms](topics/15-Sorting-Algorithms.md)**
    - Implementation-first + visualizations | All 7 algorithms | Complexity comparison

---

### Generics & Functional (16-19)

16. **[Generics](topics/16-Generics.md)**
    - Generic classes/methods | Bounded types | Wildcards | PECS | Type erasure

17. **[Lambda Expressions](topics/17-Lambda-Expressions.md)**
    - Syntax | Functional interfaces | Method references (4 types) | Closures

18. **[Stream API](topics/18-Stream-API.md)**
    - Stream creation | Intermediate ops | Terminal ops | Parallel streams

19. **[Collectors](topics/19-Collectors.md)**
    - Basic | Numerical | Grouping | Partitioning | Advanced | Custom

---

### Modern & Advanced (20-25)

20. **[Annotations & Reflection](topics/20-Annotations-Reflection.md)**
    - Built-in annotations | Custom annotations | Reflection API | Framework patterns

21. **[Exception Handling](topics/21-Exception-Handling.md)**
    - Exception hierarchy | try-catch-finally | Checked vs unchecked | Custom exceptions

22. **[I/O Programming](topics/22-IO-Programming.md)**
    - Byte/Character streams | Buffering (100x faster) | Serialization | NIO

23. **[Java 8 Features](topics/23-Java-8-Features.md)**
    - Lambda | Stream | Optional | Date/Time API | CompletableFuture | 13 features total

24. **[Advanced Topics](topics/24-Advanced-Topics.md)**
    - Reflection | Enums | Networking | Records | Pattern matching

25. **[Multithreading](topics/25-Multithreading.md)**
    - Thread creation | Synchronization | wait/notify | Executors | Atomic operations | Concurrent collections

---

## 🎯 Quick Navigation
   - Wrapper classes (Integer, Double, etc.)
   - Autoboxing/unboxing
   - Integer caching (-128 to 127)
   - Primitive vs wrapper comparison

8. **[OOP Principles](08-OOP-Principles.md)**
   - Encapsulation
   - Inheritance
   - Polymorphism
   - Abstraction

9. **[Inheritance & Polymorphism](09-Inheritance-Polymorphism.md)**
   - Constructor chaining
   - Method overriding rules
   - Dynamic method dispatch
   - Upcasting & downcasting

10. **[Inner Classes](10-Inner-Classes.md)**
    - Member inner class
    - Static nested class
    - Local inner class
    - Anonymous inner class

11. **[Dynamic Binding](11-Dynamic-Binding.md)**
    - Static vs dynamic binding
    - Method resolution order
    - Variable hiding vs method overriding
    - Virtual method invocation

---

### Design & Architecture

12. **[Design Patterns](12-Design-Patterns.md)**
    - Creational: Singleton, Factory, Builder
    - Structural: Adapter, Decorator
    - Behavioral: Observer, Strategy

13. **[SOLID Principles](13-SOLID-Principles.md)**
    - Single Responsibility Principle
    - Open/Closed Principle
    - Liskov Substitution Principle
    - Interface Segregation Principle
    - Dependency Inversion Principle

---

### Collections & Algorithms

14. **[Collections Framework](14-Collections-Framework.md)**
    - List (ArrayList, LinkedList)
    - Set (HashSet, TreeSet)
    - Map (HashMap, TreeMap)
    - Queue (PriorityQueue, ArrayDeque)
    - Comparable vs Comparator

15. **[Sorting Algorithms](15-Sorting-Algorithms.md)**
    - Bubble Sort, Merge Sort
    - Quick Sort, Heap Sort
    - Counting Sort
    - Complexity comparison

---

### Generics & Functional Programming

16. **[Generics](16-Generics.md)**
    - Generic classes and methods
    - Bounded type parameters
    - Wildcards (? extends, ? super)
    - PECS principle (Producer Extends, Consumer Super)

17. **[Lambda Expressions](17-Lambda-Expressions.md)**
    - Lambda syntax
    - Functional interfaces
    - Method references
    - Built-in interfaces (Predicate, Function, Consumer, Supplier)

18. **[Stream API](18-Stream-API.md)**
    - Stream creation
    - Intermediate operations (filter, map, flatMap, sorted)
    - Terminal operations (collect, reduce, forEach)
    - Parallel streams

19. **[Collectors](19-Collectors.md)**
    - Basic collectors (toList, toSet, toMap)
    - groupingBy, partitioningBy
    - joining, mapping, filtering
    - Custom collectors

---

### Error Handling & I/O

20. **[Exception Handling](20-Exception-Handling.md)**
    - try-catch-finally
    - Checked vs unchecked exceptions
    - Custom exceptions
    - try-with-resources
    - Exception chaining

21. **[I/O Programming](21-IO-Programming.md)**
    - Byte vs character streams
    - Buffered streams
    - Object serialization
    - NIO (Path, Files, FileChannel)

---

### Modern Java

22. **[Java 8 Features](22-Java-8-Features.md)**
    - Lambda expressions
    - Stream API
    - Default & static methods in interfaces
    - Optional class
    - Date/Time API (java.time)
    - CompletableFuture
    - Method references

23. **[Advanced Topics](23-Advanced-Topics.md)**
    - Multithreading & concurrency
    - ExecutorService & thread pools
    - Reflection
    - Annotations
    - Enums
    - Security & encryption
    - Networking (Sockets)
    - Records (Java 14+)
    - Pattern matching (Java 16+)
    - Sealed classes (Java 17+)

---

## 🎯 How to Use This Guide

### For Beginners
Start with **Core Fundamentals** (1-5) → **OOP** (6-11)

### For Intermediate
Focus on **Design** (12-13) → **Collections** (14-15) → **Functional Programming** (16-19)

### For Advanced
Master **I/O** (20-21) → **Modern Java** (22-23) → **Advanced Topics**

### For Interview Prep
Cover all topics with emphasis on:
- Design Patterns (12)
- SOLID Principles (13)
- Collections (14)
- Stream API (18)
- Exception Handling (20)

---

## ✨ Features

✅ **Concise:** No unnecessary verbosity  
✅ **Comprehensive:** All concepts covered  
✅ **Code Examples:** Practical, runnable code  
✅ **Tables:** Quick comparison references  
✅ **Best Practices:** Industry standards  
✅ **Navigation:** Links to next/previous topics  

---

## 📖 Related Resources

- **[Complete-Java-Guide.md](../Complete-Java-Guide.md)** - Original comprehensive guide (3580 lines)
- **[Java-Advanced-Mastery-Complete.md](../Java-Advanced-Mastery-Complete.md)** - Advanced concepts
- **[ORGANIZATION-SUMMARY.md](ORGANIZATION-SUMMARY.md)** - Project structure overview

---

**Happy Learning! 🚀**

*All 25 topics | Concise format | Complete coverage | Ready for mastery*
