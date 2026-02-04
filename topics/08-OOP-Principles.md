# 8. OOP PRINCIPLES

## Four Pillars of OOP

### 1. Encapsulation

**Bundling data and methods, hiding internal details**

```java
public class BankAccount {
    private double balance;  // Private field
    
    // Public getter
    public double getBalance() {
        return balance;
    }
    
    // Public setter with validation
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        }
    }
}
```

**Benefits:** Data hiding, validation, controlled access

---

### 2. Inheritance

**Child class inherits properties and methods from parent**

```java
class Animal {
    void eat() { System.out.println("Eating"); }
}

class Dog extends Animal {
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

**Types:**
- Single: A → B
- Multilevel: A → B → C
- Hierarchical: A → B, A → C
- Multiple (via interfaces): A implements B, C

---

### 3. Polymorphism

**Same method behaves differently based on object**

**Compile-time (Method Overloading):**
```java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
```

**Runtime (Method Overriding):**
```java
class Animal {
    void sound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}

class Cat extends Animal {
    @Override
    void sound() { System.out.println("Meow"); }
}

public class PolymorphismExample {
    public static void main(String[] args) {
        Animal a1 = new Dog();
        Animal a2 = new Cat();
        a1.sound();  // Bark (runtime polymorphism)
        a2.sound();  // Meow
    }
}
```

---

### 4. Abstraction

**Hiding implementation details, showing only essential features**

**Using Abstract Class:**
```java
abstract class Shape {
    abstract double area();  // Abstract method
    
    void display() {  // Concrete method
        System.out.println("Area: " + area());
    }
}

class Circle extends Shape {
    double radius;
    
    Circle(double radius) { this.radius = radius; }
    
    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}
```

**Using Interface:**
```java
interface Drawable {
    void draw();  // Abstract by default
}

class Rectangle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing rectangle");
    }
}
```

---

## Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Methods | Abstract + Concrete | Abstract (+ default from Java 8) |
| Variables | Any | public static final only |
| Multiple Inheritance | No | Yes |
| Constructor | Yes | No |
| Access Modifiers | Any | public only |
| Use Case | IS-A relationship | CAN-DO capability |

---

## Composition vs Inheritance

**Composition (HAS-A):**
```java
class Engine {
    void start() { System.out.println("Engine started"); }
}

class Car {
    private Engine engine = new Engine();  // HAS-A
    
    void start() {
        engine.start();
    }
}
```

**Favor composition over inheritance** when:
- No true IS-A relationship
- Need flexibility to change behavior at runtime
- Want to avoid tight coupling

---

## Key Takeaways

✓ **Encapsulation:** Hide data, expose methods
✓ **Inheritance:** Code reuse through parent-child relationship
✓ **Polymorphism:** One interface, multiple implementations
✓ **Abstraction:** Hide complexity, show essential features
✓ Prefer composition for flexibility

**Next:** [09. Inheritance & Polymorphism Details](09-Inheritance-Polymorphism.md)
