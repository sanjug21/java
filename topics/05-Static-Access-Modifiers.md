# Static vs Non-Static & Access Modifiers

## Static Members (Class-Level)

Belong to **class**, not objects. Shared across all instances.

```java
class Counter {
    static int count = 0;  // Shared by all objects
    String name;           // Each object has own copy
    
    Counter(String name) {
        this.name = name;
        count++;  // Shared counter
    }
    
    static void showCount() {  // Static method
        System.out.println(count);
    }
}

// Usage
Counter c1 = new Counter("First");
Counter c2 = new Counter("Second");
Counter.showCount();  // 2 (access via class name)
```

**Static Rules**:
- Can access only static members directly
- Can't use `this` or `super`
- Called via class name: `ClassName.staticMethod()`
- Loaded when class loads (before any object)

---

## Non-Static Members (Instance-Level)

Belong to **object**. Each object has own copy.

```java
class Student {
    String name;  // Instance variable
    
    void display() {  // Instance method
        System.out.println(name);
    }
}

// Usage
Student s1 = new Student();
s1.name = "Alice";
s1.display();  // Requires object
```

---

## Static vs Instance

| Feature | Static | Instance |
|---------|--------|----------|
| Belongs to | Class | Object |
| Memory | One copy in Metaspace | One per object in Heap |
| Access | `ClassName.member` | `object.member` |
| Can access | Only static | Both static & instance |
| Lifecycle | Class loading → Program end | Object creation → GC |

---

## Static Block

Executes **once** when class is loaded.

```java
class Config {
    static String value;
    
    static {
        System.out.println("Static block executed");
        value = "Initialized";
    }
}
```

---

## Access Modifiers

Control visibility and accessibility of classes, methods, variables.

| Modifier | Class | Package | Subclass | World |
|----------|-------|---------|----------|-------|
| **private** | ✓ | ✗ | ✗ | ✗ |
| **default** | ✓ | ✓ | ✗ | ✗ |
| **protected** | ✓ | ✓ | ✓ | ✗ |
| **public** | ✓ | ✓ | ✓ | ✓ |

### Private
Accessible only **within same class**.

```java
class Person {
    private String ssn;  // Only within class
    
    private void secret() {
        System.out.println("Private method");
    }
}
```

### Default (Package-Private)
Accessible **within same package** (no keyword).

```java
class Helper {  // Default access
    int value;  // Default access
}
```

### Protected
Accessible **within package + subclasses**.

```java
class Parent {
    protected int value;
}

class Child extends Parent {
    void show() {
        System.out.println(value);  // OK (inherited)
    }
}
```

### Public
Accessible **from anywhere**.

```java
public class Utils {
    public static void help() {
        System.out.println("Public method");
    }
}
```

---

## Encapsulation

Hide internal data using **private** fields + **public** getters/setters.

```java
class User {
    private String name;
    private int age;
    
    // Getter
    public String getName() {
        return name;
    }
    
    // Setter with validation
    public void setAge(int age) {
        if (age >= 0 && age <= 120) {
            this.age = age;
        }
    }
}

// Usage
User user = new User();
user.setAge(25);  // Validated
```

**Benefits**: Data hiding, validation, flexibility, security

---

## Best Practices

1. **Make fields private**, provide getters/setters
2. **Use static for utilities**: `Math.max()`, `Collections.sort()`
3. **Use final for constants**: `static final double PI = 3.14159;`
4. **Minimize access**: Start with private, increase only if needed
5. **Prefer protected over public** for inheritance

---

## Key Concepts

- **Static**: Class-level, shared, accessed via class name
- **Instance**: Object-level, per-object, accessed via object
- **Access Modifiers**: Control visibility (private < default < protected < public)
- **Encapsulation**: Private fields + public getters/setters

---

## Next: [Class Hierarchies](06-Class-Hierarchies.md)
