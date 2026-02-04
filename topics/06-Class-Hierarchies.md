# 6. CLASS HIERARCHIES & OBJECT CLASS

## Object Class - Root of All Classes

Every class in Java implicitly extends `Object` class.

**Key Methods:**

| Method | Description | Default Behavior |
|--------|-------------|------------------|
| `toString()` | String representation | Returns `ClassName@hashCode` |
| `equals(Object)` | Equality check | Checks reference equality (`==`) |
| `hashCode()` | Hash value | Returns memory address |
| `clone()` | Creates copy | Shallow copy (must implement Cloneable) |
| `getClass()` | Returns Class object | Runtime type information |
| `finalize()` | Called before GC | Cleanup (deprecated in Java 9+) |

---

## Overriding equals() and hashCode()

```java
public class Person {
    private String name;
    private int age;
    
    // Constructor
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Override equals()
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Person person = (Person) obj;
        return age == person.age && name.equals(person.name);
    }
    
    // Override hashCode()
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
    
    // Override toString()
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + '}';
    }
}
```

**Contract Rules:**
- If `a.equals(b)` is true, then `a.hashCode() == b.hashCode()` must be true
- If `a.hashCode() == b.hashCode()`, `a.equals(b)` may or may not be true
- Always override both together when using objects in HashMap/HashSet

---

## instanceof Operator

```java
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

public class InstanceOfExample {
    public static void main(String[] args) {
        Animal animal = new Dog();
        
        System.out.println(animal instanceof Dog);     // true
        System.out.println(animal instanceof Animal);  // true
        System.out.println(animal instanceof Cat);     // false
        
        // Pattern matching (Java 16+)
        if (animal instanceof Dog d) {
            System.out.println("It's a dog: " + d);
        }
    }
}
```

---

## Shallow vs Deep Copy

```java
class Address {
    String city;
    Address(String city) { this.city = city; }
}

class Employee implements Cloneable {
    String name;
    Address address;
    
    Employee(String name, Address address) {
        this.name = name;
        this.address = address;
    }
    
    // Shallow copy
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();  // Only copies references
    }
    
    // Deep copy
    public Employee deepCopy() {
        return new Employee(this.name, new Address(this.address.city));
    }
}
```

---

## Key Takeaways

✓ All classes inherit from Object
✓ Override `equals()` and `hashCode()` together
✓ Use `instanceof` for type checking
✓ Understand shallow vs deep copy

**Next:** [07. Autoboxing & Wrapper Classes](07-Autoboxing-Wrapper-Classes.md)
