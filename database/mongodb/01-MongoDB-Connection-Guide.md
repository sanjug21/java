# MongoDB Connection Guide - Complete Tutorial

This guide covers everything you need to connect to MongoDB databases from Java applications, both locally and remotely.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Setup & Installation](#setup--installation)
3. [Maven Dependencies](#maven-dependencies)
4. [Local Connection](#local-connection)
5. [Remote Connection](#remote-connection)
6. [Spring Boot with application.properties](#step-7-spring-boot-connection-using-applicationproperties)
7. [Connection Best Practices](#connection-best-practices)
8. [Troubleshooting](#troubleshooting)

---

## Prerequisites

- **Java**: JDK 8 or higher
- **MongoDB**: Version 4.0+ or MongoDB 5.0+
- **Maven or Gradle**: For dependency management
- **MongoDB Compass** (optional): GUI for database management

---

## Setup & Installation

### Step 1: Install MongoDB Server

#### Windows
1. Download MongoDB from [mongodb.com](https://www.mongodb.com/try/download/community)
2. Run the MSI installer
3. Choose setup type (Complete or Custom)
4. Install MongoDB Compass (optional)
5. Configure MongoDB as a Windows Service
6. Verify installation

#### macOS
```bash
# Using Homebrew
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB
brew services start mongodb-community

# Verify
mongosh
```

#### Linux (Ubuntu)
```bash
# Import GPG key
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -

# Add repository
echo "deb http://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list

# Install
sudo apt-get update
sudo apt-get install -y mongodb-org

# Start service
sudo systemctl start mongod
sudo systemctl status mongod
```

### Step 2: Verify MongoDB Installation

```bash
# Using mongosh (new shell)
mongosh
# or using mongo (older shell)
mongo

# In MongoDB shell
db.version()
show dbs
```

### Step 3: Create Test Database and Collections

```javascript
// Switch to test database
use java_test_db

// Create a collection with sample data
db.users.insertMany([
    {
        _id: 1,
        name: "John Doe",
        email: "john@example.com",
        age: 25,
        createdAt: new Date()
    },
    {
        _id: 2,
        name: "Jane Smith",
        email: "jane@example.com",
        age: 28,
        createdAt: new Date()
    }
])

// Verify
db.users.find().pretty()

// Create index
db.users.createIndex({ email: 1 })
```

---

## Maven Dependencies

### Option 1: MongoDB Java Driver (Synchronous)

```xml
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongodb-driver-sync</artifactId>
    <version>4.10.0</version>
</dependency>
```

### Option 2: MongoDB Java Driver (Asynchronous)

```xml
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongodb-driver-async</artifactId>
    <version>3.12.14</version>
</dependency>
```

### Option 3: Spring Data MongoDB (Recommended for Spring)

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-mongodb</artifactId>
    <version>3.4.0</version>
</dependency>

<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongodb-driver-sync</artifactId>
    <version>4.10.0</version>
</dependency>
```

---

## Local Connection

### Step 1: Basic MongoDB Connection

Create a file: `MongoDBLocalConnection.java`

```java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class MongoDBLocalConnection {
    
    public static void main(String[] args) {
        // Connection string for local MongoDB
        String uri = "mongodb://localhost:27017";
        
        MongoClient mongoClient = null;
        
        try {
            // Step 1: Create MongoClient
            mongoClient = MongoClients.create(uri);
            System.out.println("✓ MongoDB client created");
            
            // Step 2: Get database
            MongoDatabase database = mongoClient.getDatabase("java_test_db");
            System.out.println("✓ Connected to database: java_test_db");
            
            // Step 3: Get collection and verify
            long collectionCount = database.getCollection("users").estimatedDocumentCount();
            System.out.println("✓ Users collection has " + collectionCount + " documents");
            
            // Step 4: Verify connection with ping
            Document command = new Document("ping", 1);
            Document result = database.runCommand(command);
            System.out.println("✓ Ping successful: " + result.toJson());
            
        } catch (Exception e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // Step 5: Close connection
            if (mongoClient != null) {
                mongoClient.close();
                System.out.println("✓ Connection closed");
            }
        }
    }
}
```

### Step 2: CRUD Operations with MongoDB

Create a file: `MongoDBCRUDOperations.java`

```java
import com.mongodb.client.*;
import org.bson.Document;
import java.util.ArrayList;
import java.util.List;

public class MongoDBCRUDOperations {
    
    private static final String URI = "mongodb://localhost:27017";
    private static final String DATABASE = "java_test_db";
    private static final String COLLECTION = "users";
    
    // CREATE - Insert document
    public static void createUser(String name, String email, int age) {
        try (MongoClient mongoClient = MongoClients.create(URI)) {
            
            MongoDatabase database = mongoClient.getDatabase(DATABASE);
            MongoCollection<Document> collection = database.getCollection(COLLECTION);
            
            Document user = new Document()
                .append("name", name)
                .append("email", email)
                .append("age", age)
                .append("createdAt", new java.util.Date());
            
            collection.insertOne(user);
            System.out.println("✓ User created: " + user.getObjectId("_id"));
            
        } catch (Exception e) {
            System.err.println("Error creating user: " + e.getMessage());
        }
    }
    
    // READ - Fetch all users
    public static void readAllUsers() {
        try (MongoClient mongoClient = MongoClients.create(URI)) {
            
            MongoDatabase database = mongoClient.getDatabase(DATABASE);
            MongoCollection<Document> collection = database.getCollection(COLLECTION);
            
            System.out.println("\n=== All Users ===");
            try (MongoCursor<Document> cursor = collection.find().iterator()) {
                while (cursor.hasNext()) {
                    Document doc = cursor.next();
                    System.out.printf("ID: %s | Name: %s | Email: %s | Age: %d\n",
                        doc.getObjectId("_id"),
                        doc.getString("name"),
                        doc.getString("email"),
                        doc.getInteger("age")
                    );
                }
            }
            
        } catch (Exception e) {
            System.err.println("Error reading users: " + e.getMessage());
        }
    }
    
    // READ - Find user by email
    public static void findUserByEmail(String email) {
        try (MongoClient mongoClient = MongoClients.create(URI)) {
            
            MongoDatabase database = mongoClient.getDatabase(DATABASE);
            MongoCollection<Document> collection = database.getCollection(COLLECTION);
            
            Document query = new Document("email", email);
            Document user = collection.find(query).first();
            
            if (user != null) {
                System.out.println("✓ Found: " + user.toJson());
            } else {
                System.out.println("✗ User not found");
            }
            
        } catch (Exception e) {
            System.err.println("Error finding user: " + e.getMessage());
        }
    }
    
    // UPDATE - Update user
    public static void updateUser(String email, String newName, int newAge) {
        try (MongoClient mongoClient = MongoClients.create(URI)) {
            
            MongoDatabase database = mongoClient.getDatabase(DATABASE);
            MongoCollection<Document> collection = database.getCollection(COLLECTION);
            
            Document filter = new Document("email", email);
            Document update = new Document("$set", new Document()
                .append("name", newName)
                .append("age", newAge)
            );
            
            com.mongodb.client.result.UpdateResult result = collection.updateOne(filter, update);
            System.out.println("✓ Updated " + result.getModifiedCount() + " user(s)");
            
        } catch (Exception e) {
            System.err.println("Error updating user: " + e.getMessage());
        }
    }
    
    // DELETE - Delete user
    public static void deleteUser(String email) {
        try (MongoClient mongoClient = MongoClients.create(URI)) {
            
            MongoDatabase database = mongoClient.getDatabase(DATABASE);
            MongoCollection<Document> collection = database.getCollection(COLLECTION);
            
            Document filter = new Document("email", email);
            com.mongodb.client.result.DeleteResult result = collection.deleteOne(filter);
            System.out.println("✓ Deleted " + result.getDeletedCount() + " user(s)");
            
        } catch (Exception e) {
            System.err.println("Error deleting user: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        // Create
        createUser("Alice Johnson", "alice@example.com", 30);
        
        // Read all
        readAllUsers();
        
        // Find by email
        findUserByEmail("alice@example.com");
        
        // Update
        updateUser("alice@example.com", "Alice Updated", 31);
        
        // Read again
        readAllUsers();
        
        // Delete
        deleteUser("john@example.com");
    }
}
```

### Step 3: Advanced Queries

Create a file: `MongoDBAdvancedQueries.java`

```java
import com.mongodb.client.*;
import com.mongodb.client.model.Filters;
import com.mongodb.client.model.Sorts;
import org.bson.Document;

public class MongoDBAdvancedQueries {
    
    private static final String URI = "mongodb://localhost:27017";
    private static final String DATABASE = "java_test_db";
    private static final String COLLECTION = "users";
    
    public static void main(String[] args) {
        try (MongoClient mongoClient = MongoClients.create(URI)) {
            
            MongoDatabase database = mongoClient.getDatabase(DATABASE);
            MongoCollection<Document> collection = database.getCollection(COLLECTION);
            
            // Query 1: Find users with age > 25
            System.out.println("=== Users with age > 25 ===");
            try (MongoCursor<Document> cursor = 
                 collection.find(Filters.gt("age", 25)).iterator()) {
                while (cursor.hasNext()) {
                    Document doc = cursor.next();
                    System.out.println(doc.getString("name") + " - Age: " + doc.getInteger("age"));
                }
            }
            
            // Query 2: Sort by name ascending
            System.out.println("\n=== Users sorted by name ===");
            try (MongoCursor<Document> cursor = 
                 collection.find().sort(Sorts.ascending("name")).iterator()) {
                while (cursor.hasNext()) {
                    System.out.println(cursor.next().getString("name"));
                }
            }
            
            // Query 3: Limit and skip (pagination)
            System.out.println("\n=== First 5 users (pagination) ===");
            try (MongoCursor<Document> cursor = 
                 collection.find().skip(0).limit(5).iterator()) {
                while (cursor.hasNext()) {
                    System.out.println(cursor.next().toJson());
                }
            }
            
            // Query 4: Count documents
            long count = collection.countDocuments();
            System.out.println("\nTotal users: " + count);
            
            // Query 5: Complex filter (AND condition)
            System.out.println("\n=== Users: age > 25 AND age < 30 ===");
            try (MongoCursor<Document> cursor = 
                 collection.find(Filters.and(
                     Filters.gt("age", 25),
                     Filters.lt("age", 30)
                 )).iterator()) {
                while (cursor.hasNext()) {
                    Document doc = cursor.next();
                    System.out.println(doc.getString("name") + " - Age: " + doc.getInteger("age"));
                }
            }
            
            // Query 6: OR condition
            System.out.println("\n=== Users: John OR Jane ===");
            try (MongoCursor<Document> cursor = 
                 collection.find(Filters.or(
                     Filters.eq("name", "John Doe"),
                     Filters.eq("name", "Jane Smith")
                 )).iterator()) {
                while (cursor.hasNext()) {
                    System.out.println(cursor.next().getString("name"));
                }
            }
            
            // Query 7: Projection (select specific fields)
            System.out.println("\n=== Users (name and email only) ===");
            Document projection = new Document()
                .append("name", 1)
                .append("email", 1)
                .append("_id", 0);
            
            try (MongoCursor<Document> cursor = 
                 collection.find().projection(projection).iterator()) {
                while (cursor.hasNext()) {
                    System.out.println(cursor.next().toJson());
                }
            }
            
        } catch (Exception e) {
            System.err.println("✗ Error: " + e.getMessage());
        }
    }
}
```

---

## Remote Connection

### Step 1: Configure MongoDB for Remote Access

#### On MongoDB Server Machine

1. **Edit MongoDB Configuration File**

   **Windows** (`C:\Program Files\MongoDB\Server\5.0\mongod.cfg`):
   ```yaml
   net:
     bindIp: 0.0.0.0
     port: 27017
   ```

   **Linux** (`/etc/mongod.conf`):
   ```yaml
   net:
     bindIp: 0.0.0.0
     port: 27017
   ```

2. **Restart MongoDB Service**

   **Windows**:
   ```powershell
   net stop MongoDB
   net start MongoDB
   ```

   **Linux**:
   ```bash
   sudo systemctl restart mongod
   ```

3. **Create Remote User** (Optional but Recommended)

   ```javascript
   // Connect to admin database
   use admin
   
   // Create user with authentication
   db.createUser({
       user: "remoteuser",
       pwd: "secure_password",
       roles: ["readWrite", "dbAdmin"]
   })
   
   // Verify
   db.getUsers()
   ```

4. **Configure Firewall**

   **Windows Firewall**:
   ```powershell
   netsh advfirewall firewall add rule name="MongoDB" dir=in action=allow protocol=tcp localport=27017
   ```

   **Linux (ufw)**:
   ```bash
   sudo ufw allow 27017/tcp
   ```

### Step 2: Remote Connection from Java

Create a file: `MongoDBRemoteConnection.java`

```java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class MongoDBRemoteConnection {
    
    public static void main(String[] args) {
        
        // Remote MongoDB connection parameters
        String remoteHost = "192.168.1.50";
        int remotePort = 27017;
        String remoteDatabase = "java_test_db";
        
        // Connection string without authentication
        String uri = "mongodb://" + remoteHost + ":" + remotePort;
        
        MongoClient mongoClient = null;
        
        try {
            // Step 1: Create MongoClient for remote connection
            mongoClient = MongoClients.create(uri);
            System.out.println("✓ Connected to remote MongoDB at " + remoteHost);
            
            // Step 2: Get database
            MongoDatabase database = mongoClient.getDatabase(remoteDatabase);
            System.out.println("✓ Accessed database: " + remoteDatabase);
            
            // Step 3: Verify connection
            Document command = new Document("ping", 1);
            Document result = database.runCommand(command);
            System.out.println("✓ Ping successful");
            
            // Step 4: List collections
            System.out.println("\n=== Collections ===");
            for (String collectionName : database.listCollectionNames()) {
                System.out.println("- " + collectionName);
            }
            
        } catch (Exception e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
            System.err.println("\nTroubleshooting checklist:");
            System.err.println("1. Verify host IP and port");
            System.err.println("2. Check firewall rules");
            System.err.println("3. Verify MongoDB bind-address is 0.0.0.0");
            System.err.println("4. Check MongoDB service is running");
        } finally {
            if (mongoClient != null) {
                mongoClient.close();
                System.out.println("\n✓ Connection closed");
            }
        }
    }
}
```

### Step 3: Remote Connection with Authentication

Create a file: `MongoDBRemoteConnectionAuth.java`

```java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class MongoDBRemoteConnectionAuth {
    
    public static void main(String[] args) {
        
        // Remote connection parameters
        String remoteHost = "192.168.1.50";
        int remotePort = 27017;
        String username = "remoteuser";
        String password = "secure_password";
        String authDatabase = "admin";
        String targetDatabase = "java_test_db";
        
        // Connection string with authentication
        String uri = String.format(
            "mongodb://%s:%s@%s:%d/%s?authSource=%s",
            username,
            password,
            remoteHost,
            remotePort,
            targetDatabase,
            authDatabase
        );
        
        MongoClient mongoClient = null;
        
        try {
            // Step 1: Create MongoClient with authentication
            mongoClient = MongoClients.create(uri);
            System.out.println("✓ Connected to remote MongoDB with authentication");
            
            // Step 2: Get database
            MongoDatabase database = mongoClient.getDatabase(targetDatabase);
            System.out.println("✓ Accessed database: " + targetDatabase);
            
            // Step 3: Verify connection
            Document command = new Document("ping", 1);
            Document result = database.runCommand(command);
            System.out.println("✓ Ping successful: " + result.toJson());
            
        } catch (Exception e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
        } finally {
            if (mongoClient != null) {
                mongoClient.close();
                System.out.println("✓ Connection closed");
            }
        }
    }
}
```

### Step 4: Remote Connection with SSL

Create a file: `MongoDBRemoteConnectionSSL.java`

```java
import com.mongodb.MongoClientSettings;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class MongoDBRemoteConnectionSSL {
    
    public static void main(String[] args) {
        
        // MongoDB connection string with SSL
        String uri = "mongodb+srv://remoteuser:secure_password@" +
                    "mongo.example.com/java_test_db?" +
                    "retryWrites=true&w=majority&ssl=true";
        
        MongoClient mongoClient = null;
        
        try {
            // Step 1: Configure SSL settings
            MongoClientSettings settings = MongoClientSettings.builder()
                .applyConnectionString(new com.mongodb.connection.ConnectionString(uri))
                .build();
            
            // Step 2: Create MongoClient
            mongoClient = MongoClients.create(settings);
            System.out.println("✓ Secure connection established with SSL");
            
            // Step 3: Get database
            MongoDatabase database = mongoClient.getDatabase("java_test_db");
            System.out.println("✓ Connected to database");
            
            // Step 4: Verify
            Document command = new Document("ping", 1);
            Document result = database.runCommand(command);
            System.out.println("✓ Ping successful");
            
        } catch (Exception e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
        } finally {
            if (mongoClient != null) {
                mongoClient.close();
                System.out.println("✓ Connection closed");
            }
        }
    }
}
```

### Step 5: MongoDB Atlas Cloud Connection

Create a file: `MongoDBAtlasConnection.java`

```java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class MongoDBAtlasConnection {
    
    public static void main(String[] args) {
        
        // MongoDB Atlas connection string
        // Format: mongodb+srv://username:password@cluster.mongodb.net/database?retryWrites=true&w=majority
        String atlasUri = "mongodb+srv://admin:password123@" +
                         "cluster0.abcde.mongodb.net/java_test_db?" +
                         "retryWrites=true&w=majority";
        
        MongoClient mongoClient = null;
        
        try {
            // Step 1: Create MongoClient for Atlas
            mongoClient = MongoClients.create(atlasUri);
            System.out.println("✓ Connected to MongoDB Atlas");
            
            // Step 2: Get database
            MongoDatabase database = mongoClient.getDatabase("java_test_db");
            System.out.println("✓ Accessed database");
            
            // Step 3: Verify connection
            Document command = new Document("ping", 1);
            Document result = database.runCommand(command);
            System.out.println("✓ Ping successful");
            
            // Step 4: Check connection status
            System.out.println("\n=== Connection Details ===");
            System.out.println("Status: " + result.get("ok"));
            
        } catch (Exception e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
            System.err.println("\nCommon issues:");
            System.err.println("1. Wrong credentials");
            System.err.println("2. IP address not whitelisted in Atlas");
            System.err.println("3. Network connectivity issues");
        } finally {
            if (mongoClient != null) {
                mongoClient.close();
                System.out.println("\n✓ Connection closed");
            }
        }
    }
}
```

### Step 6: Spring Data MongoDB Configuration

Create a file: `MongoDBSpringConfig.java`

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.config.AbstractMongoClientConfiguration;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;

@Configuration
public class MongoDBSpringConfig extends AbstractMongoClientConfiguration {
    
    // For local MongoDB
    @Override
    public MongoClient mongoClient() {
        return MongoClients.create("mongodb://localhost:27017");
    }
    
    @Override
    protected String getDatabaseName() {
        return "java_test_db";
    }
    
    // For remote MongoDB with authentication
    /*
    @Override
    public MongoClient mongoClient() {
        String uri = "mongodb://remoteuser:secure_password@" +
                    "192.168.1.50:27017/java_test_db?authSource=admin";
        return MongoClients.create(uri);
    }
    */
}
```

Create a file: `User.java` (Spring Data Model)

```java
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "users")
public class User {
    
    @Id
    private String id;
    
    private String name;
    private String email;
    private int age;
    private java.util.Date createdAt;
    
    public User() {}
    
    public User(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.createdAt = new java.util.Date();
    }
    
    // Getters and setters
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
    
    public java.util.Date getCreatedAt() { return createdAt; }
    public void setCreatedAt(java.util.Date createdAt) { this.createdAt = createdAt; }
}
```

Create a file: `UserRepository.java` (Spring Data Repository)

```java
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends MongoRepository<User, String> {
    User findByEmail(String email);
}
```

### Step 7: Spring Boot Connection using application.properties

#### Maven Dependencies for Spring Boot MongoDB

Add these to your `pom.xml`:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <!-- Spring Boot Data MongoDB Starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    
    <!-- Optional: Spring Boot Web (for REST APIs) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

#### application.properties - Local Connection

Create `src/main/resources/application.properties`:

```properties
# ========================================
# Local MongoDB Database Configuration
# ========================================

# MongoDB URI
spring.data.mongodb.uri=mongodb://localhost:27017/java_test_db

# Alternative: Individual properties
# spring.data.mongodb.host=localhost
# spring.data.mongodb.port=27017
# spring.data.mongodb.database=java_test_db

# Connection Pool Settings
spring.data.mongodb.pool.max-connections=100
spring.data.mongodb.pool.min-connections=10

# Optional: Enable auto-index creation
spring.data.mongodb.auto-index-creation=true

# Logging
logging.level.org.springframework.data.mongodb=DEBUG
logging.level.org.mongodb.driver=INFO
```

#### application.properties - Remote Connection

```properties
# ========================================
# Remote MongoDB Database Configuration
# ========================================

# MongoDB URI - Remote Server with Authentication
spring.data.mongodb.uri=mongodb://remoteuser:secure_password@192.168.1.50:27017/java_test_db?authSource=admin

# Alternative: Individual properties
# spring.data.mongodb.host=192.168.1.50
# spring.data.mongodb.port=27017
# spring.data.mongodb.database=java_test_db
# spring.data.mongodb.username=remoteuser
# spring.data.mongodb.password=secure_password
# spring.data.mongodb.authentication-database=admin

# Connection Pool Settings
spring.data.mongodb.pool.max-connections=100
spring.data.mongodb.pool.min-connections=10
spring.data.mongodb.pool.max-wait-time=5000

# Connection Timeout (milliseconds)
spring.data.mongodb.socket-timeout=5000
spring.data.mongodb.connect-timeout=5000

# Auto-index creation
spring.data.mongodb.auto-index-creation=false

# Logging
logging.level.org.springframework.data.mongodb=INFO
logging.level.org.mongodb.driver=WARN
```

#### application.properties - MongoDB Atlas Cloud

```properties
# ========================================
# MongoDB Atlas Cloud Configuration
# ========================================

# MongoDB Atlas URI (SRV connection string)
spring.data.mongodb.uri=mongodb+srv://admin:password123@cluster0.abcde.mongodb.net/java_test_db?retryWrites=true&w=majority

# Connection Pool
spring.data.mongodb.pool.max-connections=50
spring.data.mongodb.pool.min-connections=5

# Retry settings
spring.data.mongodb.pool.max-wait-time=10000

# Auto-index creation (use false for production)
spring.data.mongodb.auto-index-creation=false

# Logging
logging.level.org.springframework.data.mongodb=INFO
logging.level.org.mongodb.driver=WARN
```

#### application.yml Alternative (Optional)

Create `src/main/resources/application.yml`:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://localhost:27017/java_test_db
      auto-index-creation: true
      pool:
        max-connections: 100
        min-connections: 10
        max-wait-time: 5000
      socket-timeout: 5000
      connect-timeout: 5000

logging:
  level:
    org.springframework.data.mongodb: DEBUG
    org.mongodb.driver: INFO
```

#### Complete Spring Boot Application Example

Create a file: `SpringBootMongoDBApp.java`

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.CommandLineRunner;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import com.mongodb.client.MongoDatabase;

@SpringBootApplication
public class SpringBootMongoDBApp implements CommandLineRunner {
    
    @Autowired
    private MongoTemplate mongoTemplate;
    
    public static void main(String[] args) {
        SpringApplication.run(SpringBootMongoDBApp.class, args);
    }
    
    @Override
    public void run(String... args) throws Exception {
        // Test the database connection
        try {
            MongoDatabase database = mongoTemplate.getDb();
            System.out.println("✓ MongoDB connected successfully!");
            System.out.println("Database Name: " + database.getName());
            
            // List collections
            System.out.println("\n=== Collections ===");
            for (String collectionName : database.listCollectionNames()) {
                System.out.println("- " + collectionName);
            }
            
            // Get collection count
            long userCount = mongoTemplate.getCollection("users").countDocuments();
            System.out.println("\nTotal users: " + userCount);
            
        } catch (Exception e) {
            System.err.println("✗ MongoDB connection failed: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

#### MongoDB Document Model (Entity)

Create a file: `User.java`

```java
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;
import org.springframework.data.mongodb.core.index.Indexed;
import java.time.LocalDateTime;

@Document(collection = "users")
public class User {
    
    @Id
    private String id;
    
    @Field("name")
    private String name;
    
    @Field("email")
    @Indexed(unique = true)
    private String email;
    
    @Field("age")
    private Integer age;
    
    @Field("created_at")
    private LocalDateTime createdAt;
    
    // Constructors
    public User() {
        this.createdAt = LocalDateTime.now();
    }
    
    public User(String name, String email, Integer age) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.createdAt = LocalDateTime.now();
    }
    
    // Getters and Setters
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public Integer getAge() { return age; }
    public void setAge(Integer age) { this.age = age; }
    
    public LocalDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(LocalDateTime createdAt) { this.createdAt = createdAt; }
    
    @Override
    public String toString() {
        return String.format("User[id=%s, name='%s', email='%s', age=%d]", 
            id, name, email, age);
    }
}
```

#### MongoDB Repository Interface

Create a file: `UserRepository.java`

```java
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.data.mongodb.repository.Query;
import org.springframework.stereotype.Repository;
import java.util.List;
import java.util.Optional;

@Repository
public interface UserRepository extends MongoRepository<User, String> {
    
    // Derived query methods (Spring Data generates implementation)
    Optional<User> findByEmail(String email);
    List<User> findByName(String name);
    List<User> findByAgeGreaterThan(int age);
    List<User> findByAgeBetween(int start, int end);
    long countByAge(int age);
    
    // Custom query using @Query annotation
    @Query("{name: ?0}")
    List<User> findUsersByNameCustomQuery(String name);
    
    @Query("{age: {$gt: ?0, $lt: ?1}}")
    List<User> findUsersByAgeRange(int minAge, int maxAge);
}
```

#### Service Layer Example

Create a file: `UserService.java`

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.Optional;

@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    // CREATE
    public User createUser(User user) {
        return userRepository.save(user);
    }
    
    // READ - All
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    // READ - By ID
    public Optional<User> getUserById(String id) {
        return userRepository.findById(id);
    }
    
    // READ - By Email
    public Optional<User> getUserByEmail(String email) {
        return userRepository.findByEmail(email);
    }
    
    // UPDATE
    public User updateUser(String id, User updatedUser) {
        return userRepository.findById(id)
            .map(user -> {
                user.setName(updatedUser.getName());
                user.setEmail(updatedUser.getEmail());
                user.setAge(updatedUser.getAge());
                return userRepository.save(user);
            })
            .orElseThrow(() -> new RuntimeException("User not found with id: " + id));
    }
    
    // DELETE
    public void deleteUser(String id) {
        userRepository.deleteById(id);
    }
    
    // Count
    public long getUserCount() {
        return userRepository.count();
    }
}
```

#### REST Controller Example (Optional)

Create a file: `UserController.java`

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.createUser(user);
        return ResponseEntity.ok(created);
    }
    
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable String id) {
        return userService.getUserById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable String id, @RequestBody User user) {
        try {
            User updated = userService.updateUser(id, user);
            return ResponseEntity.ok(updated);
        } catch (RuntimeException e) {
            return ResponseEntity.notFound().build();
        }
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable String id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

#### MongoTemplate Advanced Operations

Create a file: `UserMongoService.java`

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.data.mongodb.core.query.Update;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class UserMongoService {
    
    @Autowired
    private MongoTemplate mongoTemplate;
    
    // Find users by age range
    public List<User> findUsersByAgeRange(int minAge, int maxAge) {
        Query query = new Query();
        query.addCriteria(Criteria.where("age").gte(minAge).lte(maxAge));
        return mongoTemplate.find(query, User.class);
    }
    
    // Update user age by email
    public void updateUserAge(String email, int newAge) {
        Query query = new Query(Criteria.where("email").is(email));
        Update update = new Update().set("age", newAge);
        mongoTemplate.updateFirst(query, update, User.class);
    }
    
    // Delete users older than specified age
    public void deleteUsersOlderThan(int age) {
        Query query = new Query(Criteria.where("age").gt(age));
        mongoTemplate.remove(query, User.class);
    }
    
    // Complex query with multiple criteria
    public List<User> findUsersByComplexCriteria(String name, int minAge) {
        Query query = new Query();
        query.addCriteria(
            Criteria.where("name").regex(name, "i")
            .and("age").gte(minAge)
        );
        return mongoTemplate.find(query, User.class);
    }
}
```

#### Environment-Specific Configuration

**application.properties** (default):
```properties
spring.profiles.active=dev
```

**application-dev.properties** (development):
```properties
# Local Development MongoDB
spring.data.mongodb.uri=mongodb://localhost:27017/java_test_db
spring.data.mongodb.auto-index-creation=true
logging.level.org.springframework.data.mongodb=DEBUG
```

**application-prod.properties** (production):
```properties
# Production MongoDB (Remote or Atlas)
spring.data.mongodb.uri=mongodb+srv://${MONGO_USERNAME}:${MONGO_PASSWORD}@cluster0.abcde.mongodb.net/production_db?retryWrites=true&w=majority
spring.data.mongodb.auto-index-creation=false
logging.level.org.springframework.data.mongodb=WARN

# Enhanced connection pool for production
spring.data.mongodb.pool.max-connections=200
spring.data.mongodb.pool.min-connections=20
spring.data.mongodb.pool.max-wait-time=10000
```

**application-test.properties** (testing):
```properties
# Test MongoDB (Embedded or Test Container)
spring.data.mongodb.uri=mongodb://localhost:27017/test_db
spring.data.mongodb.auto-index-creation=true
logging.level.org.springframework.data.mongodb=INFO
```

#### Run Commands

```bash
# Run with default profile (dev)
mvn spring-boot:run

# Run with production profile
mvn spring-boot:run -Dspring-boot.run.profiles=prod

# Or using environment variable
export SPRING_PROFILES_ACTIVE=prod
mvn spring-boot:run

# Run with environment variables for credentials
export MONGO_USERNAME=admin
export MONGO_PASSWORD=secretpassword
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

#### Configuration Class (Alternative to Properties)

Create a file: `MongoConfig.java`

```java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.config.AbstractMongoClientConfiguration;
import org.springframework.data.mongodb.core.MongoTemplate;

@Configuration
public class MongoConfig extends AbstractMongoClientConfiguration {
    
    @Value("${spring.data.mongodb.uri}")
    private String mongoUri;
    
    @Value("${spring.data.mongodb.database:java_test_db}")
    private String databaseName;
    
    @Override
    protected String getDatabaseName() {
        return databaseName;
    }
    
    @Override
    public MongoClient mongoClient() {
        return MongoClients.create(mongoUri);
    }
    
    @Bean
    public MongoTemplate mongoTemplate() {
        return new MongoTemplate(mongoClient(), getDatabaseName());
    }
}
```

---

## Connection Best Practices

1. **Use connection pooling** automatically handled by MongoClient
2. **Reuse MongoClient** - create once per application
3. **Close MongoClient gracefully** in finally or try-with-resources
4. **Use authentication** for production environments
5. **Enable SSL for remote connections**
6. **Implement retry logic** for failed connections
7. **Use connection timeouts** to prevent hanging
8. **Monitor connection usage**
9. **Index frequently queried fields**
10. **Use projections** to limit data transfer

---

## Troubleshooting

### Issue: "Connection refused"
**Solution**: Verify MongoDB is running
```bash
# Linux
sudo systemctl status mongod

# Windows
sc query MongoDB
```

### Issue: "Authentication failed"
**Solution**: Create user in MongoDB
```javascript
use admin
db.createUser({
    user: "remoteuser",
    pwd: "password",
    roles: ["readWrite", "dbAdmin"]
})
```

### Issue: "Timeout connecting to server"
**Solution**: Check network connectivity and firewall
```bash
# Test connection
ping 192.168.1.50
telnet 192.168.1.50 27017
```

### Issue: "MongoSocketOpenException"
**Solution**: Ensure MongoDB bind-address is correct
```yaml
# In mongod.conf
net:
  bindIp: 0.0.0.0
  port: 27017
```

### Issue: "No servers available"
**Solution**: Verify server is running and accessible
```bash
# Check if port is listening
netstat -an | grep 27017
```

---

## Connection Comparison

| Aspect | Local | Remote | Atlas (Cloud) |
|--------|-------|--------|---------------|
| URI | `mongodb://localhost:27017` | `mongodb://host:27017` | `mongodb+srv://...` |
| Default Port | 27017 | 27017 | 443 (secure) |
| Configuration | System default | bind-address=0.0.0.0 | N/A (managed) |
| Authentication | Optional | Recommended | Required |
| SSL | Optional | Recommended | Required |
| Firewall | N/A | Required | Open IPs |
| Management | Local | Self-managed | MongoDB manages |

---

## Performance Tips

1. **Create indexes** for frequently queried fields
2. **Use projection** to limit fields returned
3. **Implement pagination** with skip() and limit()
4. **Use bulk operations** for mass inserts
5. **Monitor slow queries** in production
6. **Use connection pooling** for concurrent access
7. **Cache frequently accessed data**
8. **Minimize document size** when possible

---

Next: Check [Database Overview](../01-Database-Overview.md)
