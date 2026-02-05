# Strings & String Pool

## String Immutability

Strings are **immutable** - cannot be changed after creation. Any modification creates new object.

```java
String str = "Hello";
str = str + " World";  // Creates NEW object, doesn't modify original
// str now references a different String object in memory
```

**Why Immutable?**
- **Security**: Used in passwords, file paths - can't be altered by accident
- **Thread-safe**: Multiple threads can safely access the same String
- **Enables String Pool**: Same string literal can be reused efficiently
- **Hashcode caching**: hashCode() computed once and never changes
- **HashMap Keys**: Reliably use Strings as HashMap keys (hash doesn't change)

**Performance Implication**:
```java
// Each operation creates new objects
String s = "A";
s = s + "B";  // s now → "AB" (creates new object, old "A" → eligible for GC)
s = s + "C";  // s now → "ABC" (creates new object, old "AB" → eligible for GC)
// For 1000 operations: 1000 objects created → GC overhead
```

---

## String Pool

Special memory region in **Heap** for storing string literals (not objects created with `new`).

**How it Works**:
- When you write `"Java"`, JVM checks if it already exists in pool
- If yes: returns reference to existing string (no new object created)
- If no: creates new string in pool and stores reference

```java
String s1 = "Java";  // Creates "Java" in String Pool
String s2 = "Java";  // Checks pool → finds existing → reuses it
System.out.println(s1 == s2);  // true (same object reference)

String s3 = new String("Java");  // BYPASSES pool → creates in Heap
System.out.println(s1 == s3);    // false (different objects)
System.out.println(s1.equals(s3)); // true (same content, but different objects)
```

**Memory Visualization**:
```
String Pool (in Heap):  ["Java"] ← s1 and s2 both point here
Regular Heap:           ["Java"] ← s3 points here (wasted copy!)
```

**Benefits**:
- Memory savings: Duplicate string literals share single object
- Faster creation: No allocation if string already exists
- Default behavior for string literals

---

## String Creation: Literal vs new

| Method | Location | Reuses Pool? | Objects Created |
|--------|----------|--------------|-----------------|
| `"Hello"` | String Pool | **Yes** | 1 |
| `new String("Hello")` | Heap | **No** | 2 (pool + heap) |

```java
// Using literal: Efficient
String s1 = "Hello";  // Creates in pool (if not exists)
String s2 = "Hello";  // Reuses from pool

// Using new: Wasteful
String s3 = new String("Hello");  // Creates BOTH: 1) "Hello" in pool, 2) new String in heap
// Now we have 2 "Hello" objects in memory!

System.out.println(s1 == s2);  // true (same pool object)
System.out.println(s1 == s3);  // false (s3 is different heap object)
```

**When to use each**:
- **Literal** (`"text"`): 99% of the time, most efficient
- **new String()**: Rare cases - when you explicitly want a separate object (usually not needed)

---

## intern() Method

Moves string to pool or returns existing pool reference.

```java
String s1 = new String("Hello");  // In heap (outside pool)
String s2 = s1.intern();           // Checks pool: if exists, returns it; if not, adds s1 to pool
String s3 = "Hello";               // Pool reference

System.out.println(s1 == s2);     // false (s1 in heap, s2 in pool)
System.out.println(s2 == s3);     // true (both in pool)
System.out.println(s1.equals(s2)); // true (content is same)
```

**How intern() works**:
1. Check if string exists in pool
2. If found: return reference to existing pool string
3. If not found: add this string to pool and return reference

**Use case**: Memory optimization when you have many duplicate strings from external sources (database, files)
```java
// Loading strings from database that might have duplicates
String nameFromDB = getFromDatabase();  // Could be "John", "Jane", etc.
String optimized = nameFromDB.intern();  // Reuse pool if duplicate exists
```

**Warning**: intern() can be slow (searches entire pool) - use only when necessary

---

## String vs StringBuilder vs StringBuffer

| Feature | String | StringBuilder | StringBuffer |
|---------|--------|---------------|--------------|
| Mutable | **No** | **Yes** | **Yes** |
| Thread-safe | Yes (immutable) | **No** | **Yes** (synchronized) |
| Performance | Slow (creates many objects) | **Fast** | Medium (synchronization overhead) |
| Memory | High (many objects) | Low (single object) | Low (single object) |
| Use when | Fixed strings, keys | Single thread concatenation | Multi-thread concatenation |

```java
// ❌ Bad: Creates 1000 String objects (huge overhead!)
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // Each += creates new String, old one → GC
}
// Memory: O(n²) - quadratic!

// ✅ Good: Single StringBuilder, appends to same buffer
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);  // Reuses same char buffer, just expands
}
String result = sb.toString();
// Memory: O(n) - linear!

// Difference: Loops with +=" can be 10-100x slower with strings
```

**Why StringBuilder is mutable**:
- Has internal char array that grows as needed
- Doesn't create new object on each operation
- Much more efficient for building strings dynamically

**When to use each**:
- **String**: When value won't change (fields, constants)
- **StringBuilder**: Loops, dynamic building (most common)
- **StringBuffer**: Rare - only when multi-threaded access to same buffer

---

## String Comparison

### == vs equals()

- **==**: Compares **memory references** (are they the same object?)
- **equals()**: Compares **content** (do they have the same characters?)

```java
String s1 = "Java";           // Pool
String s2 = "Java";           // Pool
String s3 = new String("Java"); // Heap

// Reference comparison (==)
s1 == s2      // true  (both point to same pool object)
s1 == s3      // false (different objects in memory)

// Content comparison (equals)
s1.equals(s2)  // true  (both have same content)
s1.equals(s3)  // true  (both have same content)
s3.equals(s1)  // true  (content is identical)
```

**Important**: 
- `==` works for literals by accident (String Pool), but **UNRELIABLE**
- Always use `equals()` for string comparison!

```java
// This works but for WRONG reason (pool reuse)
if ("admin".equals(username)) { }  // ✅ Correct way

// This might fail if usernames are from external sources
if (username == "admin") { }  // ❌ Wrong - unreliable
```

**Null-safe comparison**:
```java
// NPE risk if username is null
if (username.equals("admin")) { }  // ❌ Throws NPE if null

// Safe: String literal can't be null
if ("admin".equals(username)) { }  // ✅ Returns false if username is null
```

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

1. **Use string literals**: `String s = "Hello";` not `new String("Hello")`
   - Literals use String Pool → memory efficient
   
2. **Use StringBuilder for concatenation in loops**
   - String += in loop = memory disaster
   - StringBuilder = single object, reused
   
3. **Always use equals() not ==** for comparison
   - == checks references (unreliable)
   - equals() checks content (correct)
   
4. **Avoid NPE with null-safe comparison**
   - Use `"constant".equals(variable)` not `variable.equals("constant")`
   - Literal can't be null, prevents NullPointerException
   
5. **Use intern() wisely**: Only for many duplicate strings
   - intern() is slow (searches pool)
   - Use only when memory is critical and duplicates are common

6. **Use String.valueOf() or toString() for type conversion**
   ```java
   int num = 42;
   String s1 = "" + num;              // ❌ Creates String object
   String s2 = String.valueOf(num);   // ✅ Direct conversion
   String s3 = Integer.toString(num); // ✅ Also good
   ```

---

## Key Concepts

- **Immutability**: Strings can't be modified
- **String Pool**: Memory optimization for literals
- **new vs literal**: Literal uses pool, new creates heap object
- **StringBuilder**: Mutable, use for concatenation
- **equals() vs ==**: Content vs reference comparison

---

## Next: [Static vs Non-Static](05-Static-Access-Modifiers.md)
