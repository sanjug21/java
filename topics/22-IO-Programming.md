# 22. I/O PROGRAMMING

## Stream Types

**Java I/O uses two approaches: Byte streams for binary data, Character streams for text with Unicode support**

## Stream Types

**Java I/O uses two approaches: Byte streams for binary data, Character streams for text with Unicode support**

### Byte Streams

**Handle binary data (images, audio, etc.); lowest-level I/O, process single bytes**

```java
// FileInputStream & FileOutputStream
// Use when: copying binary files, reading/writing non-text data
try (FileInputStream fis = new FileInputStream("input.txt");
     FileOutputStream fos = new FileOutputStream("output.txt")) {
    
    int data;
    while ((data = fis.read()) != -1) {  // Read one byte, -1 means EOF
        fos.write(data);
    }
}  // Auto-closes both streams

// Efficient buffered approach for large files
byte[] buffer = new byte[8192];  // Read in chunks
int bytesRead;
while ((bytesRead = fis.read(buffer)) != -1) {
    fos.write(buffer, 0, bytesRead);
}
```

**Characteristics:**
- Slowest approach (single byte at a time)
- Use for binary files: images, audio, compiled classes
- Always wrap with BufferedInputStream for performance

### Character Streams

**Handle text data with Unicode support; better for human-readable text**

```java
// FileReader & FileWriter
// Use when: reading/writing text files, XML, JSON, source code
try (FileReader fr = new FileReader("input.txt");
     FileWriter fw = new FileWriter("output.txt")) {
    
    int ch;
    while ((ch = fr.read()) != -1) {  // Read one character (may be multi-byte)
        fw.write(ch);
    }
}
```

**Key differences from Byte Streams:**
- Works with characters (not bytes), handles Unicode automatically
- FileReader uses default charset; use InputStreamReader for custom charset
- Better for text files, but single-char reading is slow
- Similar performance issue as byte streams - wrap with BufferedReader

**Charset awareness:**
```java
// Specify UTF-8 encoding explicitly
FileReader fr = new FileReader("file.txt", StandardCharsets.UTF_8);
// Or use InputStreamReader for more control
InputStreamReader isr = new InputStreamReader(new FileInputStream("file.txt"), StandardCharsets.UTF_8);
```

---

## Buffered Streams

**Improves performance dramatically with internal buffer; collects multiple bytes/chars before I/O operation**

```java
// BufferedReader wraps FileReader - reads into memory buffer
try (BufferedReader br = new BufferedReader(new FileReader("input.txt"));
     BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"))) {
    
    String line;
    while ((line = br.readLine()) != null) {  // Reads full line at once
        bw.write(line);
        bw.newLine();
    }
}  // Auto-flushes and closes
```

**Performance impact:**
- Unbuffered byte-by-byte reading: ~1000ms for 1MB file
- Buffered reading: ~10ms for same file (100x faster!)
- Default buffer size: 8192 bytes - sufficient for most use cases
- Use `readLine()` for text files, `read(buffer)` for binary data

**When to use:
- Text files with readLine(): Always use BufferedReader
- Binary files: Always use BufferedInputStream/BufferedOutputStream
- Small files (<1KB): Direct streams acceptable
- Large files/high performance: Always buffer

---

## Object Serialization

**Convert object to byte stream for storage/transmission; enables object persistence and distributed systems**

```java
class Employee implements Serializable {
    private static final long serialVersionUID = 1L;  // Version control for compatibility
    String name;
    int empId;
    transient String password;  // Won't be serialized - security/privacy
    
    Employee(String name, int empId, String password) {
        this.name = name;
        this.empId = empId;
        this.password = password;
    }
}

// Serialize - convert object to bytes
try (ObjectOutputStream oos = new ObjectOutputStream(
        new FileOutputStream("employee.ser"))) {
    Employee emp = new Employee("Pushpendra", 101, "secret");
    oos.writeObject(emp);  // Entire object graph serialized
}

// Deserialize - reconstruct object from bytes
try (ObjectInputStream ois = new ObjectInputStream(
        new FileInputStream("employee.ser"))) {
    Employee emp = (Employee) ois.readObject();
    System.out.println(emp.name);  // Pushpendra
    System.out.println(emp.password);  // null (transient not serialized)
}
```

**Key Points:**
- `Serializable`: Marker interface (no methods) - tells JVM class can be serialized
- `serialVersionUID`: Ensures compatibility when class changes; JVM checks version during deserialization
- `transient`: Fields marked won't be serialized - use for passwords, temporary data, non-serializable objects
- All referenced objects must also be Serializable (deep serialization)
- Deserialization creates object without calling constructor - bypasses initialization logic

**When to use:**
- Caching objects to disk
- Sending objects over network (RPC, messaging)
- Session persistence in web applications
- Distributed computing frameworks (Spark, Hadoop)

**Security warning:** Never deserialize untrusted data - potential remote code execution vulnerability

---

## NIO (New I/O)

**Modern I/O API with channel-based architecture; non-blocking, buffer-based, enables high-performance servers**

### Path & Files (Recommended modern approach)

```java
import java.nio.file.*;

// Create Path - represents file system path (doesn't check existence)
Path path = Paths.get("example.txt");
// Path path = Path.of("example.txt");  // Java 11+

// Read all lines (simple, loads entire file in memory)
List<String> lines = Files.readAllLines(path);

// Write lines (creates or overwrites file)
List<String> data = Arrays.asList("Line 1", "Line 2", "Line 3");
Files.write(path, data);  // StandardOpenOption.CREATE_NEW to fail if exists

// Copy file with options
Path source = Paths.get("source.txt");
Path dest = Paths.get("dest.txt");
Files.copy(source, dest, StandardCopyOption.REPLACE_EXISTING);  // Overwrite if exists

// Delete file - returns false if doesn't exist
Files.deleteIfExists(path);

// Check existence and properties
boolean exists = Files.exists(path);
boolean isFile = Files.isRegularFile(path);
boolean isDir = Files.isDirectory(path);
long size = Files.size(path);

// Stream lines for large files (doesn't load all in memory)
try (Stream<String> stream = Files.lines(path)) {
    stream.filter(s -> s.contains("keyword"))
          .forEach(System.out::println);
}
```

**Advantages over traditional I/O:**
- Simple static methods - no wrapper stream chains
- Files.readAllLines() vs BufferedReader + FileReader boilerplate
- Better exception handling - doesn't throw checked exceptions
- More functional - supports streams for memory-efficient processing
- Modern, thread-safe implementation

### FileChannel & ByteBuffer (Advanced non-blocking)

**Channel-based I/O for advanced scenarios; enables non-blocking operations and memory-mapped files**

```java
try (FileInputStream fis = new FileInputStream("input.txt");
     FileChannel channel = fis.getChannel()) {
    
    ByteBuffer buffer = ByteBuffer.allocate(1024);  // Direct buffer in JVM heap
    int bytesRead = channel.read(buffer);  // Read into buffer
    
    buffer.flip();  // Switch from write mode to read mode (position=0, limit=bytesRead)
    while (buffer.hasRemaining()) {
        System.out.print((char) buffer.get());  // Read one byte
    }
}
```

**When to use Channels:**
- Non-blocking network I/O (Selector pattern)
- Memory-mapped files for large files
- Direct memory operations for performance
- File locking and shared access
- Most applications: Files class is sufficient and simpler

**Selector pattern for servers (non-blocking):**
```java
Selector selector = Selector.open();
ServerSocketChannel serverChannel = ServerSocketChannel.open();
serverChannel.configureBlocking(false);
serverChannel.register(selector, SelectionKey.OP_ACCEPT);
// Can now handle multiple connections without blocking
```

---

## File Operations

### File Class (Legacy - use Files/Path for new code)

**Traditional approach; still useful for some file property checks**

```java
import java.io.File;

File file = new File("example.txt");

// Check properties (fast - doesn't require file existence)
boolean exists = file.exists();
boolean isFile = file.isFile();
boolean isDirectory = file.isDirectory();
long size = file.length();  // Size in bytes (-1 if file not found)
long modified = file.lastModified();  // Milliseconds since epoch

// Create/Delete (use Files.createFile/deleteIfExists for new code)
boolean created = file.createNewFile();  // Returns false if exists
boolean deleted = file.delete();  // Returns false if not found

// Directory operations
File dir = new File("myFolder");
dir.mkdir();  // Create single directory (fails if parent doesn't exist)
dir.mkdirs();  // Create nested directories (parent created if needed)

// List files
String[] files = dir.list();  // Returns null if not a directory
File[] fileObjects = dir.listFiles();  // Use to get File objects
File[] filtered = dir.listFiles(f -> f.isFile() && f.getName().endsWith(".txt"));
```

**Modern alternative (Files class):**
```java
// Simpler, better error handling, no checked exceptions
Path path = Paths.get("example.txt");
Files.exists(path);
Files.isRegularFile(path);
Files.size(path);
Files.createFile(path);
Files.delete(path);
Files.list(dir);  // Returns Stream<Path>
```

---

## Reading from Console

**Scanner for standard input (works in IDEs), Console for secure input (only in terminal)**

```java
import java.util.Scanner;

// Scanner - recommended for most use cases
Scanner scanner = new Scanner(System.in);  // Reads from standard input
System.out.print("Enter name: ");
String name = scanner.nextLine();  // Reads full line
System.out.print("Enter age: ");
int age = scanner.nextInt();  // Parses integer
// scanner.nextLine();  // Skip trailing newline if needed

// Split input into tokens
System.out.print("Enter space-separated numbers: ");
Scanner tokenScanner = new Scanner(scanner.nextLine());
while (tokenScanner.hasNextInt()) {
    System.out.println(tokenScanner.nextInt());
}

// Console - secure input (null in IDEs, works only in terminal)
Console console = System.console();  
if (console != null) {  // Check if running in terminal
    String username = console.readLine("Username: ");
    char[] password = console.readPassword("Password: ");  // Doesn't echo input
} else {
    System.out.println("Running in IDE - console unavailable");
    // Fallback to Scanner
}
```

**Key differences:**
- Scanner: Works in IDEs, visible input, has parsing methods (nextInt, nextDouble)
- Console: Terminal only, secure password input, no echo
- Scanner creates new thread for input parsing - slower
- Console is lightweight - faster, better for secure operations

**Best practices:**
- User interaction: Scanner (works everywhere)
- Login forms: Console (secure password handling)
- Batch processing: Neither - read from files instead

---

## Stream Hierarchy

**Understanding the stream decorator pattern and when to use each**

```
InputStream/OutputStream (Raw binary - basic, slow)
├── FileInputStream/FileOutputStream - Reads/writes to file
├── ByteArrayInputStream/ByteArrayOutputStream - Reads/writes to byte array
├── BufferedInputStream/BufferedOutputStream - Adds internal buffer (faster)
└── ObjectInputStream/ObjectOutputStream - Serializes/deserializes objects

Reader/Writer (Text with charset support - faster for text)
├── FileReader/FileWriter - Reads/writes text files
├── StringReader/StringWriter - Reads/writes from/to String
├── BufferedReader/BufferedWriter - Adds line buffering
├── InputStreamReader/OutputStreamWriter - Bridges byte/char streams
└── PrintWriter - Formatted text output
```

**Decision matrix:**
| Data Type | Use | Benefit |
|-----------|-----|----------|
| Binary files | Byte streams + BufferedInputStream | Handles all binary data |
| Text files | BufferedReader + FileReader | 100x faster than unbuffered |
| Console I/O | Scanner (input), PrintStream (output) | Parsing support |
| Objects | ObjectInputStream/ObjectOutputStream | Automatic serialization |
| Large files | Files.lines(path) + Stream | Memory-efficient |
| In-memory | ByteArrayInputStream/StringReader | No file I/O |

**Performance comparison:**
- Unbuffered reading 1MB file: 1000ms
- Buffered reading 1MB file: 10ms
- NIO channel reading 1MB file: 5ms
- Memory-mapped file 1MB: 1ms

---

## Best Practices

### ✓ DO

```java
// 1. Use try-with-resources - automatically closes all streams
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine()) != null) {
        processLine(line);
    }
}  // All streams closed automatically, even if exception

// 2. Use buffered streams for better performance
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("file.txt"));
// 100x faster than unbuffered for file operations

// 3. Use NIO for modern code
List<String> lines = Files.readAllLines(Paths.get("file.txt"));
// Cleaner, simpler, no stream wrapper chains

// 4. Use proper stream for data type
BufferedReader br;  // for text
BufferedInputStream bis;  // for binary

// 5. Flush before closing for buffered writers
bw.flush();  // Ensures data written to disk
bw.close();
```

### ✗ DON'T

```java
// 1. Don't forget to close streams
FileReader fr = new FileReader("file.txt");
String line = fr.readLine();  // ERROR: no close, resource leak

// 2. Don't use unbuffered for large files
FileInputStream fis = new FileInputStream("large.txt");
while ((fis.read()) != -1) { }  // WRONG: 100x slower, single byte per operation

// 3. Don't mix byte and character streams inconsistently
FileInputStream fis = new FileInputStream("text.txt");
fis.read();  // WRONG: treats text as bytes, won't handle Unicode properly
// Use FileReader or InputStreamReader instead

// 4. Don't assume default charset
FileReader fr = new FileReader("utf8File.txt");  // Uses system default charset!
// Better: explicitly specify charset
InputStreamReader isr = new InputStreamReader(
    new FileInputStream("utf8File.txt"), 
    StandardCharsets.UTF_8);

// 5. Don't deserialize untrusted data
ObjectInputStream ois = new ObjectInputStream(networkStream);
Object obj = ois.readObject();  // SECURITY RISK: remote code execution
// Only deserialize data from trusted sources
```

---

## Practical Example

```java
// Copy file efficiently with buffer
public static void copyFile(String source, String dest) throws IOException {
    // Use try-with-resources for automatic cleanup
    try (BufferedInputStream bis = new BufferedInputStream(
            new FileInputStream(source), 8192);  // Custom buffer size
         BufferedOutputStream bos = new BufferedOutputStream(
            new FileOutputStream(dest), 8192)) {
        
        byte[] buffer = new byte[8192];  // 8KB chunks
        int bytesRead;
        while ((bytesRead = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, bytesRead);  // Only write bytes actually read
        }
    }  // Streams auto-close, even if exception thrown
}

// Modern NIO approach (simpler)
public static void copyFileNIO(String source, String dest) throws IOException {
    Files.copy(Paths.get(source), Paths.get(dest), 
               StandardCopyOption.REPLACE_EXISTING);
}

// Read file to String (Java 11+)
String content = Files.readString(Paths.get("file.txt"));

// Write String to file
Files.writeString(Paths.get("file.txt"), "Hello World", 
                  StandardOpenOption.CREATE_NEW);  // Fail if exists

// Process large file line-by-line (memory-efficient)
try (Stream<String> lines = Files.lines(Paths.get("huge.txt"))) {
    lines.filter(s -> s.contains("keyword"))  // Lazy evaluation
         .limit(100)  // Stop after 100 matches
         .forEach(System.out::println);
}  // Only loads lines into memory as needed
```

---

## Key Takeaways

✓ **Byte streams:** Use for binary data (images, audio), always buffer them
✓ **Character streams:** Use for text data with automatic Unicode handling
✓ **Buffered streams:** 100x faster than unbuffered - always use for files
✓ **Serialization:** Convert objects to bytes for persistence/transmission; security risk from untrusted sources
✓ **NIO:** Modern approach with Files class - simpler, cleaner than traditional I/O
✓ **Channel I/O:** Advanced non-blocking operations for high-performance servers
✓ **try-with-resources:** Always use to ensure streams closed even if exception thrown
✓ **charset awareness:** Always specify encoding explicitly; don't rely on system default

**Performance summary:**
- Unbuffered: 1000ms for 1MB file
- Buffered: 10ms (100x faster)
- NIO: 5ms
- Memory-mapped: 1ms

**Next:** [23. Java 8 Features](23-Java-8-Features.md)
