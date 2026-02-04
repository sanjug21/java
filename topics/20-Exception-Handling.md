# 20. EXCEPTION HANDLING

## Exception Hierarchy

```
Throwable
├── Error (Unchecked - JVM errors)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
└── Exception
    ├── RuntimeException (Unchecked)
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── ArithmeticException
    │   └── IllegalArgumentException
    └── Checked Exceptions
        ├── IOException
        ├── SQLException
        ├── ClassNotFoundException
        └── FileNotFoundException
```

---

## try-catch-finally

```java
try {
    int result = 10 / 0;  // ArithmeticException
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
} finally {
    System.out.println("Always executed");
}
```

---

## Multiple catch Blocks

```java
try {
    int[] arr = {1, 2, 3};
    System.out.println(arr[5]);
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Index out of bounds");
} catch (Exception e) {
    System.out.println("General exception");
}
```

**Order:** Specific exceptions first, general exceptions last

---

## Multi-catch (Java 7+)

```java
try {
    // Code
} catch (IOException | SQLException e) {
    System.out.println("IO or SQL exception: " + e.getMessage());
}
```

---

## try-with-resources (Java 7+)

**Auto-closes resources implementing AutoCloseable**

```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
    System.out.println(line);
} catch (IOException e) {
    e.printStackTrace();
}
// br.close() called automatically
```

**Multiple resources:**

```java
try (FileReader fr = new FileReader("input.txt");
     BufferedReader br = new BufferedReader(fr)) {
    // Use resources
}
```

---

## throw vs throws

### throw

**Explicitly throws an exception**

```java
void validate(int age) {
    if (age < 18) {
        throw new IllegalArgumentException("Age must be >= 18");
    }
}
```

### throws

**Declares exceptions that method might throw**

```java
void readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);
    // ...
}
```

---

## Checked vs Unchecked Exceptions

| Feature | Checked | Unchecked |
|---------|---------|-----------|
| Compile-time check | Yes | No |
| Must handle | Yes (try-catch or throws) | No |
| Examples | IOException, SQLException | NullPointerException, ArithmeticException |
| Parent class | Exception | RuntimeException |
| Use case | Recoverable errors | Programming errors |

---

## Custom Exceptions

```java
class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}

class BankAccount {
    private double balance;
    
    void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException("Balance: " + balance);
        }
        balance -= amount;
    }
}

// Usage
try {
    account.withdraw(1000);
} catch (InsufficientFundsException e) {
    System.out.println(e.getMessage());
}
```

---

## Exception Chaining

```java
try {
    // Original exception
    throw new IOException("File error");
} catch (IOException e) {
    // Chain with new exception
    throw new RuntimeException("Wrapper exception", e);
}

// Access original exception
catch (RuntimeException e) {
    Throwable cause = e.getCause();  // Returns IOException
}
```

---

## Best Practices

### ✓ DO

```java
// Specific exceptions
try {
    // Code
} catch (FileNotFoundException e) {
    // Handle file not found
} catch (IOException e) {
    // Handle other IO exceptions
}

// Log exceptions
catch (Exception e) {
    logger.error("Error occurred", e);
}

// Use try-with-resources
try (Connection conn = getConnection()) {
    // Use connection
}
```

### ✗ DON'T

```java
// Empty catch block
try {
    // Code
} catch (Exception e) {
    // Bad: swallowing exception
}

// Catching Throwable/Error
catch (Throwable t) {
    // Bad: catches even JVM errors
}

// Generic Exception as first catch
catch (Exception e) {
    // Bad: too broad
}
```

---

## Common Exceptions

### NullPointerException

```java
String str = null;
// str.length();  // NPE

// Prevention
if (str != null) {
    str.length();
}

// Java 8+
Optional.ofNullable(str).map(String::length).orElse(0);
```

### ArrayIndexOutOfBoundsException

```java
int[] arr = {1, 2, 3};
// arr[5] = 10;  // AIOOBE

// Prevention
if (index >= 0 && index < arr.length) {
    arr[index] = 10;
}
```

### NumberFormatException

```java
// String num = "abc";
// Integer.parseInt(num);  // NFE

// Prevention
try {
    Integer.parseInt(num);
} catch (NumberFormatException e) {
    // Handle invalid number
}
```

---

## Suppressed Exceptions (try-with-resources)

```java
class Resource implements AutoCloseable {
    public void close() throws Exception {
        throw new Exception("Close exception");
    }
}

try (Resource r = new Resource()) {
    throw new Exception("Try block exception");
} catch (Exception e) {
    System.out.println(e.getMessage());  // Try block exception
    
    Throwable[] suppressed = e.getSuppressed();
    System.out.println(suppressed[0].getMessage());  // Close exception
}
```

---

## Key Takeaways

✓ **Checked exceptions:** Must handle or declare
✓ **Unchecked exceptions:** Programming errors
✓ **try-with-resources:** Auto-close resources
✓ **Custom exceptions:** Extend Exception or RuntimeException
✓ **Best practice:** Catch specific exceptions, don't swallow
✓ Use `finally` for cleanup or try-with-resources

**Next:** [21. I/O Programming](21-IO-Programming.md)
