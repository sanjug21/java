# 7. AUTOBOXING & WRAPPER CLASSES

## Wrapper Classes

Wrapper classes convert primitive types to objects.

| Primitive | Wrapper Class |
|-----------|---------------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

---

## Autoboxing & Unboxing

**Autoboxing:** Automatic conversion from primitive to wrapper
**Unboxing:** Automatic conversion from wrapper to primitive

```java
public class AutoboxingExample {
    public static void main(String[] args) {
        // Autoboxing - primitive to Integer
        Integer num1 = 10;  // Auto: Integer num1 = Integer.valueOf(10);
        
        // Unboxing - Integer to primitive
        int num2 = num1;    // Auto: int num2 = num1.intValue();
        
        // In collections
        List<Integer> list = new ArrayList<>();
        list.add(5);        // Autoboxing
        int val = list.get(0);  // Unboxing
    }
}
```

---

## Integer Caching

Java caches Integer objects from -128 to 127 for performance.

```java
public class IntegerCachingExample {
    public static void main(String[] args) {
        Integer a = 127;
        Integer b = 127;
        System.out.println(a == b);  // true (cached)
        
        Integer c = 128;
        Integer d = 128;
        System.out.println(c == d);  // false (not cached, different objects)
        
        // Always use equals() for comparison
        System.out.println(c.equals(d));  // true
    }
}
```

**Cache Range:** -128 to 127 (can be configured with JVM option: `-XX:AutoBoxCacheMax=<size>`)

---

## Common Wrapper Methods

```java
public class WrapperMethods {
    public static void main(String[] args) {
        // Parsing
        int num = Integer.parseInt("123");
        double d = Double.parseDouble("45.67");
        
        // Value conversion
        Integer i = Integer.valueOf(100);
        String str = i.toString();
        
        // Comparison
        Integer x = 10, y = 20;
        System.out.println(Integer.compare(x, y));  // -1 (x < y)
        
        // Min/Max values
        System.out.println(Integer.MAX_VALUE);  // 2147483647
        System.out.println(Integer.MIN_VALUE);  // -2147483648
        
        // Binary/Hex/Octal
        System.out.println(Integer.toBinaryString(10));  // 1010
        System.out.println(Integer.toHexString(255));    // ff
        System.out.println(Integer.toOctalString(8));    // 10
    }
}
```

---

## Primitive vs Wrapper

| Feature | Primitive | Wrapper |
|---------|-----------|---------|
| Default Value | 0, false | null |
| Collections | No | Yes |
| Null Support | No | Yes |
| Performance | Faster | Slower (overhead) |
| Memory | Less | More |

---

## Best Practices

✓ Use primitives for performance-critical code
✓ Use wrappers in collections (generics)
✓ Always use `equals()` to compare wrapper objects
✓ Beware of NullPointerException during unboxing
✓ Avoid unnecessary boxing/unboxing in loops

```java
// BAD - boxing in loop
for (int i = 0; i < 1000; i++) {
    Integer num = i;  // Autoboxing 1000 times
}

// GOOD - use primitive
for (int i = 0; i < 1000; i++) {
    int num = i;
}
```

**Next:** [08. OOP Principles](08-OOP-Principles.md)
