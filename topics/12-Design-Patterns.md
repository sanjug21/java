# 12. DESIGN PATTERNS

## Creational Patterns

### 1. Singleton

**Ensures only one instance of a class exists throughout application lifetime**

```java
public class Singleton {
    private static Singleton instance;  // Single instance held here
    
    private Singleton() { }  // Private constructor - prevents instantiation
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();  // Create only once
        }
        return instance;  // Return same instance always
    }
}

// Thread-safe version (recommended)
public class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() { }
    
    public static ThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (ThreadSafeSingleton.class) {  // Lock before checking again
                if (instance == null) {
                    instance = new ThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}

// Usage
Singleton s1 = Singleton.getInstance();
Singleton s2 = Singleton.getInstance();
// s1 == s2 → true (same object)
```

**Use cases**: Database connection, Logger, Configuration manager
**Benefit**: Guaranteed single instance, controlled global access point

---

### 2. Factory

**Creates objects without client specifying exact class - abstracts object creation**

```java
interface Shape {
    void draw();
}

class Circle implements Shape {
    public void draw() { System.out.println("Circle"); }
}

class Rectangle implements Shape {
    public void draw() { System.out.println("Rectangle"); }
}

class ShapeFactory {
    public Shape getShape(String type) {
        if ("CIRCLE".equals(type)) return new Circle();
        if ("RECTANGLE".equals(type)) return new Rectangle();
        return null;
    }
}

// Usage - Client doesn't need to know about Circle, Rectangle classes
ShapeFactory factory = new ShapeFactory();
Shape shape = factory.getShape("CIRCLE");  // Client only knows "CIRCLE" string
shape.draw();  // Circle
```

**Benefit**: 
- Client decoupled from concrete classes
- Easy to add new shapes without changing client code
- Centralized creation logic

---

### 3. Builder

**Constructs complex objects step by step - fluent interface for readable code**

```java
class User {
    private String firstName;
    private String lastName;
    private int age;
    private String email;
    
    private User(Builder builder) {  // Private constructor - use Builder
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.email = builder.email;
    }
    
    static class Builder {
        private String firstName;
        private String lastName;
        private int age;
        private String email;
        
        Builder setFirstName(String firstName) {
            this.firstName = firstName;
            return this;  // Return Builder for method chaining
        }
        
        Builder setLastName(String lastName) {
            this.lastName = lastName;
            return this;
        }
        
        Builder setAge(int age) {
            this.age = age;
            return this;
        }
        
        Builder setEmail(String email) {
            this.email = email;
            return this;
        }
        
        User build() {  // Final step - create immutable User
            return new User(this);
        }
    }
}

// Usage - Readable, flexible, optional parameters
User user = new User.Builder()
    .setFirstName("Pushpendra")
    .setAge(22)
    .setEmail("push@example.com")
    .build();  // Only set required fields, skip others
```

**Benefit**: 
- More readable than many constructor parameters
- Handle optional fields elegantly
- Immutable objects once built

---

## Structural Patterns

### 4. Adapter

**Converts interface of one class into another - bridges incompatible interfaces**

```java
interface MediaPlayer {
    void play(String filename);
}

interface AdvancedMediaPlayer {
    void playVlc(String filename);
    void playMp4(String filename);
}

class VlcPlayer implements AdvancedMediaPlayer {
    public void playVlc(String filename) {
        System.out.println("Playing VLC: " + filename);
    }
    public void playMp4(String filename) { }  // Not supported
}

// Adapter - converts AdvancedMediaPlayer interface to MediaPlayer
class MediaAdapter implements MediaPlayer {
    AdvancedMediaPlayer player;
    
    MediaAdapter(String type) {
        if ("vlc".equals(type)) {
            player = new VlcPlayer();
        }
    }
    
    public void play(String filename) {
        player.playVlc(filename);  // Adapt AdvancedMediaPlayer to MediaPlayer
    }
}

// Usage - Client expects MediaPlayer interface
MediaPlayer player = new MediaAdapter("vlc");
player.play("song.vlc");  // Works even though VlcPlayer has different interface
```

**Benefit**: Reuse existing classes with incompatible interfaces without modifying them

---

### 5. Decorator

**Adds new functionality to objects dynamically at runtime - wrapper pattern**

```java
interface Coffee {
    double cost();
    String description();
}

class SimpleCoffee implements Coffee {
    public double cost() { return 5.0; }
    public String description() { return "Simple Coffee"; }
}

abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;  // Wraps original object
    
    CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
}

class MilkDecorator extends CoffeeDecorator {
    MilkDecorator(Coffee coffee) { super(coffee); }
    
    public double cost() {
        return coffee.cost() + 1.5;  // Add to original cost
    }
    
    public String description() {
        return coffee.description() + ", Milk";  // Add to original description
    }
}

class SugarDecorator extends CoffeeDecorator {
    SugarDecorator(Coffee coffee) { super(coffee); }
    
    public double cost() {
        return coffee.cost() + 0.5;
    }
    
    public String description() {
        return coffee.description() + ", Sugar";
    }
}

// Usage - Stack decorators for combinations
Coffee coffee = new SimpleCoffee();  // 5.0, "Simple Coffee"
coffee = new MilkDecorator(coffee);  // 6.5, "Simple Coffee, Milk"
coffee = new SugarDecorator(coffee); // 7.0, "Simple Coffee, Milk, Sugar"
System.out.println(coffee.description() + " = $" + coffee.cost());
```

**Benefit**: Add features without modifying original class or creating subclasses for every combination

---

## Behavioral Patterns

### 6. Observer

**One-to-many dependency: when one object changes, notify all dependents automatically**

```java
import java.util.*;

interface Observer {
    void update(String message);
}

class ConcreteObserver implements Observer {
    private String name;
    
    ConcreteObserver(String name) { this.name = name; }
    
    public void update(String message) {
        System.out.println(name + " received: " + message);  // React to update
    }
}

class Subject {
    private List<Observer> observers = new ArrayList<>();
    
    void attach(Observer observer) {
        observers.add(observer);  // Register observer
    }
    
    void detach(Observer observer) {
        observers.remove(observer);  // Unregister observer
    }
    
    void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);  // Notify all
        }
    }
}

// Usage - Many observers react to one subject
Subject subject = new Subject();
subject.attach(new ConcreteObserver("Observer1"));
subject.attach(new ConcreteObserver("Observer2"));
subject.notifyObservers("Hello!");  // Both observers notified
// Output:
// Observer1 received: Hello!
// Observer2 received: Hello!
```

**Use cases**: Event listeners, MVC model updates, publish-subscribe systems

---

### 7. Strategy

**Family of interchangeable algorithms - select at runtime based on conditions**

```java
interface PaymentStrategy {
    void pay(int amount);
}

class CreditCardStrategy implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card");
    }
}

class UPIStrategy implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using UPI");
    }
}

class ShoppingCart {
    private PaymentStrategy strategy;  // Strategy can change at runtime
    
    void setPaymentStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;  // Switch algorithm dynamically
    }
    
    void checkout(int amount) {
        strategy.pay(amount);  // Use current strategy
    }
}

// Usage - Choose strategy based on user preference
ShoppingCart cart = new ShoppingCart();
cart.setPaymentStrategy(new CreditCardStrategy());
cart.checkout(100);  // Paid 100 using Credit Card

cart.setPaymentStrategy(new UPIStrategy());
cart.checkout(200);  // Paid 200 using UPI
```

**Benefit**: Easy to add new payment methods without modifying ShoppingCart

---

## Pattern Categories

| Category | Purpose | When to Use | Examples |
|----------|---------|------------|----------|
| **Creational** | Control object creation | Need control over instantiation | Singleton, Factory, Builder |
| **Structural** | Compose objects into structures | Need to organize object relationships | Adapter, Decorator, Proxy |
| **Behavioral** | Define object interactions | Need to distribute responsibilities | Observer, Strategy, Template |

**Quick Decision Guide**:
- Creating objects? → **Creational**
- Composing/wrapping objects? → **Structural**
- Defining behavior/communication? → **Behavioral**

---

## Key Takeaways

✓ **Singleton:** One instance only
✓ **Factory:** Object creation abstraction
✓ **Builder:** Step-by-step construction
✓ **Adapter:** Interface conversion
✓ **Decorator:** Dynamic feature addition
✓ **Observer:** Event notification
✓ **Strategy:** Algorithm selection at runtime

**Next:** [13. SOLID Principles](13-SOLID-Principles.md)
