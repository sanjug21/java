# 11. DYNAMIC BINDING & METHOD RESOLUTION

## Static vs Dynamic Binding

### Static Binding (Early Binding)

**Resolved at compile time - JVM knows exactly which method to call**

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

**Statically bound (compile-time resolution)**:
- `static` methods → cannot be overridden
- `final` methods → cannot be overridden
- `private` methods → cannot be overridden
- Instance variables → always resolved by reference type

**Why**: These cannot be overridden, so compiler knows exact method to call

---

### Dynamic Binding (Late Binding)

**Resolved at runtime - JVM determines which method to call based on actual object type**

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
        Animal animal = new Dog();  // Reference: Animal, Actual object: Dog
        animal.sound();  // JVM looks at Dog class → calls Dog's sound() → "Bark"
    }
}
```

**Dynamically bound (runtime resolution)**:
- Overridden instance methods only
- JVM looks at actual object type, not reference type
- Enables polymorphism - same reference can call different methods

**Why**: Methods CAN be overridden, so compiler must defer decision to runtime

---

## Method Resolution Order

Shows how JVM decides which method to call based on static vs dynamic binding:

```java
class Parent {
    void instanceMethod() { System.out.println("Parent instance"); }
    static void staticMethod() { System.out.println("Parent static"); }
}

class Child extends Parent {
    @Override
    void instanceMethod() { System.out.println("Child instance"); }  // Overrides
    
    static void staticMethod() { System.out.println("Child static"); }  // Hides
}

public class MethodResolutionExample {
    public static void main(String[] args) {
        Parent obj = new Child();  // Reference type: Parent, Object type: Child
        
        obj.instanceMethod();  // Child instance (dynamic binding - uses object type)
        obj.staticMethod();    // Parent static (static binding - uses reference type)
    }
}
```

**Key Rules**: 
- **Instance methods** → resolved by **object type at runtime** (dynamic binding)
- **Static methods** → resolved by **reference type at compile time** (static binding)
- **Variables** → resolved by **reference type** (never dynamic)

---

## Variable Hiding vs Method Overriding

**Important distinction - variables don't use dynamic binding!**

```java
class Parent {
    int x = 10;  // Parent's variable
    
    void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    int x = 20;  // Variable hiding - Child's x shadows Parent's x
    
    @Override
    void display() {  // Method overriding
        System.out.println("Child display");
    }
}

public class HidingVsOverridingExample {
    public static void main(String[] args) {
        Parent obj = new Child();  // Reference: Parent, Object: Child
        
        System.out.println(obj.x);  // 10 (Parent's x) - static binding
        obj.display();              // Child display (Child's method) - dynamic binding
    }
}
```

**Critical Difference**:
- **Variables** → resolved by **reference type** (static binding, never dynamic)
- **Methods** → resolved by **object type** (dynamic binding)
- Use methods, not variables, to leverage polymorphism

---

## Virtual Method Invocation

**How polymorphism works - method called based on runtime object type**

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
    static void render(Shape shape) {  // Accepts any Shape subclass
        shape.draw();  // Virtual method call - actual method determined at runtime
    }
    
    public static void main(String[] args) {
        render(new Circle());     // Calls Circle.draw() → "Drawing circle"
        render(new Rectangle());  // Calls Rectangle.draw() → "Drawing rectangle"
    }
}
```

**Virtual Method Call**: 
- Single method name `draw()`
- Different behavior based on object type
- This is the power of polymorphism - code is written for parent class, works with any subclass
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

**How JVM resolves method calls at runtime**

```
Compile Time:
  1. Check if method exists in reference type
  2. Check access permissions (public, protected, etc.)
  3. Verify parameters match
  4. If all OK → permit compilation
  
Runtime (Dynamic Dispatch):
  1. Get actual object type (what was instantiated)
  2. Start from actual object's class
  3. Search for method implementation in current class
  4. If not found → check parent class
  5. Continue up hierarchy until found
  6. Call the found method
```

**Example**:
```java
Shape s = new Circle();  // Actual type: Circle
s.draw();  // Runtime: Circle class has draw() → call Circle.draw()
```

This two-phase approach (compile-time validation + runtime dispatch) gives Java both safety and flexibility

---

## Practical Example - Payment Processing

**Real-world example of polymorphism through dynamic binding**

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
    void pay(Payment payment, double amount) {  // Accepts any Payment implementation
        payment.processPayment(amount);  // Virtual method call - actual implementation determined at runtime
    }
}

public class PaymentExample {
    public static void main(String[] args) {
        PaymentGateway gateway = new PaymentGateway();
        
        gateway.pay(new CreditCardPayment(), 100.0);  // Dispatches to CreditCard
        gateway.pay(new UPIPayment(), 500.0);         // Dispatches to UPI
    }
}
```

**Benefit**: `pay()` method never changes - add new payment types without modifying it

---

## Performance Considerations

| Binding Type | Performance | Flexibility | When Used |
|--------------|-------------|-------------|-----------|
| Static | Faster (resolved at compile) | Less flexible | static, final, private |
| Dynamic | Slower (lookup at runtime) | More flexible | overridden instance methods |

**Trade-off**: Dynamic binding is slightly slower than static binding due to runtime lookup
- JVM searches method table at runtime
- But: JVM uses JIT compilation and method inlining to optimize virtual calls
- In practice: Performance difference is negligible, benefits of polymorphism huge

**Optimization**: Modern JVMs cache method lookups, so repeated calls to same virtual method are very fast

---

## Key Takeaways

✓ **Static binding:** Compile-time for static/final/private methods
✓ **Dynamic binding:** Runtime for overridden instance methods
✓ Variables always use static binding (reference type)
✓ Methods use dynamic binding (object type)
✓ Virtual method calls enable polymorphism

**Next:** [12. Design Patterns](12-Design-Patterns.md)
