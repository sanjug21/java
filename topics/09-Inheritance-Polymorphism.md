# 9. INHERITANCE & POLYMORPHISM (Deep Dive)

## Constructor Chaining

```java
class Parent {
    Parent() {
        System.out.println("Parent constructor");
    }
    
    Parent(String msg) {
        System.out.println("Parent: " + msg);
    }
}

class Child extends Parent {
    Child() {
        super("Hello");  // Calls Parent(String)
        System.out.println("Child constructor");
    }
}

// Output:
// Parent: Hello
// Child constructor
```

**Rules:**
- `super()` must be first statement in constructor
- If not specified, `super()` is called implicitly
- Constructor execution: Parent → Child

---

## Method Overriding Rules

```java
class Animal {
    void sound() { System.out.println("Animal sound"); }
    final void breathe() { System.out.println("Breathing"); }
}

class Dog extends Animal {
    @Override
    void sound() {  // Valid override
        System.out.println("Bark");
    }
    
    // void breathe() { }  // ERROR: Cannot override final method
}
```

**Requirements:**
- Same method signature
- Return type: same or covariant (subtype)
- Access modifier: same or more accessible
- Cannot override: `static`, `final`, `private` methods

---

## Covariant Return Type

```java
class Animal {
    Animal reproduce() {
        return new Animal();
    }
}

class Dog extends Animal {
    @Override
    Dog reproduce() {  // Covariant return type
        return new Dog();
    }
}
```

---

## super Keyword

```java
class Parent {
    int x = 10;
    void display() { System.out.println("Parent"); }
}

class Child extends Parent {
    int x = 20;
    
    void display() {
        System.out.println("Child x: " + x);         // 20
        System.out.println("Parent x: " + super.x); // 10
        super.display();  // Calls Parent's display()
    }
}
```

**Uses:**
- Call parent constructor: `super()`
- Access parent field: `super.field`
- Call parent method: `super.method()`

---

## Dynamic Method Dispatch

**Runtime polymorphism - method call resolved at runtime**

```java
class Animal {
    void sound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    void sound() { System.out.println("Bark"); }
}

class Cat extends Animal {
    void sound() { System.out.println("Meow"); }
}

public class DynamicDispatchExample {
    public static void main(String[] args) {
        Animal animal;  // Reference type: Animal
        
        animal = new Dog();  // Object type: Dog
        animal.sound();      // Bark (Dog's method called)
        
        animal = new Cat();  // Object type: Cat
        animal.sound();      // Meow (Cat's method called)
    }
}
```

**Key:** Method called depends on **object type**, not reference type

---

## Upcasting & Downcasting

```java
class Animal {
    void eat() { System.out.println("Eating"); }
}

class Dog extends Animal {
    void bark() { System.out.println("Barking"); }
}

public class CastingExample {
    public static void main(String[] args) {
        // Upcasting (implicit)
        Animal animal = new Dog();  // Dog → Animal
        animal.eat();  // OK
        // animal.bark();  // ERROR: Animal doesn't have bark()
        
        // Downcasting (explicit)
        if (animal instanceof Dog) {
            Dog dog = (Dog) animal;  // Animal → Dog
            dog.bark();  // OK now
        }
    }
}
```

---

## Method Overloading vs Overriding

| Feature | Overloading | Overriding |
|---------|-------------|------------|
| Definition | Same name, different parameters | Same signature in child class |
| Polymorphism | Compile-time | Runtime |
| Class | Same class | Parent-child |
| Return Type | Can differ | Same or covariant |
| Access Modifier | Any | Same or more accessible |
| static/final | Allowed | Not allowed |

---

## final Keyword

```java
final class FinalClass { }  // Cannot be extended
// class Child extends FinalClass { }  // ERROR

class Parent {
    final void display() { }  // Cannot be overridden
}

class Child extends Parent {
    // void display() { }  // ERROR
}

class Example {
    final int x = 10;  // Cannot be reassigned
    // x = 20;  // ERROR
}
```

---

## Key Takeaways

✓ Constructor chaining: `super()` calls parent constructor
✓ Override rules: same signature, same/broader access
✓ Dynamic dispatch: method call resolved at runtime
✓ Upcasting is safe, downcasting requires check
✓ Use `final` to prevent inheritance/overriding

**Next:** [10. Inner Classes](10-Inner-Classes.md)
