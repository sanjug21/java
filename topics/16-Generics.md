# 16. GENERICS

## Generic Classes

**Allows type-safe storage without explicit casting**

```java
class Box<T> {  // T is type parameter (placeholder)
    private T value;
    
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

// Usage - type specified at instantiation (type argument)
Box<Integer> intBox = new Box<>();  // T = Integer
intBox.set(100);
System.out.println(intBox.get());  // 100 (no cast needed)

Box<String> strBox = new Box<>();   // T = String
strBox.set("Hello");
System.out.println(strBox.get());  // Hello (no cast needed)
```

**Why it matters:**
- **Type Safety:** Compiler prevents `intBox.set("text")` at compile time
- **No Casting:** Unlike `Object box = new Box(); Integer val = (Integer) box.get();`
- **Reusability:** Single `Box<T>` class works for any type

---

## Generic Methods

**Type parameters scoped to individual method, inferred from arguments**

```java
class GenericMethods {
    // Generic method - type parameter <T> declared before return type
    static <T> void printArray(T[] arr) {
        for (T element : arr) {
            System.out.println(element);
        }
    }
    
    // Bounded generic - only Number or subtypes allowed
    static <T extends Number> double sum(T[] arr) {
        double total = 0;
        for (T num : arr) {
            total += num.doubleValue();  // OK: Number has doubleValue()
        }
        return total;
    }
}

// Usage - type inferred from argument
Integer[] intArr = {1, 2, 3};
printArray(intArr);           // T inferred as Integer
System.out.println(sum(intArr));  // 6.0, T = Integer (extends Number)
```

**Key Difference:**
- **Generic Class:** Type determined at instantiation `Box<Integer>`
- **Generic Method:** Type determined at call site, method to method

---

## Bounded Type Parameters

**Restricts type to specific class/interface or subtypes - enables method calls on generic type**

```java
// Upper bound - T must be Number or its subclass (Integer, Double, etc.)
<T extends Number>

// Multiple bounds - T must implement ALL (uses & not comma)
<T extends Number & Comparable<T>>  // T = Number AND Comparable

// Example - Comparable bound allows compareTo() call
static <T extends Comparable<T>> T findMax(T[] arr) {
    T max = arr[0];
    for (T element : arr) {
        if (element.compareTo(max) > 0) {  // OK: Comparable guarantees compareTo()
            max = element;
        }
    }
    return max;
}

// Works with: String, Integer, Double, any Comparable
Integer maxInt = findMax(new Integer[]{1, 5, 3});      // 5
String maxStr = findMax(new String[]{"a", "c", "b"});  // "c"
```

**Why bounds matter:**
- Without bounds: `<T>` can't call any methods (only from Object)
- With `<T extends Number>`: Can call `doubleValue()`, `intValue()` etc.
- Compile-time contract between code and caller

---

## Wildcards

**Use `?` when type doesn't matter or can be multiple related types**

### Upper Bound Wildcard (? extends T)

**Read-only from generic type, can write null only**

```java
static void printNumbers(List<? extends Number> list) {
    // Can READ as Number (safe cast from Integer, Double, etc.)
    for (Number num : list) {
        System.out.println(num);
    }
    // Cannot WRITE: list.add(1.5);  ❌ Compiler error
    // Reason: Don't know if list is List<Integer>, List<Double>, etc.
}

// Usage - accepts any List subtyped to Number
List<Integer> intList = Arrays.asList(1, 2, 3);
List<Double> doubleList = Arrays.asList(1.5, 2.5);
printNumbers(intList);    // OK: Integer is-a Number
printNumbers(doubleList); // OK: Double is-a Number
```

### Lower Bound Wildcard (? super T)

**Write-only to generic type, read back as Object only**

```java
static void addNumbers(List<? super Integer> list) {
    // Can WRITE Integer safely (supertype lists accept Integer)
    list.add(10);
    list.add(20);
    // Can only READ as Object: Object obj = list.get(0);  ⚠️
    // Reason: List could be List<Number>, List<Object>, List<Integer>
}

// Usage - accepts any List superttyped to Integer
List<Number> numList = new ArrayList<>();
List<Object> objList = new ArrayList<>();
addNumbers(numList);  // OK: Number is supertype of Integer
addNumbers(objList);  // OK: Object is supertype of Integer
```

### Unbounded Wildcard (?)

**When you don't care about type - most restrictive**

```java
static void printList(List<?> list) {
    for (Object obj : list) {  // Can only use Object
        System.out.println(obj);
    }
    // list.add("anything");  ❌ Cannot add anything (except null)
}
```

---

## PECS Principle

**Producer Extends, Consumer Super - mnemonic for wildcard usage**

**Producer (`? extends T`):** When you **read/get** data from collection
- Source that provides data
- Covariant (subtypes are safe to read as parent type)
- Example: `List<? extends Number> producer` → read as Number

**Consumer (`? super T`):** When you **write/put** data into collection
- Sink that accepts data
- Contravariant (supertypes are safe to receive subtypes)
- Example: `List<? super Integer> consumer` → write Integer safely

```java
// Classic example: copy data from source to destination
static <T> void copy(List<? extends T> source, List<? super T> dest) {
    // source: Producer - read data (could be subtypes)
    // dest: Consumer - write data (must be supertypes)
    for (T item : source) {
        dest.add(item);
    }
}

List<Integer> intList = Arrays.asList(1, 2, 3);
List<Number> numList = new ArrayList<>();
copy(intList, numList);  // OK: Integer extends Number (source), Number super Integer (dest)

List<Double> doubleList = Arrays.asList(1.5, 2.5);
copy(doubleList, numList);  // OK: Double extends Number (source)
```

---

## Type Erasure

**Generics are compile-time only - type information removed at runtime for JVM compatibility**

```java
List<String> list1 = new ArrayList<>();
List<Integer> list2 = new ArrayList<>();

// At compile-time: different types (type-safe checks happen)
// At runtime: both become raw List (generics erased)
System.out.println(list1.getClass() == list2.getClass());  // true ⚠️

// Why: JVM doesn't store <String> or <Integer> in bytecode
// Enables backward compatibility with pre-generics code
```

**Consequences - What You Cannot Do:**

| Cannot | Reason |
|--------|--------|
| `new T[10]` | Type T unknown at runtime |
| `instanceof List<String>` | Can't check erased type |
| `throw new T()` | Generic exceptions not allowed |
| `static T field;` | Static shared, type would be ambiguous |
| Cast doesn't check type | `(List<String>) rawList` unchecked |

**Workaround - Pass Class Token:**
```java
// BAD - loses type info
static <T> List<T> readList(File file) {
    return new ArrayList<T>();  // What is T?
}

// GOOD - explicit type passed
static <T> List<T> readList(File file, Class<T> elementType) {
    List<T> list = new ArrayList<>();
    // Now you can use elementType.newInstance() if needed
    return list;
}
```

---

## Generic Interfaces

**Interface type parameters inherited by implementing classes**

```java
interface Pair<K, V> {
    K getKey();
    V getValue();
}

class OrderedPair<K, V> implements Pair<K, V> {
    // Implementation must match generic interface
    private K key;
    private V value;
    
    OrderedPair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() { return key; }
    public V getValue() { return value; }
}

// Usage - type arguments specified at instantiation
Pair<String, Integer> pair = new OrderedPair<>("Age", 22);
System.out.println(pair.getKey() + ": " + pair.getValue());  // Age: 22

// Alternative: Fix type parameters in subclass
class StringIntPair implements Pair<String, Integer> {
    // K = String, V = Integer (fixed)
    private String key;
    private Integer value;
    // ...
}
```

**Multiple Type Parameters:**
```java
// Map<K, V> interface has two type parameters
Map<String, Integer> map = new HashMap<>();  // K=String, V=Integer
```

---

## Benefits vs Limitations

| Benefits | Limitations |
|----------|-------------|
| Type safety at compile-time | Type erasure at runtime |
| Eliminate casts | Cannot create generic arrays |
| Code reusability | Cannot use primitives directly |
| Early error detection | Cannot instantiate type parameters |

---

## Best Practices

✓ **Use generics for type safety** - Catch errors at compile-time, not runtime

✓ **Use `? extends T` when reading** - Covariant, safe for producers
  ```java
  public void process(List<? extends Number> numbers) { ... }
  ```

✓ **Use `? super T` when writing** - Contravariant, safe for consumers
  ```java
  public void fill(List<? super Integer> list) { ... }
  ```

✓ **Prefer generic types over raw types** - Eliminates unchecked warnings
  ```java
  // ❌ BAD - raw type, no type checking
  List list = new ArrayList();  // Type information lost
  
  // ✓ GOOD - generic type, full type safety
  List<String> list = new ArrayList<>();
  ```

✓ **Use bounded wildcards for flexibility** - Accept multiple related types
  ```java
  // Flexible: works with List<Integer>, List<Double>, etc.
  void printNumbers(List<? extends Number> nums) { ... }
  
  // Rigid: only List<Number>
  void printNumbers(List<Number> nums) { ... }
  ```

---

## Key Takeaways

✓ **Generics:** Type safety, reusability
✓ **Bounded types:** Restrict to subtypes/supertypes
✓ **Wildcards:** Flexible parameter types
✓ **PECS:** Producer extends, Consumer super
✓ **Type erasure:** Generics removed at runtime

**Next:** [17. Lambda Expressions](17-Lambda-Expressions.md)
