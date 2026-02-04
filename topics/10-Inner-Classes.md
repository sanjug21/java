# 10. INNER CLASSES

## Types of Inner Classes

### 1. Member Inner Class

**Non-static nested class**

```java
class Outer {
    private int x = 10;
    
    class Inner {  // Member inner class
        void display() {
            System.out.println("x = " + x);  // Can access outer's private members
        }
    }
}

public class MemberInnerExample {
    public static void main(String[] args) {
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();
        inner.display();  // x = 10
    }
}
```

**Features:**
- Access to all members of outer class (including private)
- Cannot have static members (except `static final`)
- Instantiation requires outer object

---

### 2. Static Nested Class

```java
class Outer {
    private static int x = 10;
    private int y = 20;
    
    static class StaticNested {
        void display() {
            System.out.println("x = " + x);  // OK - static member
            // System.out.println("y = " + y);  // ERROR - non-static member
        }
    }
}

public class StaticNestedExample {
    public static void main(String[] args) {
        Outer.StaticNested nested = new Outer.StaticNested();
        nested.display();  // x = 10
    }
}
```

**Features:**
- Can only access static members of outer class
- Can have static members
- No outer object needed for instantiation

---

### 3. Local Inner Class

**Class defined inside a method**

```java
class Outer {
    void display() {
        final int x = 10;  // Effectively final
        
        class LocalInner {  // Local inner class
            void print() {
                System.out.println("x = " + x);
            }
        }
        
        LocalInner inner = new LocalInner();
        inner.print();
    }
}

public class LocalInnerExample {
    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.display();  // x = 10
    }
}
```

**Features:**
- Visible only within the method
- Can access final/effectively final local variables
- Cannot have access modifiers

---

### 4. Anonymous Inner Class

**Class without a name, defined and instantiated together**

```java
interface Greeting {
    void greet();
}

public class AnonymousInnerExample {
    public static void main(String[] args) {
        // Anonymous inner class implementing interface
        Greeting greeting = new Greeting() {
            @Override
            public void greet() {
                System.out.println("Hello from anonymous class!");
            }
        };
        greeting.greet();
        
        // Anonymous inner class extending class
        Thread thread = new Thread() {
            @Override
            public void run() {
                System.out.println("Thread running");
            }
        };
        thread.start();
    }
}
```

**Use Cases:**
- One-time use implementations
- Event listeners (GUI programming)
- Comparators, Runnables

---

## Comparison Table

| Type | Outer Access | Static Members | Instantiation | Use Case |
|------|--------------|----------------|---------------|----------|
| Member Inner | All members | No (except final) | Needs outer object | Logical grouping |
| Static Nested | Static only | Yes | No outer needed | Helper classes |
| Local Inner | Final locals + outer | No | Inside method only | Method-specific logic |
| Anonymous | All (in scope) | No | Inline | One-time implementation |

---

## Practical Example - Iterator Pattern

```java
class MyList {
    private String[] items = {"A", "B", "C"};
    
    class Iterator {  // Member inner class
        private int index = 0;
        
        boolean hasNext() {
            return index < items.length;
        }
        
        String next() {
            return items[index++];
        }
    }
    
    Iterator getIterator() {
        return new Iterator();
    }
}

public class IteratorExample {
    public static void main(String[] args) {
        MyList list = new MyList();
        MyList.Iterator iterator = list.getIterator();
        
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

---

## Lambda vs Anonymous Inner Class

```java
// Anonymous inner class
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};

// Lambda (Java 8+) - cleaner syntax
Runnable r2 = () -> System.out.println("Running");
```

**Lambda advantages:** Concise, functional, easier to read

---

## Key Takeaways

✓ **Member Inner:** Access all outer members, needs outer object
✓ **Static Nested:** Access static members only, no outer object needed
✓ **Local Inner:** Method-scoped, access final/effectively final locals
✓ **Anonymous:** One-time use, inline implementation
✓ Use lambdas instead of anonymous classes for functional interfaces

**Next:** [11. Dynamic Binding](11-Dynamic-Binding.md)
