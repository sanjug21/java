# 21. EXCEPTION HANDLING

## Exception Hierarchy

**Understanding exception types: Errors are JVM-level problems, Exceptions are application-level problems**

Java's exception hierarchy enables different handling strategies based on exception severity and recoverability:

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

**The fundamental exception handling mechanism: try executes code, catch handles specific exceptions, finally ensures cleanup**

```java
try {
    int result = 10 / 0;  // ArithmeticException thrown here
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");  // Executes when exception caught
} finally {
    System.out.println("Always executed");  // Runs regardless of try/catch outcome
}
```

**Key behaviors:**
- `try`: Executes until exception thrown or completion
- `catch`: Only executes if matching exception thrown; can have multiple catch blocks
- `finally`: Always executes (except JVM termination) - use for resource cleanup, logging, state restoration
- Return in try/catch: finally still executes before method returns

---

## Multiple catch Blocks

**Handle different exception types with specific recovery strategies; more specific exceptions first**

```java
try {
    int[] arr = {1, 2, 3};
    System.out.println(arr[5]);  // Throws ArrayIndexOutOfBoundsException
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Index out of bounds");  // Specific handling for this error
} catch (NullPointerException e) {
    System.out.println("Null pointer occurred");  // Specific handling for NPE
} catch (Exception e) {
    System.out.println("General exception");  // Fallback for any other exception
}
```

**Critical ordering rule:**
- Specific (narrower) exceptions MUST come before general (broader) exceptions
- Exception matching stops at first catch block that matches
- If `Exception` catch placed first, child class catches never execute (unreachable code)
- Order matters because inheritance hierarchy determines matching

---

## Multi-catch (Java 7+)

**Handle multiple unrelated exceptions with identical logic using pipe separator**

```java
try {
    // Code that might throw IOException or SQLException
} catch (IOException | SQLException e) {
    System.out.println("IO or SQL exception: " + e.getMessage());
    logger.error("Database/File error", e);
}
```

**Benefits:**
- Reduces code duplication when multiple exceptions need same handling
- `e` is implicitly final - cannot be reassigned
- Exception variable `e` has type of common superclass (IOException | SQLException = Exception)
- Alternative to chaining try-catch blocks
- Cannot use for exceptions with inheritance relationship (IOException | FileNotFoundException not allowed since FileNotFoundException extends IOException)

---

## try-with-resources (Java 7+)

**Automatically close resources implementing AutoCloseable; cleaner than try-finally pattern**

```java
// Traditional approach with manual closing
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("file.txt"));
    String line = br.readLine();
    System.out.println(line);
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (br != null) br.close();  // Manual cleanup required
}

// Modern approach with try-with-resources
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
    System.out.println(line);
} catch (IOException e) {
    e.printStackTrace();
}  // br.close() called automatically
```

**Multiple resources (closed in reverse order):**

```java
try (FileReader fr = new FileReader("input.txt");
     BufferedReader br = new BufferedReader(fr);  // Closed second
     FileWriter fw = new FileWriter("output.txt")) {  // Closed third
    // Use resources - automatically closed after this block
}
// Close order: fw → br → fr
```

**Key characteristics:**
- Resource variables are implicitly final
- All resources closed even if exception thrown
- Suppressed exceptions from close() attached to primary exception (accessible via getSuppressed())
- Only works with classes implementing AutoCloseable interface
- Eliminates try-finally boilerplate code

---

## throw vs throws

### throw

**Explicitly throw an exception instance; transfers control to nearest matching catch block**

```java
void validate(int age) {
    if (age < 18) {
        throw new IllegalArgumentException("Age must be >= 18");  // Immediately stops execution
    }
    // Code after throw never executes
}

// Usage
try {
    validate(15);
} catch (IllegalArgumentException e) {
    System.out.println(e.getMessage());  // "Age must be >= 18"
}
```

### throws

**Declares that method might throw exceptions; caller must handle or propagate**

```java
// Method declares it throws checked exception
void readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);  // IOException not caught here
    // Caller must handle IOException
}

// Caller must handle or re-declare
try {
    readFile("file.txt");
} catch (IOException e) {
    // Handle or:
}

// Or propagate
void processFile(String path) throws IOException {
    readFile(path);  // IOException propagates up
}
```

**Key differences:**
- `throw`: Creates exception instance and transfers control (statement)
- `throws`: Declares that method might throw exception (declaration)
- `throw` forces immediate exception handling or propagation
- `throws` allows method to avoid handling checked exceptions by declaring them
- Only checked exceptions must be declared with `throws`
- `throws` enables throwing multiple exception types separated by commas

---

## Checked vs Unchecked Exceptions

**Checked exceptions force compile-time handling; unchecked exceptions represent logic errors**

| Feature | Checked | Unchecked |
|---------|---------|----------|
| Compile-time check | Yes - compiler enforces handling | No - runtime only |
| Must handle | Yes (try-catch or throws) | No - optional |
| Examples | IOException, SQLException, FileNotFoundException | NullPointerException, ArithmeticException, IndexOutOfBoundsException |
| Parent class | Exception (but NOT RuntimeException) | RuntimeException |
| Inheritance from | Exception directly | RuntimeException → Exception |
| Use case | Expected, recoverable errors (file missing, network down) | Programming errors (null check, bounds check) |
| When thrown | By API methods you call (IO, DB, reflection) | By JVM or bad logic |
| Best practice | Always handle or declare | Fix code to prevent rather than catch |

**Decision table:**
```
If API method declares throws IOException
  → You MUST catch or declare throws
  → Compiler won't let you ignore it

If code throws NullPointerException
  → You CAN catch but shouldn't
  → Better to: if (obj != null) before using
  → Fix root cause rather than catch
```

**Design principle:**
- Use checked exceptions for conditions caller can reasonably recover from
- Use unchecked for logic errors that indicate code bugs

---

## Custom Exceptions

**Create domain-specific exceptions for application logic; communicate intent to callers**

```java
// Checked exception for recoverable business logic error
class InsufficientFundsException extends Exception {
    private double balance;
    private double requested;
    
    public InsufficientFundsException(String message, double balance, double requested) {
        super(message);
        this.balance = balance;
        this.requested = requested;
    }
    
    // Provide domain-specific accessors
    public double getBalance() { return balance; }
    public double getRequested() { return requested; }
}

class BankAccount {
    private double balance;
    
    void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(
                "Cannot withdraw: insufficient funds", 
                balance, 
                amount
            );  // Caller can recover by withdrawing less
        }
        balance -= amount;
    }
}

// Usage
try {
    account.withdraw(1000);
} catch (InsufficientFundsException e) {
    System.out.println("Error: " + e.getMessage());
    System.out.println("Available: " + e.getBalance());
    System.out.println("Requested: " + e.getRequested());
    // Caller can retry with different amount
}
```

**When to extend Exception vs RuntimeException:**
- Extend `Exception` (checked): Business rules that caller should handle (insufficient funds, invalid operation)
- Extend `RuntimeException` (unchecked): Programming errors like invalid input validation

**Best practices for custom exceptions:**
- Provide multiple constructors for different information levels
- Include context-specific accessors (getBalance(), getRequested())
- Use meaningful names that describe the problem
- Document when your code throws them

---

## Exception Chaining

**Preserve original exception while wrapping with higher-level exception; maintains complete error context**

```java
public void processFile(String path) throws FileProcessingException {
    try {
        FileReader fr = new FileReader(path);
        // Read and process file
    } catch (IOException e) {
        // Chain low-level IOException as cause of high-level exception
        throw new FileProcessingException(
            "Failed to process file: " + path, 
            e  // Original exception becomes cause
        );
    }
}

// Caller sees business exception but can access root cause
try {
    processFile("data.txt");
} catch (FileProcessingException e) {
    System.out.println("High-level error: " + e.getMessage());
    
    // Access original exception for debugging
    Throwable cause = e.getCause();  // Returns IOException
    System.out.println("Root cause: " + cause.getClass().getName());
    System.out.println("Root message: " + cause.getMessage());
}
```

**Why exception chaining matters:**
- Preserves stack trace from original exception
- Encapsulates low-level implementation details
- Allows callers to handle business exceptions while accessing root cause
- Full call chain visible: IOException → FileProcessingException → application recovery
- Printed stack trace shows both exceptions with `Caused by:` link

**Stack trace example:**
```
FileProcessingException: Failed to process file: data.txt
    at FileProcessor.processFile(FileProcessor.java:15)
    at Main.main(Main.java:10)
Caused by: FileNotFoundException: data.txt (No such file or directory)
    at FileReader.<init>(FileReader.java:101)
    at FileProcessor.processFile(FileProcessor.java:8)
```

---

## Best Practices

### ✓ DO

```java
// 1. Catch specific exceptions for targeted recovery
try {
    BufferedReader br = new BufferedReader(new FileReader("file.txt"));
    String line = br.readLine();
} catch (FileNotFoundException e) {
    // Specific handling: file doesn't exist, create new one
    createDefaultFile();
} catch (IOException e) {
    // Specific handling: IO problem, log and retry
    logger.warn("IO error, retrying", e);
    retry();
}

// 2. Use try-with-resources for automatic cleanup
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    String line = br.readLine();
} catch (IOException e) {
    logger.error("Failed to read file", e);  // File automatically closed
}

// 3. Always log exceptions with full context
catch (Exception e) {
    logger.error("Error occurred during processing: " + context, e);  // Include stack trace
}

// 4. Preserve exception chain
catch (IOException e) {
    throw new DataProcessingException("Cannot process data", e);  // Pass cause
}

// 5. Provide meaningful error messages
catch (IllegalArgumentException e) {
    System.err.println("Invalid input: " + e.getMessage());  // Includes context
}
```

### ✗ DON'T

```java
// 1. Empty catch blocks (swallowing exceptions)
try {
    int result = Integer.parseInt(input);  // NumberFormatException
} catch (NumberFormatException e) {
    // WRONG: Exception disappears, caller unaware of problem
}
// Result: Silent failures, hard to debug

// 2. Catching too broad - Throwable or Error
try {
    // Code
} catch (Throwable t) {
    // WRONG: Catches OutOfMemoryError, StackOverflowError, etc.
    // These are JVM-fatal errors you cannot recover from
}

// 3. Generic Exception before specific exceptions
try {
    // Code
} catch (Exception e) {
    // WRONG: Placed before specific catches, those become unreachable
    System.out.println("Error");
} catch (FileNotFoundException e) {  // Never executes
    // WRONG: FileNotFoundException already caught by Exception above
}

// 4. Ignoring exception in finally
finally {
    try {
        resource.close();  // Exception here is silent
    } catch (Exception e) {
        // WRONG: Exception swallowed, original exception lost
    }
}
// Use try-with-resources instead

// 5. Catching unchecked exceptions to hide logic errors
try {
    String str = null;
    int len = str.length();  // NullPointerException
} catch (NullPointerException e) {
    // WRONG: Means 'str' should have been null-checked
    // Fix: if (str != null) { len = str.length(); }
}
```

**Anti-patterns summary:**
| Don't | Why | Do Instead |
|------|-----|-----------|
| Catch Exception globally | Hides specific errors | Catch specific exceptions |
| Empty catch blocks | Loses error information | Log or handle appropriately |
| Catch Throwable/Error | Unrecoverable JVM errors | Let JVM handle these |
| Catch unchecked exceptions | Indicates code bug | Fix code logic instead |
| Ignore exceptions in finally | Masks original error | Use try-with-resources |

---

## Common Exceptions

### NullPointerException (Unchecked)

**Most common runtime exception - occurs when dereferencing null reference**

```java
String str = null;
str.length();  // NullPointerException: cannot invoke method on null

// ✓ Prevention: Check before use
if (str != null) {
    int len = str.length();
}

// ✓ Java 8+: Use Optional for functional null handling
int len = Optional.ofNullable(str)
    .map(String::length)
    .orElse(0);  // Returns 0 if str is null

// ✓ Java 14+: Records with non-nullable fields
public record Person(String name) {  // name cannot be null
    public Person {
        Objects.requireNonNull(name, "name cannot be null");
    }
}
```

### ArrayIndexOutOfBoundsException (Unchecked)

**Thrown when accessing array outside bounds**

```java
int[] arr = {1, 2, 3};  // Valid indices: 0, 1, 2
arr[5] = 10;  // ArrayIndexOutOfBoundsException: Index 5 out of bounds

// ✓ Prevention: Validate index before access
if (index >= 0 && index < arr.length) {
    arr[index] = 10;
}

// ✓ Use enhanced for loop (no index management)
for (int value : arr) {
    System.out.println(value);  // No index, no bounds issues
}
```

### NumberFormatException (Unchecked)

**Thrown by parseInt/parseDouble when string is not valid number**

```java
String num = "abc";
Integer.parseInt(num);  // NumberFormatException: For input string: "abc"

// ✓ Prevention: Try-catch for untrusted input
try {
    int value = Integer.parseInt(userInput);
} catch (NumberFormatException e) {
    System.out.println("Invalid number: " + userInput);
    value = 0;  // Default
}

// ✓ Validation approach
if (num.matches("-?\\d+")) {  // Regex check
    int value = Integer.parseInt(num);
}
```

### ClassCastException (Unchecked)

**Thrown when casting object to incompatible type**

```java
Object obj = "Hello";
Integer num = (Integer) obj;  // ClassCastException: class String cannot be cast to Integer

// ✓ Prevention: Use instanceof before casting
if (obj instanceof Integer) {
    Integer num = (Integer) obj;
}

// ✓ Java 16+: Pattern matching
if (obj instanceof Integer num) {  // Automatic casting
    System.out.println(num);  // num is already Integer
}
```

### IOException (Checked)

**Thrown by I/O operations when file/stream error occurs**

```java
// IOException is checked - must handle
try {
    FileReader fr = new FileReader("file.txt");  // Throws IOException
    BufferedReader br = new BufferedReader(fr);
    String line = br.readLine();
} catch (IOException e) {
    System.err.println("Cannot read file: " + e.getMessage());
}

// Or declare in method signature
void readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);  // Propagates IOException
}
```

---

## Suppressed Exceptions (try-with-resources)

**When multiple exceptions occur, later exceptions "suppress" earlier ones; accessible via getSuppressed()**

```java
class Resource implements AutoCloseable {
    public void close() throws Exception {
        throw new Exception("Close exception");  // Thrown during cleanup
    }
}

try (Resource r = new Resource()) {
    throw new Exception("Try block exception");  // Primary exception
} catch (Exception e) {
    System.out.println("Primary: " + e.getMessage());  // "Try block exception"
    
    // Access suppressed exceptions from close()
    Throwable[] suppressed = e.getSuppressed();
    for (Throwable s : suppressed) {
        System.out.println("Suppressed: " + s.getMessage());  // "Close exception"
    }
}
```

**Why suppression matters:**
- In try-with-resources, if resource.close() throws exception while handling primary exception, the close exception is suppressed
- Primary exception (from try block) is what caller sees
- Suppressed exceptions not lost - accessible via getSuppressed() for logging/debugging
- Prevents losing error context when cleanup also fails
- Stack trace shows both:
  ```
  Exception: Try block exception
      at Main.main(Main.java:10)
  Suppressed: Exception: Close exception
      at Resource.close(Resource.java:4)
  ```

---

## Key Takeaways

✓ **Checked exceptions:** Must handle or declare with throws; for recoverable errors (IOException, SQLException)
✓ **Unchecked exceptions:** Represent programming logic errors (NullPointerException, ArithmeticException)
✓ **try-with-resources:** Auto-closes resources; cleaner than try-finally pattern
✓ **Multiple catch:** Order matters - specific before general exceptions
✓ **Exception chaining:** Use `throw new HighLevelException(message, originalException)` to preserve root cause
✓ **Custom exceptions:** Domain-specific for clear communication; include context in constructor
✓ **Best practices:**
  - Catch specific exceptions for targeted recovery
  - Never swallow exceptions silently (empty catch blocks)
  - Log with full stack trace for debugging
  - Fix unchecked exceptions in code, don't catch them
  - Use Optional for null-safe operations (Java 8+)
  - Use try-with-resources for AutoCloseable resources

**Next:** [22. I/O Programming](22-IO-Programming.md)
