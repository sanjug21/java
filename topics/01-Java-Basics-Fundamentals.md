# Java Basics & Fundamentals

## Introduction
Java is a **platform-independent, object-oriented** language following **"Write Once, Run Anywhere" (WORA)**. Code compiles to bytecode that runs on any JVM.

**Key Features**: Platform Independent, Object-Oriented, Robust, Secure, Multi-threaded, Garbage Collected

---

## Hello World Program

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

- `public class HelloWorld`: Class declaration (filename must match)
- `public static void main(String[] args)`: Entry point
- `System.out.println()`: Print to console

---

## Data Types

### Primitive Types

| Type | Size | Range | Example |
|------|------|-------|---------|
| `byte` | 1 byte | -128 to 127 | `byte b = 100;` |
| `short` | 2 bytes | -32,768 to 32,767 | `short s = 1000;` |
| `int` | 4 bytes | -2³¹ to 2³¹-1 | `int i = 50000;` |
| `long` | 8 bytes | -2⁶³ to 2⁶³-1 | `long l = 100000L;` |
| `float` | 4 bytes | 6-7 digits | `float f = 3.14f;` |
| `double` | 8 bytes | 15 digits | `double d = 3.14159;` |
| `char` | 2 bytes | Unicode char | `char c = 'A';` |
| `boolean` | 1 bit | true/false | `boolean b = true;` |

### Reference Types
- **String**, **Arrays**, **Classes**, **Interfaces**

```java
String name = "Java";
int[] numbers = {1, 2, 3, 4, 5};
```

---

## Control Flow

### If-Else
```java
if (marks >= 90) {
    System.out.println("Grade A");
} else if (marks >= 80) {
    System.out.println("Grade B");
} else {
    System.out.println("Grade C");
}
```
**When to use**: When you need to check **ranges** or **complex conditions** (comparisons with <, >, <=, >=, logical operators)

### Switch
```java
switch (day) {
    case 1: System.out.println("Monday"); break;
    case 2: System.out.println("Tuesday"); break;
    default: System.out.println("Invalid");
}
```
**When to use**: When you have **exact value matching** (e.g., menu options, days, status codes) - cleaner than multiple if-else

### Ternary Operator
```java
String status = (age >= 18) ? "Adult" : "Minor";
```

---

## Loops

### For Loop
```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```
**When to use**: When you know the **exact number of iterations** beforehand (predefined range/count)

### Enhanced For
```java
int[] arr = {10, 20, 30};
for (int num : arr) {
    System.out.println(num);
}
```
**When to use**: When iterating through **all elements in a collection/array** (cleaner, but can't modify index)

### While Loop
```java
int i = 0;
while (i < 5) {
    System.out.println(i++);
}
```
**When to use**: When the **number of iterations is unknown** (depends on a condition), like reading user input or waiting for an event

### Do-While
```java
int i = 0;
do {
    System.out.println(i++);
} while (i < 5);
```
**When to use**: When the **loop body must execute at least once** (e.g., menu systems, input validation)

### Loop Control
- **break**: Exit loop
- **continue**: Skip current iteration

---

## Methods

```java
// Method with return value
static int add(int a, int b) {
    return a + b;
}

// Void method
static void greet(String name) {
    System.out.println("Hello, " + name);
}

// Method overloading
static int add(int a, int b, int c) {
    return a + b + c;
}

// Recursion
static int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}
```

---

## Arrays

```java
// Declaration
int[] numbers = {1, 2, 3, 4, 5};
String[] names = new String[5];

// Access
int first = numbers[0];
int length = numbers.length;

// Multi-dimensional
int[][] matrix = {{1,2}, {3,4}};
```

---

## Key Concepts
- **Variables**: Store data (primitive vs reference types)
- **Control Flow**: if-else, switch, ternary
- **Loops**: for, while, do-while, enhanced for
- **Methods**: Reusable code blocks, overloading, recursion
- **Arrays**: Fixed-size collections, zero-indexed

---

## Next: [JVM, JRE, JDK Architecture](02-JVM-JRE-JDK-Architecture.md)
