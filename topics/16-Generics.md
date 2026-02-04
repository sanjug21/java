# 16. GENERICS

## Generic Classes

```java
class Box<T> {
    private T value;
    
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

// Usage
Box<Integer> intBox = new Box<>();
intBox.set(100);
System.out.println(intBox.get());  // 100

Box<String> strBox = new Box<>();
strBox.set("Hello");
System.out.println(strBox.get());  // Hello
```

---

## Generic Methods

```java
class GenericMethods {
    // Generic method
    static <T> void printArray(T[] arr) {
        for (T element : arr) {
            System.out.println(element);
        }
    }
    
    // Bounded generic
    static <T extends Number> double sum(T[] arr) {
        double total = 0;
        for (T num : arr) {
            total += num.doubleValue();
        }
        return total;
    }
}

// Usage
Integer[] intArr = {1, 2, 3};
printArray(intArr);
System.out.println(sum(intArr));  // 6.0
```

---

## Bounded Type Parameters

```java
// Upper bound - T must be Number or subclass
<T extends Number>

// Multiple bounds
<T extends Number & Comparable<T>>

// Example
static <T extends Comparable<T>> T findMax(T[] arr) {
    T max = arr[0];
    for (T element : arr) {
        if (element.compareTo(max) > 0) {
            max = element;
        }
    }
    return max;
}
```

---

## Wildcards

### Upper Bound Wildcard (? extends T)

**Read-only, accepts T and subtypes**

```java
static void printNumbers(List<? extends Number> list) {
    for (Number num : list) {
        System.out.println(num);
    }
}

// Usage
List<Integer> intList = Arrays.asList(1, 2, 3);
List<Double> doubleList = Arrays.asList(1.5, 2.5);
printNumbers(intList);    // OK
printNumbers(doubleList); // OK
```

### Lower Bound Wildcard (? super T)

**Write-only, accepts T and supertypes**

```java
static void addNumbers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}

// Usage
List<Number> numList = new ArrayList<>();
List<Object> objList = new ArrayList<>();
addNumbers(numList);  // OK
addNumbers(objList);  // OK
```

### Unbounded Wildcard (?)

**Any type**

```java
static void printList(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

---

## PECS Principle

**Producer Extends, Consumer Super**

- **Producer (`? extends T`):** When you read/get data
- **Consumer (`? super T`):** When you write/put data

```java
// Producer - read from source
static <T> void copy(List<? extends T> source, List<? super T> dest) {
    for (T item : source) {
        dest.add(item);
    }
}

List<Integer> intList = Arrays.asList(1, 2, 3);
List<Number> numList = new ArrayList<>();
copy(intList, numList);  // OK: Integer extends Number
```

---

## Type Erasure

**Generics are compile-time only, erased at runtime**

```java
List<String> list1 = new ArrayList<>();
List<Integer> list2 = new ArrayList<>();

// At runtime, both become List (raw type)
System.out.println(list1.getClass() == list2.getClass());  // true
```

**Implications:**
- Cannot create generic arrays: `new T[10]` ❌
- Cannot use instanceof: `obj instanceof List<String>` ❌
- Cannot create generic exceptions
- Static fields cannot be generic

---

## Generic Interfaces

```java
interface Pair<K, V> {
    K getKey();
    V getValue();
}

class OrderedPair<K, V> implements Pair<K, V> {
    private K key;
    private V value;
    
    OrderedPair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() { return key; }
    public V getValue() { return value; }
}

// Usage
Pair<String, Integer> pair = new OrderedPair<>("Age", 22);
System.out.println(pair.getKey() + ": " + pair.getValue());
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

✓ Use generics for type safety
✓ Use `? extends T` when reading
✓ Use `? super T` when writing
✓ Prefer generic types over raw types
✓ Use bounded wildcards for flexibility

```java
// BAD - raw type
List list = new ArrayList();

// GOOD - generic type
List<String> list = new ArrayList<>();
```

---

## Key Takeaways

✓ **Generics:** Type safety, reusability
✓ **Bounded types:** Restrict to subtypes/supertypes
✓ **Wildcards:** Flexible parameter types
✓ **PECS:** Producer extends, Consumer super
✓ **Type erasure:** Generics removed at runtime

**Next:** [17. Lambda Expressions](17-Lambda-Expressions.md)
