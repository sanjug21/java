# 13. SOLID PRINCIPLES

## S - Single Responsibility Principle

**A class should have only one reason to change - one job, one reason to modify**

```java
// ❌ VIOLATION - Class has 3 reasons to change (DB change, Email service change, Validation logic change)
class User {
    void saveToDatabase() { /* DB logic */ }
    void sendEmail() { /* Email logic */ }
    void validateUser() { /* Validation */ }
}

// ✅ SOLUTION - Each class has one reason to change
class UserRepository {
    void save(User user) { /* Only changes if DB logic changes */ }
}

class EmailService {
    void sendEmail(User user) { /* Only changes if email service changes */ }
}

class UserValidator {
    boolean validate(User user) { /* Only changes if validation rules change */ }
}
```

**Benefit**: 
- Easy to maintain - changes in one area don't affect others
- Easy to test - mock each responsibility independently
- Reusable - EmailService can be used elsewhere

---

## O - Open/Closed Principle

**Open for extension (add new features), closed for modification (don't change existing code)**

```java
// ❌ VIOLATION - Must modify PaymentProcessor to add new payment method
class PaymentProcessor {
    void process(String method, double amount) {
        if ("credit".equals(method)) { /* Credit logic */ }
        else if ("upi".equals(method)) { /* UPI logic */ }
        // Need to edit this class every time new payment method added!
    }
}

// ✅ SOLUTION - Extend with new class, don't modify existing code
interface PaymentMethod {
    void process(double amount);
}

class CreditCard implements PaymentMethod {
    public void process(double amount) { /* Credit logic */ }
}

class UPI implements PaymentMethod {
    public void process(double amount) { /* UPI logic */ }
}

class Bitcoin implements PaymentMethod {  // New payment method - just add new class!
    public void process(double amount) { /* Bitcoin logic */ }
}

class PaymentGateway {
    void pay(PaymentMethod method, double amount) {
        method.process(amount);  // Works with any PaymentMethod
    }
}
```

**Benefit**: Add new payment methods without touching PaymentGateway code - reduces bug risk

---

## L - Liskov Substitution Principle

**Derived classes must be substitutable for their base class - don't break parent contract**

```java
// ❌ VIOLATION - Penguin violates Bird's contract (can't fly)
class Bird {
    void fly() { System.out.println("Flying"); }
}

class Penguin extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException();  // Violates contract!
    }
}

// Usage breaks:
Bird bird = new Penguin();
bird.fly();  // Crashes! This should work for any Bird

// ✅ SOLUTION - Separate concerns with interfaces
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
    // Not Flyable - no fly() method
}

// Now safe to use:
Animal animal = new Penguin();
animal.move();  // Works - doesn't break contract
```

**Benefit**: Predictable inheritance - derived class behavior is consistent with base class

---

## I - Interface Segregation Principle

**Don't force clients to implement interfaces they don't use - keep interfaces focused**

```java
// ❌ VIOLATION - Robot forced to implement eat() and sleep()
interface Worker {
    void work();
    void eat();
    void sleep();
}

class Robot implements Worker {
    public void work() { System.out.println("Working"); }
    public void eat() { /* Meaningless for robot */ }
    public void sleep() { /* Meaningless for robot */ }
}

// ❌ Problem: Robot has unused methods, code is confusing

// ✅ SOLUTION - Split into smaller, focused interfaces
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
    public void work() { System.out.println("Working"); }
    public void eat() { System.out.println("Eating"); }
    public void sleep() { System.out.println("Sleeping"); }
}

class Robot implements Workable {
    public void work() { System.out.println("Working"); }
    // No forced eat() or sleep() - clean!
}
```

**Benefit**: 
- No dummy implementations
- Clearer interfaces - name tells exactly what class can do
- Easier testing - mock only what's needed

---

## L vs I: Key Differences with Real-World Examples

**LSP (L) and ISP (I) are DIFFERENT problems - often confused:**

### **LSP: Inheritance Hierarchy Problem**
- **Issue**: Child class breaks parent's contract
- **Focus**: Behavior consistency - derived class must behave like parent
- **Question**: "Can I safely use child wherever parent is expected?"

### **ISP: Interface Design Problem**
- **Issue**: Interface has methods client doesn't need
- **Focus**: Interface should be focused - don't force unused methods
- **Question**: "Does client need ALL methods in this interface?"

---

### Real-World Example: Restaurant System

#### **LSP Violation: Flying Chef**
```java
// ❌ LSP VIOLATION - Child breaks parent contract
abstract class Chef {
    abstract void cook();
    abstract void fly();  // All chefs can fly?
}

class NormalChef extends Chef {
    public void cook() { System.out.println("Cooking"); }
    
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Can't fly!");  // BREAKS CONTRACT
    }
}

class SuperChef extends Chef {
    public void cook() { System.out.println("Cooking well"); }
    
    @Override
    public void fly() { System.out.println("Flying"); }  // Actually can fly
}

// Usage breaks:
Chef chef = new NormalChef();
chef.fly();  // Crashes! Violates Liskov Substitution
```

#### **ISP Violation: Too Many Methods**
```java
// ❌ ISP VIOLATION - Interface too fat, methods not related
interface Employee {
    void work();
    void manageBudget();      // Not all employees manage budget
    void conductMeeting();    // Not all employees conduct meetings
    void fry();               // Why fry in general Employee?
}

class JuniorDeveloper implements Employee {
    public void work() { System.out.println("Coding"); }
    
    public void manageBudget() { /* Meaningless */ }
    
    public void conductMeeting() { /* Meaningless */ }
    
    public void fry() { /* Meaningless */ }
}

// Problem: Junior dev must implement 4 methods, only needs work()
```

#### **✅ CORRECT: LSP + ISP Solution**
```java
// LSP: Use proper inheritance - no contract violation
interface Cooker {
    void cook();
}

interface Manager {
    void manageBudget();
}

interface Flyer {
    void fly();
}

// ISP: Each class implements only what it needs
class NormalChef implements Cooker {
    public void cook() { System.out.println("Cooking"); }
    // No fly() - doesn't implement Flyer
}

class SuperChef implements Cooker, Flyer {
    public void cook() { System.out.println("Cooking well"); }
    public void fly() { System.out.println("Flying"); }
}

class Manager implements Cooker, Manager {
    public void cook() { System.out.println("Cooking"); }
    public void manageBudget() { System.out.println("Managing budget"); }
}

class JuniorDeveloper implements Cooker {
    public void cook() { System.out.println("Coding"); }  // Only implements what needed
}
```

#### **Side-by-side Comparison**

| Aspect | LSP (L) | ISP (I) |
|--------|---------|--------|
| **Problem Type** | Inheritance hierarchy | Interface design |
| **What's Wrong** | Child breaks parent contract | Interface has unused methods |
| **Example** | Penguin.fly() throws exception | Robot.eat() is meaningless |
| **Solution** | Use composition/multiple interfaces | Split into smaller interfaces |
| **Impact** | Runtime errors when using wrong child | Ugly code with dummy implementations |
| **Question** | "Is child substitutable for parent?" | "Does class need ALL methods?" |
| **When Violated** | Throws exception or incorrect behavior | Empty implementations or dummy code |

---

### Real-World: Payment System

```java
// ❌ LSP VIOLATION
abstract class Payment {
    abstract void process();
    abstract void refund();  // Not all payments can be refunded
}

class CryptoPayment extends Payment {
    public void process() { System.out.println("Processing crypto"); }
    
    @Override
    public void refund() {
        throw new UnsupportedOperationException("Crypto irreversible!");  // BREAKS CONTRACT
    }
}

// ✅ ISP VIOLATION
interface PaymentMethod {
    void process();
    void refund();
    void validate();
    void encrypt();
    void log();           // All need logging?
    void notifyBank();    // All need bank notification?
}

class CreditCard implements PaymentMethod {
    // Must implement ALL 6 methods, even if don't need some
}

// ✅ CORRECT SOLUTION
interface Processable {
    void process();
}

interface Refundable {
    void refund();
}

class CryptoPayment implements Processable {
    public void process() { System.out.println("Processing"); }
    // No refund() - not Refundable
}

class CreditCard implements Processable, Refundable {
    public void process() { System.out.println("Processing"); }
    public void refund() { System.out.println("Refunding"); }
}
```

---

## D - Dependency Inversion Principle

**Depend on abstractions, not concrete implementations - makes code flexible and testable**

```java
// ❌ VIOLATION - Tight coupling to MySQLDatabase
class MySQLDatabase {
    void save(String data) { /* MySQL logic */ }
}

class UserService {
    private MySQLDatabase db = new MySQLDatabase();  // Hard dependency!
    
    void saveUser(String data) {
        db.save(data);  // Can only work with MySQL
    }
}
// Problem: If want MongoDB, must rewrite UserService

// ✅ SOLUTION - Depend on Database interface, not concrete class
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
    
    UserService(Database db) {  // Inject dependency
        this.db = db;
    }
    
    void saveUser(String data) {
        db.save(data);  // Works with any Database implementation
    }
}

// Usage - Easy to swap implementations
Database mysql = new MySQLDatabase();
UserService service1 = new UserService(mysql);

Database mongo = new MongoDatabase();
UserService service2 = new UserService(mongo);  // Same UserService, different DB!

// Testing
class MockDatabase implements Database {
    public void save(String data) { /* Test implementation */ }
}
UserService testService = new UserService(new MockDatabase());  // Easy to test!
```

**Benefit**: 
- Loose coupling - can swap Database implementation anytime
- Easy testing - inject mock Database for unit tests
- Flexible - code works with any Database implementation

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
