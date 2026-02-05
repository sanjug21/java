# 8. OOP PRINCIPLES

## Four Pillars of OOP

### 1. Encapsulation

**Bundling data and methods together, hiding internal implementation details behind controlled access**

```java
public class BankAccount {
    private double balance;  // Private field - only accessible within this class
    
    // Public getter - read-only access
    public double getBalance() {
        return balance;
    }
    
    // Public setter with validation - controlled write access
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;  // Can enforce business rules
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;  // Can prevent invalid operations
        }
    }
}
```

**Benefits:** 
- Data hiding: Direct access prevented
- Validation: Business rules enforced
- Flexibility: Can change implementation without breaking client code
- Security: Prevents accidental/malicious data corruption

---

### 2. Inheritance

**Child class inherits properties and methods from parent class, enabling code reuse and hierarchies**

```java
class Animal {
    void eat() { System.out.println("Eating"); }
}

class Dog extends Animal {  // Inherits from Animal
    void bark() { System.out.println("Barking"); }
}

public class InheritanceExample {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.eat();   // Inherited from Animal
        dog.bark();  // Own method
    }
}
```

**Types of Inheritance**:
- **Single**: A → B (most common)
- **Multilevel**: A → B → C (chain)
- **Hierarchical**: A → B, A → C (multiple children)
- **Multiple**: Via interfaces only (Java doesn't support class multiple inheritance)

---

### 3. Polymorphism

**Same method name behaves differently based on the object or parameters calling it**

**Compile-time Polymorphism (Method Overloading):**
```java
class Calculator {
    // Same method name, different parameters
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
// Compiler knows which to call based on arguments
```

**Runtime Polymorphism (Method Overriding):**
```java
class Animal {
    void sound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }  // Overrides parent
}

class Cat extends Animal {
    @Override
    void sound() { System.out.println("Meow"); }  // Overrides parent
}

public class PolymorphismExample {
    public static void main(String[] args) {
        Animal a1 = new Dog();    // Reference type: Animal, actual type: Dog
        Animal a2 = new Cat();    // Reference type: Animal, actual type: Cat
        a1.sound();  // Bark (JVM calls Dog's sound())
        a2.sound();  // Meow (JVM calls Cat's sound())
    }
}
```

**Key**: Same reference type, different actual types → JVM calls appropriate method

---

### 4. Abstraction

**Hiding implementation details, showing only essential features/interface to the user**

**Using Abstract Class:**
```java
abstract class Shape {
    abstract double area();  // Abstract method - no implementation
    
    void display() {  // Concrete method - has implementation
        System.out.println("Area: " + area());
    }
}

class Circle extends Shape {
    double radius;
    
    Circle(double radius) { this.radius = radius; }
    
    @Override
    double area() {
        return Math.PI * radius * radius;  // Must implement abstract method
    }
}
// User doesn't need to know HOW area() is calculated
```

**Using Interface:**
```java
interface Drawable {
    void draw();  // Abstract method - contract only
}

class Rectangle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing rectangle");
    }
}
// Client uses Drawable, doesn't care which class implements it
```

**Benefits**:
- Simplifies API: User sees only what's needed
- Flexibility: Can change implementation without affecting client code
- Enforces contracts: Must implement abstract methods

---

## Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Methods | Abstract + Concrete | Abstract (+ default from Java 8+) |
| Variables | Any type | public static final only |
| Multiple Inheritance | No (single parent) | Yes (multiple implements) |
| Constructor | Yes (initialize state) | No |
| Access Modifiers | Any (public, private, etc.) | public only |
| Use Case | **IS-A** relationship (Car IS-A Vehicle) | **CAN-DO** capability (Car CAN-DO Drivable) |
| Instantiation | Cannot instantiate | Cannot instantiate |

**When to use**:
- **Abstract Class**: When classes share code, state, or IS-A relationship
- **Interface**: For contracts, multiple inheritance, or defining capability

---

## Composition vs Inheritance

**Inheritance (IS-A):**
```java
class Animal { }
class Dog extends Animal { }  // Dog IS-A Animal
```

**Composition (HAS-A):**
```java
class Engine {
    void start() { System.out.println("Engine started"); }
}

class Car {
    private Engine engine = new Engine();  // Car HAS-A Engine
    
    void start() {
        engine.start();
    }
}
```

**Favor composition over inheritance** when:
- No true IS-A relationship exists
- Need flexibility to change behavior at runtime
- Want to avoid tight coupling to parent class
- Need multiple behaviors (composition can combine multiple objects)

**Example**: A car HAS-A engine (composition), HAS-A transmission, HAS-A wheels
- Better than: Car extends Engine, Car extends Transmission (multiple inheritance issues)

---

## Key Takeaways

✓ **Encapsulation:** Hide data, expose methods
✓ **Inheritance:** Code reuse through parent-child relationship
✓ **Polymorphism:** One interface, multiple implementations
✓ **Abstraction:** Hide complexity, show essential features
✓ Prefer composition for flexibility

**Next:** [09. Inheritance & Polymorphism Details](09-Inheritance-Polymorphism.md)
