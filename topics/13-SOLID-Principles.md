# 13. SOLID PRINCIPLES

## S - Single Responsibility Principle

**A class should have only one reason to change**

```java
// VIOLATION
class User {
    void saveToDatabase() { /* DB logic */ }
    void sendEmail() { /* Email logic */ }
    void validateUser() { /* Validation */ }
}

// SOLUTION
class UserRepository {
    void save(User user) { /* DB logic */ }
}

class EmailService {
    void sendEmail(User user) { /* Email logic */ }
}

class UserValidator {
    boolean validate(User user) { /* Validation */ }
}
```

**Benefit:** Easy to maintain, test, and modify

---

## O - Open/Closed Principle

**Open for extension, closed for modification**

```java
// VIOLATION
class PaymentProcessor {
    void process(String method, double amount) {
        if ("credit".equals(method)) { /* Credit logic */ }
        else if ("upi".equals(method)) { /* UPI logic */ }
    }
}

// SOLUTION
interface PaymentMethod {
    void process(double amount);
}

class CreditCard implements PaymentMethod {
    public void process(double amount) { /* Credit logic */ }
}

class UPI implements PaymentMethod {
    public void process(double amount) { /* UPI logic */ }
}

class PaymentGateway {
    void pay(PaymentMethod method, double amount) {
        method.process(amount);
    }
}
```

**Benefit:** Add new features without changing existing code

---

## L - Liskov Substitution Principle

**Subtypes must be substitutable for their base types**

```java
// VIOLATION
class Bird {
    void fly() { System.out.println("Flying"); }
}

class Penguin extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException();  // Violates LSP
    }
}

// SOLUTION
interface Animal {
    void move();
}

interface Flyable {
    void fly();
}

class Eagle implements Animal, Flyable {
    public void move() { System.out.println("Moving"); }
    public void fly() { System.out.println("Flying"); }
}

class Penguin implements Animal {
    public void move() { System.out.println("Waddling"); }
}
```

**Benefit:** Proper inheritance hierarchy

---

## I - Interface Segregation Principle

**Clients shouldn't depend on interfaces they don't use**

```java
// VIOLATION
interface Worker {
    void work();
    void eat();
    void sleep();
}

class Robot implements Worker {
    public void work() { }
    public void eat() { /* Meaningless */ }
    public void sleep() { /* Meaningless */ }
}

// SOLUTION
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    public void work() { }
    public void eat() { }
    public void sleep() { }
}

class Robot implements Workable {
    public void work() { }
}
```

**Benefit:** No forced implementation of unused methods

---

## D - Dependency Inversion Principle

**Depend on abstractions, not concretions**

```java
// VIOLATION
class MySQLDatabase {
    void save(String data) { /* MySQL logic */ }
}

class UserService {
    private MySQLDatabase db = new MySQLDatabase();  // Tight coupling
    
    void saveUser(String data) {
        db.save(data);
    }
}

// SOLUTION
interface Database {
    void save(String data);
}

class MySQLDatabase implements Database {
    public void save(String data) { /* MySQL logic */ }
}

class MongoDatabase implements Database {
    public void save(String data) { /* Mongo logic */ }
}

class UserService {
    private Database db;  // Depends on abstraction
    
    UserService(Database db) {
        this.db = db;
    }
    
    void saveUser(String data) {
        db.save(data);
    }
}

// Usage
Database mysql = new MySQLDatabase();
UserService service = new UserService(mysql);
service.saveUser("data");
```

**Benefit:** Loose coupling, easy to swap implementations

---

## SOLID Summary

| Principle | Meaning | Benefit |
|-----------|---------|---------|
| **S** - SRP | One class, one responsibility | Easy maintenance |
| **O** - OCP | Open for extension, closed for modification | Add features safely |
| **L** - LSP | Subtypes are substitutable | Proper inheritance |
| **I** - ISP | No unused interface methods | Lean interfaces |
| **D** - DIP | Depend on abstractions | Loose coupling |

---

## Real-World Example

```java
// Database abstraction (DIP)
interface Repository {
    void save(User user);
    User findById(int id);
}

// Single responsibility (SRP)
class UserRepository implements Repository {
    public void save(User user) { /* DB logic */ }
    public User findById(int id) { /* DB logic */ }
}

// Open/Closed (OCP) - can add new validators without modifying
interface Validator {
    boolean validate(User user);
}

class EmailValidator implements Validator {
    public boolean validate(User user) { /* Email validation */ }
}

class AgeValidator implements Validator {
    public boolean validate(User user) { /* Age validation */ }
}

// Service using DIP
class UserService {
    private Repository repo;
    private List<Validator> validators;
    
    UserService(Repository repo, List<Validator> validators) {
        this.repo = repo;
        this.validators = validators;
    }
    
    void registerUser(User user) {
        for (Validator v : validators) {
            if (!v.validate(user)) return;
        }
        repo.save(user);
    }
}
```

---

## Key Takeaways

✓ **SRP:** One class, one job
✓ **OCP:** Extend without modifying existing code
✓ **LSP:** Subclasses must honor parent contract
✓ **ISP:** Keep interfaces focused
✓ **DIP:** Program to interfaces, not implementations

**Next:** [14. Collections Framework](14-Collections-Framework.md)
