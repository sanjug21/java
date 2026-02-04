# 12. DESIGN PATTERNS

## Creational Patterns

### 1. Singleton

**Ensures only one instance of a class**

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() { }  // Private constructor
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

// Thread-safe version
public class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() { }
    
    public static ThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (ThreadSafeSingleton.class) {
                if (instance == null) {
                    instance = new ThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}
```

---

### 2. Factory

**Creates objects without specifying exact class**

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

// Usage
ShapeFactory factory = new ShapeFactory();
Shape shape = factory.getShape("CIRCLE");
shape.draw();  // Circle
```

---

### 3. Builder

**Constructs complex objects step by step**

```java
class User {
    private String firstName;
    private String lastName;
    private int age;
    private String email;
    
    private User(Builder builder) {
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
            return this;
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
        
        User build() {
            return new User(this);
        }
    }
}

// Usage
User user = new User.Builder()
    .setFirstName("Pushpendra")
    .setAge(22)
    .setEmail("push@example.com")
    .build();
```

---

## Structural Patterns

### 4. Adapter

**Converts interface of a class into another interface**

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
    public void playMp4(String filename) { }
}

class MediaAdapter implements MediaPlayer {
    AdvancedMediaPlayer player;
    
    MediaAdapter(String type) {
        if ("vlc".equals(type)) {
            player = new VlcPlayer();
        }
    }
    
    public void play(String filename) {
        player.playVlc(filename);
    }
}
```

---

### 5. Decorator

**Adds new functionality to objects dynamically**

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
    protected Coffee coffee;
    
    CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
}

class MilkDecorator extends CoffeeDecorator {
    MilkDecorator(Coffee coffee) { super(coffee); }
    
    public double cost() {
        return coffee.cost() + 1.5;
    }
    
    public String description() {
        return coffee.description() + ", Milk";
    }
}

// Usage
Coffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
System.out.println(coffee.description() + " = $" + coffee.cost());
// Simple Coffee, Milk = $6.5
```

---

## Behavioral Patterns

### 6. Observer

**One-to-many dependency, notify all dependents**

```java
import java.util.*;

interface Observer {
    void update(String message);
}

class ConcreteObserver implements Observer {
    private String name;
    
    ConcreteObserver(String name) { this.name = name; }
    
    public void update(String message) {
        System.out.println(name + " received: " + message);
    }
}

class Subject {
    private List<Observer> observers = new ArrayList<>();
    
    void attach(Observer observer) {
        observers.add(observer);
    }
    
    void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }
}

// Usage
Subject subject = new Subject();
subject.attach(new ConcreteObserver("Observer1"));
subject.attach(new ConcreteObserver("Observer2"));
subject.notifyObservers("Hello!");
```

---

### 7. Strategy

**Family of algorithms, interchangeable at runtime**

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
    private PaymentStrategy strategy;
    
    void setPaymentStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }
    
    void checkout(int amount) {
        strategy.pay(amount);
    }
}

// Usage
ShoppingCart cart = new ShoppingCart();
cart.setPaymentStrategy(new CreditCardStrategy());
cart.checkout(100);  // Paid 100 using Credit Card
```

---

## Pattern Categories

| Category | Purpose | Examples |
|----------|---------|----------|
| Creational | Object creation | Singleton, Factory, Builder |
| Structural | Object composition | Adapter, Decorator, Proxy |
| Behavioral | Object interaction | Observer, Strategy, Template |

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
