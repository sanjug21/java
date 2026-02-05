# 17. LAMBDA EXPRESSIONS

## Syntax

**Concise syntax for anonymous function implementations - bridges functional programming with OOP**

```java
// Traditional anonymous class (Java 7 and before)
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};

// Lambda expression (Java 8+) - cleaner, more readable
Runnable r2 = () -> System.out.println("Running");
```

**Syntax:** `(parameters) -> expression` or `(parameters) -> { statements }`

**How it works:**
- Compiler infers the functional interface type from context
- Parameters matched to interface method parameters
- Expression/block becomes the method body
- No need for `new`, method name, or return statement (for single expressions)

---

## Functional Interfaces

**Interface with exactly one abstract method - enables lambda assignment**

```java
@FunctionalInterface  // Compiler enforces single abstract method
interface Calculator {
    int calculate(int a, int b);  // Single abstract method
    
    // Can have default/static methods too
    default void info() { System.out.println("Calculator"); }
}

// Lambda creates anonymous implementation of Calculator
Calculator add = (a, b) -> a + b;           // implements calculate()
Calculator multiply = (a, b) -> a * b;      // different implementation

System.out.println(add.calculate(5, 3));       // 8
System.out.println(multiply.calculate(5, 3));  // 15
```

**Why @FunctionalInterface matters:**
- Compiler verifies interface has exactly one abstract method
- Prevents accidental addition of second method breaking lambda
- Not required but recommended for clarity and safety
- Interface is functional even without annotation if it has one abstract method

---

## Built-in Functional Interfaces

**Java provides common interfaces to avoid creating custom ones**

### Predicate<T>

**Input: one argument | Output: boolean - used for filtering/testing**

```java
Predicate<Integer> isEven = num -> num % 2 == 0;
System.out.println(isEven.test(4));  // true
System.out.println(isEven.test(5));  // false

// Use in Stream API
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
nums.stream().filter(isEven).forEach(System.out::println);  // 2, 4
```

### Function<T, R>

**Input: one argument of type T | Output: result of type R - transformation**

```java
Function<String, Integer> length = str -> str.length();
System.out.println(length.apply("Hello"));  // 5

// Chaining functions
Function<Integer, Integer> double_val = n -> n * 2;
Function<String, Integer> processLength = length.andThen(double_val);
System.out.println(processLength.apply("Hi"));  // 4 (2 chars * 2)
```

### Consumer<T>

**Input: one argument | Output: void (side effects only)**

```java
Consumer<String> print = str -> System.out.println(str);
print.accept("Hello");  // Hello

Consumer<Integer> addToList = list::add;  // Method reference
```

### Supplier<T>

**Input: no arguments | Output: result of type T - factory/generator**

```java
Supplier<Double> random = () -> Math.random();
System.out.println(random.get());  // Random number

Supplier<User> userFactory = () -> new User("John", 25);
User user = userFactory.get();
```

### BiFunction<T, U, R>

**Input: two arguments (types T, U) | Output: result (type R)**

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(5, 3));  // 8

// With lambdas: multiple params need parentheses
BiFunction<String, String, String> combine = (s1, s2) -> s1 + " " + s2;
System.out.println(combine.apply("Hello", "World"));  // Hello World
```

**Other variants:** `BiPredicate<T, U>`, `BiConsumer<T, U>`, etc.

---

## Lambda Syntax Variations

**Parameter handling and expression types - compiler infers what it can**

```java
// No parameters
Runnable r = () -> System.out.println("No params");

// One parameter (parentheses optional if just name)
Consumer<String> c1 = s -> System.out.println(s);          // Parentheses omitted
Consumer<String> c2 = (s) -> System.out.println(s);        // Parentheses included (both OK)
Consumer<String> c3 = (String s) -> System.out.println(s); // Type explicit (optional)

// Multiple parameters (parentheses required)
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

// Multiple statements (requires braces and explicit return if needed)
BiFunction<Integer, Integer, Integer> max = (a, b) -> {
    System.out.println("Comparing: " + a + " vs " + b);
    return a > b ? a : b;
};

// Type inference - compiler knows types from interface
BiFunction<Integer, Integer, Integer> multiply = (a, b) -> a * b;  // Types inferred

// Explicit type declaration (optional, rare)
BiFunction<Integer, Integer, Integer> divide = (Integer a, Integer b) -> a / b;
```

**Key rules:**
- Single expression: no braces, auto-return value
- Multiple statements: braces required, explicit return needed
- Single parameter: parentheses optional if type not explicit
- Multiple parameters: parentheses always required

---

## Method References

**Shorthand for lambda that calls a single existing method - even more concise**

**Format:** `Class::methodName` or `object::methodName`

### Static Method Reference

```java
// Lambda - calls static method
Function<String, Integer> parse1 = s -> Integer.parseInt(s);

// Method reference - cleaner
Function<String, Integer> parse2 = Integer::parseInt;
// Both do exactly the same thing, method reference is shorter

System.out.println(parse2.apply("123"));  // 123
```

### Instance Method Reference

```java
String str = "Hello";

// Lambda - calls instance method on captured object
Supplier<String> upper1 = () -> str.toUpperCase();

// Method reference - cleaner, captures str implicitly
Supplier<String> upper2 = str::toUpperCase;

System.out.println(upper2.get());  // HELLO
```

### Constructor Reference

```java
// Lambda - calls constructor
Supplier<List<String>> list1 = () -> new ArrayList<>();

// Method reference - cleaner
Supplier<List<String>> list2 = ArrayList::new;

// With arguments - creates instance with constructor args
Function<Integer, List<String>> listWithCapacity = ArrayList::new;
List<String> myList = listWithCapacity.apply(10);  // ArrayList with capacity 10
```

**When to use:**
- Simple call to single method → use method reference
- Complex logic or multiple operations → stick with lambda

---

## Lambda with Collections

**Common operations that accept lambdas - functional approach**

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// forEach - iterate and do something (Consumer)
names.forEach(name -> System.out.println(name));
names.forEach(System.out::println);  // Method reference version

// removeIf - conditional removal (Predicate)
names.removeIf(name -> name.startsWith("A"));  // Removes "Alice"

// sort - custom comparator (Comparator = BiFunction that returns int)
names.sort((a, b) -> a.compareTo(b));        // Lambda
names.sort(String::compareTo);               // Method reference

// replaceAll - transform each element (Function)
List<Integer> nums = Arrays.asList(1, 2, 3);
nums.replaceAll(n -> n * 2);  // [2, 4, 6]

// Example: filter and transform
nums.stream()
    .filter(n -> n > 2)           // Keep n > 2
    .map(n -> n * 10)             // Multiply by 10
    .forEach(System.out::println); // Print (result: 30)
```

**Methods that accept lambda - all take functional interfaces as parameters**

---

## Closures & Variable Capture

**Lambdas can access variables from enclosing scope - but with restrictions for thread safety**

```java
int factor = 2;  // Local variable - must be effectively final

Function<Integer, Integer> multiply = n -> n * factor;  // Captures factor
System.out.println(multiply.apply(5));  // 10 (5 * 2)

// factor = 3;  // ❌ COMPILATION ERROR
// Reason: Once lambda captures variable, it becomes effectively final
// If allowed to change, lambdas called later would see different value
// Creates ambiguity about which value to use

// This is OK: variable never changes
List<String> items = new ArrayList<>();
Consumer<String> addItem = item -> items.add(item);  // Captures 'items'
addItem.accept("Hello");  // OK: list reference doesn't change, contents do
```

**Effectively final definition:**
- Variable must be final, OR
- Never modified after initialization
- Compiler enforces this for lambda capture to prevent bugs

**Why this matters:**
- **Thread safety:** If lambda is used in concurrent context, value must be stable
- **Predictability:** Lambda behavior doesn't change based on external state changes
- **Debugging:** Easier to reason about lambda behavior

---

## Lambda vs Anonymous Class

**When to use each - subtle but important differences**

| Feature | Lambda | Anonymous Class |
|---------|--------|-----------------|
| Syntax | `(x) -> x * 2` | `new Interface() { public ... }` |
| Verbosity | Very concise | Verbose (5+ lines) |
| `this` keyword | Refers to **enclosing class** | Refers to **anonymous class instance** |
| Use case | Functional interfaces only | Any interface/abstract class |
| Performance | Better (invokedynamic bytecode) | Slower (creates class file) |
| Compilation | No new class file | Creates ClassNameN.class file |
| Variable capture | Effectively final vars | Effectively final vars |

**Example showing `this` difference:**
```java
class Outer {
    String outerVar = "Outer";
    
    void demo() {
        // Lambda - this = Outer instance
        Runnable lambda = () -> System.out.println(this.outerVar);
        
        // Anonymous - this = anonymous instance
        Runnable anon = new Runnable() {
            public void run() {
                System.out.println(this.outerVar);  // ❌ Anonymous has no outerVar
            }
        };
    }
}
```

**When to use:**
- **Lambda:** Functional interfaces (Stream API, callbacks, handlers)
- **Anonymous:** Abstract classes, or interfaces with multiple methods

---

## Practical Examples

### Comparator

```java
List<String> names = Arrays.asList("Charlie", "Alice", "Bob");

// Traditional anonymous class (pre-Java 8)
Collections.sort(names, new Comparator<String>() {
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});

// Lambda - much cleaner
Collections.sort(names, (a, b) -> a.compareTo(b));

// Method reference - even cleaner
Collections.sort(names, String::compareTo);

// Result: [Alice, Bob, Charlie]
```

### Thread

```java
// Traditional - boilerplate
new Thread(new Runnable() {
    public void run() {
        System.out.println("Running");
    }
}).start();

// Lambda - concise
new Thread(() -> System.out.println("Running")).start();
```

### List Processing

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

// Traditional loop
List<Integer> doubled = new ArrayList<>();
for (Integer n : nums) {
    if (n > 2) {
        doubled.add(n * 2);
    }
}

// Lambda + Stream
List<Integer> doubled = nums.stream()
    .filter(n -> n > 2)          // Predicate
    .map(n -> n * 2)             // Function
    .collect(Collectors.toList());

System.out.println(doubled);  // [6, 8, 10]
```

---

## Key Takeaways

✓ **Lambda:** Concise anonymous function - simplifies functional programming in Java

✓ **Functional interface:** Single abstract method - required for lambda assignment

✓ **Built-in interfaces:** Predicate (test), Function (transform), Consumer (side effect), Supplier (generate), BiFunction (two inputs)

✓ **Method reference:** Shorthand `Class::method` - even more concise for simple method calls

✓ **Variable capture:** Access effectively final variables - ensures thread safety and predictability

✓ **Collections support:** forEach, removeIf, sort, replaceAll - all accept lambdas

✓ **Performance:** Compiled to invokedynamic bytecode - efficient at runtime

**Next:** [18. Stream API](18-Stream-API.md)
