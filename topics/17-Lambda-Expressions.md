# 17. LAMBDA EXPRESSIONS

## Syntax

```java
// Traditional anonymous class
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};

// Lambda expression (Java 8+)
Runnable r2 = () -> System.out.println("Running");
```

**Syntax:** `(parameters) -> expression` or `(parameters) -> { statements }`

---

## Functional Interfaces

**Interface with exactly one abstract method**

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}

// Lambda implementation
Calculator add = (a, b) -> a + b;
Calculator multiply = (a, b) -> a * b;

System.out.println(add.calculate(5, 3));       // 8
System.out.println(multiply.calculate(5, 3));  // 15
```

**@FunctionalInterface** annotation ensures single abstract method

---

## Built-in Functional Interfaces

### Predicate<T>

**Takes one argument, returns boolean**

```java
Predicate<Integer> isEven = num -> num % 2 == 0;
System.out.println(isEven.test(4));  // true
System.out.println(isEven.test(5));  // false
```

### Function<T, R>

**Takes one argument, returns result**

```java
Function<String, Integer> length = str -> str.length();
System.out.println(length.apply("Hello"));  // 5
```

### Consumer<T>

**Takes one argument, returns nothing**

```java
Consumer<String> print = str -> System.out.println(str);
print.accept("Hello");  // Hello
```

### Supplier<T>

**Takes no arguments, returns result**

```java
Supplier<Double> random = () -> Math.random();
System.out.println(random.get());  // Random number
```

### BiFunction<T, U, R>

**Takes two arguments, returns result**

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(5, 3));  // 8
```

---

## Lambda Syntax Variations

```java
// No parameters
Runnable r = () -> System.out.println("No params");

// One parameter (parentheses optional)
Consumer<String> c1 = s -> System.out.println(s);
Consumer<String> c2 = (s) -> System.out.println(s);

// Multiple parameters
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

// Multiple statements (requires braces)
BiFunction<Integer, Integer, Integer> max = (a, b) -> {
    if (a > b) return a;
    else return b;
};

// Type declaration (optional)
BiFunction<Integer, Integer, Integer> multiply = (Integer a, Integer b) -> a * b;
```

---

## Method References

**Shorthand for lambda that calls a single method**

### Static Method Reference

```java
// Lambda
Function<String, Integer> parse1 = s -> Integer.parseInt(s);

// Method reference
Function<String, Integer> parse2 = Integer::parseInt;
```

### Instance Method Reference

```java
String str = "Hello";

// Lambda
Supplier<String> upper1 = () -> str.toUpperCase();

// Method reference
Supplier<String> upper2 = str::toUpperCase;
```

### Constructor Reference

```java
// Lambda
Supplier<List<String>> list1 = () -> new ArrayList<>();

// Method reference
Supplier<List<String>> list2 = ArrayList::new;
```

---

## Lambda with Collections

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// forEach
names.forEach(name -> System.out.println(name));
names.forEach(System.out::println);  // Method reference

// removeIf
names.removeIf(name -> name.startsWith("A"));

// sort
names.sort((a, b) -> a.compareTo(b));
names.sort(String::compareTo);  // Method reference

// replaceAll
List<Integer> nums = Arrays.asList(1, 2, 3);
nums.replaceAll(n -> n * 2);  // [2, 4, 6]
```

---

## Closures & Variable Capture

**Lambdas can access effectively final variables from enclosing scope**

```java
int factor = 2;  // Effectively final

Function<Integer, Integer> multiply = n -> n * factor;
System.out.println(multiply.apply(5));  // 10

// factor = 3;  // ERROR: Cannot modify captured variable
```

---

## Lambda vs Anonymous Class

| Feature | Lambda | Anonymous Class |
|---------|--------|-----------------|
| Syntax | Concise | Verbose |
| `this` keyword | Refers to enclosing class | Refers to anonymous class |
| Use case | Functional interfaces only | Any interface/class |
| Performance | Better (invokedynamic) | Slower (new class) |

---

## Practical Examples

### Comparator

```java
List<String> names = Arrays.asList("Charlie", "Alice", "Bob");

// Traditional
Collections.sort(names, new Comparator<String>() {
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});

// Lambda
Collections.sort(names, (a, b) -> a.compareTo(b));

// Method reference
Collections.sort(names, String::compareTo);
```

### Thread

```java
// Traditional
new Thread(new Runnable() {
    public void run() {
        System.out.println("Running");
    }
}).start();

// Lambda
new Thread(() -> System.out.println("Running")).start();
```

---

## Key Takeaways

✓ **Lambda:** Concise anonymous function
✓ **Functional interface:** Single abstract method
✓ **Built-in interfaces:** Predicate, Function, Consumer, Supplier
✓ **Method reference:** Shorthand for simple lambdas
✓ **Variable capture:** Access effectively final variables

**Next:** [18. Stream API](18-Stream-API.md)
