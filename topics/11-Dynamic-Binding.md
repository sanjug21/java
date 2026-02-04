# 11. DYNAMIC BINDING & METHOD RESOLUTION

## Static vs Dynamic Binding

### Static Binding (Early Binding)

**Resolved at compile time**

```java
class Example {
    static void display() {
        System.out.println("Static method");
    }
    
    final void show() {
        System.out.println("Final method");
    }
    
    private void print() {
        System.out.println("Private method");
    }
}
```

**Bound at compile time:**
- `static` methods
- `final` methods
- `private` methods
- Instance variables

---

### Dynamic Binding (Late Binding)

**Resolved at runtime**

```java
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Bark");
    }
}

public class DynamicBindingExample {
    public static void main(String[] args) {
        Animal animal = new Dog();  // Reference: Animal, Object: Dog
        animal.sound();  // Bark (resolved at runtime)
    }
}
```

**Bound at runtime:**
- Overridden instance methods

---

## Method Resolution Order

```java
class Parent {
    void instanceMethod() { System.out.println("Parent instance"); }
    static void staticMethod() { System.out.println("Parent static"); }
}

class Child extends Parent {
    @Override
    void instanceMethod() { System.out.println("Child instance"); }
    
    static void staticMethod() { System.out.println("Child static"); }
}

public class MethodResolutionExample {
    public static void main(String[] args) {
        Parent obj = new Child();
        
        obj.instanceMethod();  // Child instance (dynamic binding)
        obj.staticMethod();    // Parent static (static binding)
    }
}
```

**Rule:** 
- Instance methods → resolved by **object type** (runtime)
- Static methods → resolved by **reference type** (compile time)

---

## Variable Hiding vs Method Overriding

```java
class Parent {
    int x = 10;
    
    void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    int x = 20;  // Variable hiding
    
    @Override
    void display() {  // Method overriding
        System.out.println("Child display");
    }
}

public class HidingVsOverridingExample {
    public static void main(String[] args) {
        Parent obj = new Child();
        
        System.out.println(obj.x);  // 10 (variables resolved by reference type)
        obj.display();              // Child display (methods resolved by object type)
    }
}
```

**Key Difference:**
- Variables → resolved by **reference type** (no dynamic binding)
- Methods → resolved by **object type** (dynamic binding)

---

## Virtual Method Invocation

```java
class Shape {
    void draw() { System.out.println("Drawing shape"); }
}

class Circle extends Shape {
    @Override
    void draw() { System.out.println("Drawing circle"); }
}

class Rectangle extends Shape {
    @Override
    void draw() { System.out.println("Drawing rectangle"); }
}

public class VirtualMethodExample {
    static void render(Shape shape) {  // Polymorphic parameter
        shape.draw();  // Virtual method call
    }
    
    public static void main(String[] args) {
        render(new Circle());     // Drawing circle
        render(new Rectangle());  // Drawing rectangle
    }
}
```

**Virtual method call:** Actual method determined at runtime based on object type

---

## Method Dispatch Mechanism

```
1. Compile Time:
   - Check if method exists in reference type
   - Validate access permissions
   
2. Runtime:
   - Start from actual object's class
   - Search for method implementation
   - If not found, check parent class
   - Continue up the hierarchy until found
```

---

## Practical Example - Payment Processing

```java
interface Payment {
    void processPayment(double amount);
}

class CreditCardPayment implements Payment {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing credit card: $" + amount);
    }
}

class UPIPayment implements Payment {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing UPI: ₹" + amount);
    }
}

class PaymentGateway {
    void pay(Payment payment, double amount) {  // Dynamic binding
        payment.processPayment(amount);
    }
}

public class PaymentExample {
    public static void main(String[] args) {
        PaymentGateway gateway = new PaymentGateway();
        
        gateway.pay(new CreditCardPayment(), 100.0);  // Credit card
        gateway.pay(new UPIPayment(), 500.0);         // UPI
    }
}
```

---

## Performance Considerations

| Binding Type | Performance | Flexibility |
|--------------|-------------|-------------|
| Static | Faster (compile-time) | Less flexible |
| Dynamic | Slower (runtime lookup) | More flexible |

**Optimization:** JVM uses JIT compilation and method inlining to optimize virtual calls

---

## Key Takeaways

✓ **Static binding:** Compile-time for static/final/private methods
✓ **Dynamic binding:** Runtime for overridden instance methods
✓ Variables always use static binding (reference type)
✓ Methods use dynamic binding (object type)
✓ Virtual method calls enable polymorphism

**Next:** [12. Design Patterns](12-Design-Patterns.md)
