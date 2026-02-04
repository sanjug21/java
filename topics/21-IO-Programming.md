# 21. I/O PROGRAMMING

## Stream Types

### Byte Streams

**Handle binary data (images, audio, etc.)**

```java
// FileInputStream & FileOutputStream
try (FileInputStream fis = new FileInputStream("input.txt");
     FileOutputStream fos = new FileOutputStream("output.txt")) {
    
    int data;
    while ((data = fis.read()) != -1) {
        fos.write(data);
    }
}
```

### Character Streams

**Handle text data (better for Unicode)**

```java
// FileReader & FileWriter
try (FileReader fr = new FileReader("input.txt");
     FileWriter fw = new FileWriter("output.txt")) {
    
    int ch;
    while ((ch = fr.read()) != -1) {
        fw.write(ch);
    }
}
```

---

## Buffered Streams

**Improves performance with internal buffer**

```java
try (BufferedReader br = new BufferedReader(new FileReader("input.txt"));
     BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"))) {
    
    String line;
    while ((line = br.readLine()) != null) {
        bw.write(line);
        bw.newLine();
    }
}
```

---

## Object Serialization

**Convert object to byte stream for storage/transmission**

```java
class Employee implements Serializable {
    private static final long serialVersionUID = 1L;
    String name;
    int empId;
    transient String password;  // Won't be serialized
    
    Employee(String name, int empId, String password) {
        this.name = name;
        this.empId = empId;
        this.password = password;
    }
}

// Serialize
try (ObjectOutputStream oos = new ObjectOutputStream(
        new FileOutputStream("employee.ser"))) {
    Employee emp = new Employee("Pushpendra", 101, "secret");
    oos.writeObject(emp);
}

// Deserialize
try (ObjectInputStream ois = new ObjectInputStream(
        new FileInputStream("employee.ser"))) {
    Employee emp = (Employee) ois.readObject();
    System.out.println(emp.name);  // Pushpendra
    System.out.println(emp.password);  // null (transient)
}
```

**Key Points:**
- Implement `Serializable` interface
- Use `serialVersionUID` for version control
- Use `transient` to exclude fields

---

## NIO (New I/O)

### Path & Files

```java
import java.nio.file.*;

// Create Path
Path path = Paths.get("example.txt");

// Read all lines
List<String> lines = Files.readAllLines(path);

// Write lines
List<String> data = Arrays.asList("Line 1", "Line 2", "Line 3");
Files.write(path, data);

// Copy file
Path source = Paths.get("source.txt");
Path dest = Paths.get("dest.txt");
Files.copy(source, dest, StandardCopyOption.REPLACE_EXISTING);

// Delete file
Files.deleteIfExists(path);

// Check existence
boolean exists = Files.exists(path);
```

---

### FileChannel & ByteBuffer

```java
try (FileInputStream fis = new FileInputStream("input.txt");
     FileChannel channel = fis.getChannel()) {
    
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    int bytesRead = channel.read(buffer);
    
    buffer.flip();  // Switch to read mode
    while (buffer.hasRemaining()) {
        System.out.print((char) buffer.get());
    }
}
```

---

## File Operations

### File Class

```java
import java.io.File;

File file = new File("example.txt");

// Check properties
boolean exists = file.exists();
boolean isFile = file.isFile();
boolean isDirectory = file.isDirectory();
long size = file.length();  // Size in bytes

// Create/Delete
boolean created = file.createNewFile();
boolean deleted = file.delete();

// Directory operations
File dir = new File("myFolder");
dir.mkdir();  // Create directory
dir.mkdirs();  // Create nested directories

// List files
String[] files = dir.list();
File[] fileObjects = dir.listFiles();
```

---

## Reading from Console

```java
import java.util.Scanner;

// Scanner
Scanner scanner = new Scanner(System.in);
System.out.print("Enter name: ");
String name = scanner.nextLine();
System.out.print("Enter age: ");
int age = scanner.nextInt();

// Console (returns null in IDEs)
Console console = System.console();
if (console != null) {
    String username = console.readLine("Username: ");
    char[] password = console.readPassword("Password: ");
}
```

---

## Stream Hierarchy

```
InputStream/OutputStream (Byte)
├── FileInputStream/FileOutputStream
├── BufferedInputStream/BufferedOutputStream
└── ObjectInputStream/ObjectOutputStream

Reader/Writer (Character)
├── FileReader/FileWriter
├── BufferedReader/BufferedWriter
└── InputStreamReader/OutputStreamWriter
```

---

## Best Practices

### ✓ DO

```java
// Use try-with-resources
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    // Use br
}

// Use buffered streams
BufferedReader br = new BufferedReader(new FileReader("file.txt"));

// Use NIO for modern code
Files.readAllLines(Paths.get("file.txt"));
```

### ✗ DON'T

```java
// Don't forget to close
FileReader fr = new FileReader("file.txt");
// ... forgot to close

// Don't use unbuffered for large files
FileInputStream fis = new FileInputStream("large.txt");
while (fis.read() != -1) { }  // Slow
```

---

## Practical Example

```java
// Copy file with buffer
public static void copyFile(String source, String dest) throws IOException {
    try (BufferedInputStream bis = new BufferedInputStream(
            new FileInputStream(source));
         BufferedOutputStream bos = new BufferedOutputStream(
            new FileOutputStream(dest))) {
        
        byte[] buffer = new byte[8192];
        int bytesRead;
        while ((bytesRead = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, bytesRead);
        }
    }
}

// Read file to String (Java 11+)
String content = Files.readString(Paths.get("file.txt"));

// Write String to file
Files.writeString(Paths.get("file.txt"), "Hello World");
```

---

## Key Takeaways

✓ **Byte streams:** Binary data (InputStream/OutputStream)
✓ **Character streams:** Text data (Reader/Writer)
✓ **Buffered streams:** Better performance
✓ **Serialization:** Object persistence
✓ **NIO:** Modern, efficient file operations
✓ Always use **try-with-resources**

**Next:** [22. Java 8 Features](22-Java-8-Features.md)
