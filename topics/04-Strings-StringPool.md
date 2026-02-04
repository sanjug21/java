# Strings & String Pool

## String Immutability

Strings are **immutable** - cannot be changed after creation. Any modification creates new object.

```java
String str = "Hello";
str = str + " World";  // Creates NEW object, doesn't modify original
```

**Why Immutable?**
- Security (used in network, file paths)
- Thread-safe
- Enables String Pool
- Hashcode caching

---

## String Pool

Special memory region in **Heap** for storing string literals.

```java
String s1 = "Java";  // String Pool
String s2 = "Java";  // Same pool object
System.out.println(s1 == s2);  // true (same reference)

String s3 = new String("Java");  // Heap (outside pool)
System.out.println(s1 == s3);    // false (different objects)
System.out.println(s1.equals(s3)); // true (same content)
```

**Memory**:
```
String Pool:  ["Java"] ← s1, s2 point here
Heap:         ["Java"] ← s3 points here (different object)
```

---

## String Creation: Literal vs new

| Method | Location | Reuses Pool? |
|--------|----------|--------------|
| `"Hello"` | String Pool | Yes |
| `new String("Hello")` | Heap | No (creates 2 objects!) |

```java
String s = new String("Hello");
// Creates: 1) "Hello" in pool, 2) new String in heap
```

---

## intern() Method

Moves string to pool or returns existing pool reference.

```java
String s1 = new String("Hello");
String s2 = s1.intern();  // Returns pool reference
String s3 = "Hello";      // Pool reference

System.out.println(s1 == s2); // false (s1 heap, s2 pool)
System.out.println(s2 == s3); // true (both pool)
```

**Use case**: Memory optimization with many duplicate strings.

---

## String vs StringBuilder vs StringBuffer

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| Mutable | No | Yes | Yes |
| Thread-safe | Yes | No | Yes |
| Performance | Slow (many objects) | Fast | Medium |
| Use when | Fixed strings | Single thread concat | Multi-thread concat |

```java
// Bad: Creates 1000 String objects
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;
}

// Good: Single StringBuilder object
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

---

## String Comparison

### == vs equals()

- **==**: Compares **references** (memory address)
- **equals()**: Compares **content**

```java
String s1 = "Java";
String s2 = "Java";
String s3 = new String("Java");

s1 == s2      // true (same pool object)
s1 == s3      // false (different objects)
s1.equals(s3) // true (same content)
```

**Rule**: Always use `equals()` for content comparison!

---

## Common String Methods

```java
String str = "Hello World";

// Length & Access
str.length()              // 11
str.charAt(0)             // 'H'
str.substring(0, 5)       // "Hello"

// Case
str.toUpperCase()         // "HELLO WORLD"
str.toLowerCase()         // "hello world"

// Trim & Replace
"  Java  ".trim()         // "Java"
str.replace('o', '0')     // "Hell0 W0rld"

// Split
str.split(" ")            // ["Hello", "World"]

// Search
str.contains("World")     // true
str.startsWith("Hello")   // true
str.endsWith("World")     // true
str.indexOf('o')          // 4
str.lastIndexOf('o')      // 7

// Comparison
str.equals("Hello World")           // true
str.equalsIgnoreCase("HELLO WORLD") // true
str.compareTo("Hello World")        // 0
```

---

## Best Practices

1. **Use literals**: `String s = "Hello";` not `new String("Hello")`
2. **Use StringBuilder for concatenation in loops**
3. **Use equals() not ==** for comparison
4. **Avoid NPE**: `"constant".equals(variable)` not `variable.equals("constant")`
5. **Use intern() wisely**: For many duplicate strings only

---

## Key Concepts

- **Immutability**: Strings can't be modified
- **String Pool**: Memory optimization for literals
- **new vs literal**: Literal uses pool, new creates heap object
- **StringBuilder**: Mutable, use for concatenation
- **equals() vs ==**: Content vs reference comparison

---

## Next: [Static vs Non-Static](05-Static-Access-Modifiers.md)
