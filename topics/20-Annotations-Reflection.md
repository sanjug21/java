# 20. ANNOTATIONS & REFLECTION

## What are Annotations?

**Metadata that provides information about code but is not part of code itself - doesn't directly affect code execution**

```java
// @Override is an annotation - tells compiler to check if method actually overrides
@Override
public String toString() {
    return "Custom string";
}

// @Deprecated marks code as outdated
@Deprecated
public void oldMethod() { }

// Custom annotation
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
```

**Key characteristics:**
- Use `@` symbol to denote annotations
- Processed at compile-time or runtime
- Don't affect code logic directly
- Provide metadata for tools, frameworks, or runtime

---

## Built-in Annotations

### @Override

**Marks method as overriding parent class method - compiler verifies**

```java
class Animal {
    public void sound() { System.out.println("Generic sound"); }
}

class Dog extends Animal {
    @Override
    public void sound() {  // Compiler checks: does parent have sound()?
        System.out.println("Bark");
    }
    
    // @Override
    // public void Sound() { }  // ❌ ERROR: method name doesn't match, catches typo
}
```

### @Deprecated

**Marks API as outdated - warns developers not to use**

```java
public class FileUtils {
    @Deprecated
    public static String readFile(String path) {
        return "content";  // Old way
    }
    
    @Deprecated(since = "2.0", forRemoval = true)  // Removed in version 2.0
    public static void slowMethod() { }
    
    public static String readFileNIO(String path) {
        return "content";  // New way - use this instead
    }
}
```

### @SuppressWarnings

**Tells compiler to ignore specific warnings**

```java
@SuppressWarnings("unchecked")
List rawList = new ArrayList();  // Normally warns: unchecked cast
rawList.add("string");

@SuppressWarnings({"unchecked", "deprecation"})
void legacyCode() {
    // Suppresses multiple warning types
}
```

### @FunctionalInterface

**Marks interface as functional - compiler verifies single abstract method**

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);  // Single abstract method
    
    // OK: default methods allowed
    default void display() { }
}

// ❌ This would error if you added second abstract method
```

---

## Custom Annotations

**Create your own annotations for specific needs**

```java
// Define annotation
@interface Author {
    String name();          // Required element
    String date() default ""; // Optional with default value
}

// Use annotation
@Author(name = "John", date = "2024-01-15")
class UserService {
    public void processUser() { }
}

// Accessing via reflection (runtime)
Author author = UserService.class.getAnnotation(Author.class);
System.out.println("Author: " + author.name());  // John
System.out.println("Date: " + author.date());    // 2024-01-15
```

### Annotation Retention

**Control when annotation is available - compile-time or runtime**

```java
// RetentionPolicy.SOURCE - discarded after compilation
@Retention(RetentionPolicy.SOURCE)
@interface CompileTimeOnly { }

// RetentionPolicy.CLASS - available in .class file but not at runtime
@Retention(RetentionPolicy.CLASS)
@interface ClassTimeOnly { }

// RetentionPolicy.RUNTIME - available at runtime via reflection
@Retention(RetentionPolicy.RUNTIME)
@interface RuntimeAnnotation { }
```

### Annotation Target

**Specify where annotation can be used - class, method, field, etc.**

```java
// Can be applied to classes only
@Target(ElementType.TYPE)
@interface ClassAnnotation { }

// Can be applied to methods and fields
@Target({ElementType.METHOD, ElementType.FIELD})
@interface MethodOrField { }

// Can be applied to any element
@Target(ElementType.ELEMENT_TYPE)
@interface AnyTarget { }

// Common targets: TYPE (class), METHOD, FIELD, PARAMETER, LOCAL_VARIABLE, CONSTRUCTOR
```

---

## What is Reflection?

**Ability to examine and modify code/data at runtime - inspect classes, methods, fields dynamically**

```java
// Get class object
Class<?> clazz = String.class;
// or
Class<?> clazz = Class.forName("java.lang.String");

// Get class name
System.out.println(clazz.getName());  // java.lang.String

// Get methods
Method[] methods = clazz.getMethods();
for (Method method : methods) {
    System.out.println(method.getName());
}

// Get fields
Field[] fields = clazz.getDeclaredFields();
```

---

## Reflection: Inspecting Classes

**Examine class structure - methods, fields, constructors, modifiers**

```java
class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public void greet() {
        System.out.println("Hello, I'm " + name);
    }
}

// Get class
Class<?> clazz = Person.class;

// Get constructors
Constructor<?>[] constructors = clazz.getDeclaredConstructors();
System.out.println("Constructors: " + constructors.length);  // 1

// Get methods
Method[] methods = clazz.getDeclaredMethods();
for (Method method : methods) {
    System.out.println("Method: " + method.getName());  // greet
}

// Get fields
Field[] fields = clazz.getDeclaredFields();
for (Field field : fields) {
    System.out.println("Field: " + field.getName());  // name, age
}

// Get modifiers
int modifiers = clazz.getModifiers();
boolean isPublic = Modifier.isPublic(modifiers);
boolean isFinal = Modifier.isFinal(modifiers);
```

---

## Reflection: Calling Methods Dynamically

**Invoke methods at runtime without knowing method names at compile-time**

```java
class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int multiply(int a, int b) {
        return a * b;
    }
}

// Create instance
Calculator calc = new Calculator();
Class<?> clazz = Calculator.class;

// Get method dynamically by name
Method addMethod = clazz.getMethod("add", int.class, int.class);

// Invoke method using reflection
int result = (int) addMethod.invoke(calc, 5, 3);  // calc.add(5, 3)
System.out.println(result);  // 8

// Get and invoke different method
Method multiplyMethod = clazz.getMethod("multiply", int.class, int.class);
result = (int) multiplyMethod.invoke(calc, 5, 3);
System.out.println(result);  // 15
```

---

## Reflection: Accessing Fields Dynamically

**Read and modify field values at runtime - even private fields**

```java
class User {
    private String username;
    private int userId;
    
    public User(String username, int userId) {
        this.username = username;
        this.userId = userId;
    }
}

User user = new User("john", 101);
Class<?> clazz = User.class;

// Get field
Field userNameField = clazz.getDeclaredField("username");

// Make accessible (bypass private restriction)
userNameField.setAccessible(true);

// Read field value
String username = (String) userNameField.get(user);
System.out.println(username);  // john

// Modify field value
userNameField.set(user, "jane");
System.out.println(userNameField.get(user));  // jane

// Same for numeric fields
Field userIdField = clazz.getDeclaredField("userId");
userIdField.setAccessible(true);
int userId = (int) userIdField.get(user);
System.out.println(userId);  // 101
```

---

## Reflection: Creating Instances

**Instantiate objects dynamically - useful for frameworks**

```java
class Product {
    private String name;
    private double price;
    
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    @Override
    public String toString() {
        return "Product{" + name + ", $" + price + "}";
    }
}

try {
    // Get class
    Class<?> clazz = Class.forName("com.example.Product");
    
    // Get constructor with String and double parameters
    Constructor<?> constructor = clazz.getConstructor(String.class, double.class);
    
    // Create instance using constructor
    Object instance = constructor.newInstance("Laptop", 999.99);
    
    System.out.println(instance);  // Product{Laptop, $999.99}
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## Annotations with Reflection

**Combine annotations and reflection - frameworks use this heavily (Spring, JUnit, etc.)**

```java
// Define annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Test {
    String description() default "";
}

// Create class with annotated method
class TestSuite {
    @Test(description = "Addition test")
    public void testAdd() {
        System.out.println("Testing add...");
    }
    
    @Test(description = "Multiplication test")
    public void testMultiply() {
        System.out.println("Testing multiply...");
    }
    
    public void helperMethod() { }  // No annotation
}

// Reflection finds and executes annotated methods
TestSuite suite = new TestSuite();
Class<?> clazz = TestSuite.class;

for (Method method : clazz.getDeclaredMethods()) {
    if (method.isAnnotationPresent(Test.class)) {
        Test testAnnotation = method.getAnnotation(Test.class);
        System.out.println("Running: " + testAnnotation.description());
        method.invoke(suite);  // Execute the method
    }
}
```

---

## When to Use Reflection

**Powerful but use carefully - performance cost and complexity trade-off**

| Use Case | Why Reflection | Example |
|----------|---|---------|
| **Frameworks** | Discover annotated classes/methods at runtime | Spring's @Autowired, JUnit's @Test |
| **Serialization** | Convert objects to JSON/XML dynamically | Jackson, Gson libraries |
| **Dependency Injection** | Instantiate classes without knowing type at compile-time | Spring DI Container |
| **RPC/Proxies** | Forward method calls dynamically | RMI, web service handlers |
| **Debugging/Testing** | Access private fields/methods | Mocking libraries, test frameworks |

**Avoid reflection when:**
- Direct method calls work (reflection is slower)
- Compile-time type safety isn't provided
- Code readability suffers
- Performance is critical

---

## Performance Considerations

**Reflection is powerful but slower than direct calls**

```java
class PerformanceTest {
    public int calculate(int a, int b) {
        return a + b;
    }
}

PerformanceTest obj = new PerformanceTest();

// Direct call - FAST
long start = System.nanoTime();
for (int i = 0; i < 1000000; i++) {
    obj.calculate(5, 3);
}
long directTime = System.nanoTime() - start;

// Reflection call - SLOW (10-100x slower)
Method method = PerformanceTest.class.getMethod("calculate", int.class, int.class);
start = System.nanoTime();
for (int i = 0; i < 1000000; i++) {
    method.invoke(obj, 5, 3);
}
long reflectionTime = System.nanoTime() - start;

System.out.println("Direct: " + directTime);
System.out.println("Reflection: " + reflectionTime);
// Reflection typically 10-100x slower depending on operation
```

---

## Key Takeaways

✓ **Annotations:** Metadata that provides information about code (processed compile/runtime)

✓ **@Override, @Deprecated, @SuppressWarnings, @FunctionalInterface:** Common built-in annotations

✓ **Custom Annotations:** Define own using @interface, with @Retention and @Target

✓ **Reflection:** Examine and modify code/data at runtime using Class, Method, Field APIs

✓ **Reflection use cases:** Frameworks, serialization, dependency injection, testing

✓ **Performance:** Reflection is 10-100x slower than direct calls - use sparingly

✓ **Combination:** Annotations + Reflection = powerful pattern for frameworks

**Next:** [21. IO Programming](21-IO-Programming.md)
