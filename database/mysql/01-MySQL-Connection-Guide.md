# MySQL Connection Guide - Complete Tutorial

This guide covers everything you need to connect to MySQL databases from Java applications, both locally and remotely.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Setup & Installation](#setup--installation)
3. [Maven Dependencies](#maven-dependencies)
4. [Local Connection](#local-connection)
5. [Remote Connection](#remote-connection)
6. [Spring Boot with application.properties](#step-5-spring-boot-connection-using-applicationproperties)
7. [Connection Best Practices](#connection-best-practices)
8. [Troubleshooting](#troubleshooting)

---

## Prerequisites

- **Java**: JDK 8 or higher
- **MySQL Server**: Version 5.7+ or MySQL 8.0+
- **Maven or Gradle**: For dependency management
- **MySQL Workbench** (optional): For GUI database management

---

## Setup & Installation

### Step 1: Install MySQL Server

#### Windows
1. Download MySQL from [mysql.com](https://www.mysql.com/downloads/)
2. Run the installer
3. Choose setup type (Developer Default, Server only, etc.)
4. Configure MySQL Server (port 3306 is default)
5. Configure MySQL as a Windows Service
6. Set root password
7. Complete installation

#### macOS
```bash
# Using Homebrew
brew install mysql
brew services start mysql
mysql_secure_installation
```

#### Linux (Ubuntu)
```bash
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```

### Step 2: Verify MySQL Installation

```bash
mysql --version
mysql -u root -p
# Enter password when prompted
```

### Step 3: Create Test Database

```sql
-- Login to MySQL
mysql -u root -p

-- Create database
CREATE DATABASE java_test_db;

-- Create sample table
USE java_test_db;

CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    age INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert sample data
INSERT INTO users (name, email, age) VALUES 
('John Doe', 'john@example.com', 25),
('Jane Smith', 'jane@example.com', 28);

-- Verify
SELECT * FROM users;
```

---

## Maven Dependencies

Add these dependencies to your `pom.xml`:

### Option 1: MySQL JDBC Driver (Recommended for direct JDBC)

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

### Option 2: MariaDB Driver (Alternative)

```xml
<dependency>
    <groupId>org.mariadb.jdbc</groupId>
    <artifactId>mariadb-java-client</artifactId>
    <version>3.1.4</version>
</dependency>
```

### Option 3: HikariCP (Connection Pooling - Recommended for Production)

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>5.0.1</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

---

## Local Connection

### Step 1: Basic JDBC Connection

Create a file: `MySQLLocalConnection.java`

```java
import java.sql.*;

public class MySQLLocalConnection {
    
    // Database connection parameters
    private static final String DB_URL = "jdbc:mysql://localhost:3306/java_test_db";
    private static final String DB_USER = "root";
    private static final String DB_PASSWORD = "your_password";
    
    public static void main(String[] args) {
        Connection connection = null;
        
        try {
            // Step 1: Load the MySQL JDBC driver
            Class.forName("com.mysql.cj.jdbc.Driver");
            System.out.println("✓ MySQL JDBC Driver loaded successfully");
            
            // Step 2: Establish connection
            connection = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
            System.out.println("✓ Connected to MySQL database successfully");
            
            // Step 3: Verify connection with simple query
            if (connection != null) {
                DatabaseMetaData metadata = connection.getMetaData();
                System.out.println("Database: " + metadata.getDatabaseProductName());
                System.out.println("Version: " + metadata.getDatabaseProductVersion());
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("✗ MySQL JDBC Driver not found: " + e.getMessage());
        } catch (SQLException e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
        } finally {
            // Step 4: Close connection
            try {
                if (connection != null && !connection.isClosed()) {
                    connection.close();
                    System.out.println("✓ Connection closed");
                }
            } catch (SQLException e) {
                System.err.println("Error closing connection: " + e.getMessage());
            }
        }
    }
}
```

### Step 2: Perform CRUD Operations

Create a file: `MySQLCRUDOperations.java`

```java
import java.sql.*;
import java.util.*;

public class MySQLCRUDOperations {
    
    private static final String DB_URL = "jdbc:mysql://localhost:3306/java_test_db";
    private static final String DB_USER = "root";
    private static final String DB_PASSWORD = "your_password";
    
    // CREATE - Insert data
    public static void createUser(String name, String email, int age) {
        String sql = "INSERT INTO users (name, email, age) VALUES (?, ?, ?)";
        
        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setString(1, name);
            stmt.setString(2, email);
            stmt.setInt(3, age);
            
            int rowsInserted = stmt.executeUpdate();
            if (rowsInserted > 0) {
                System.out.println("✓ User created successfully");
            }
        } catch (SQLException e) {
            System.err.println("Error creating user: " + e.getMessage());
        }
    }
    
    // READ - Fetch all users
    public static void readAllUsers() {
        String sql = "SELECT * FROM users";
        
        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            
            System.out.println("\n=== All Users ===");
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String email = rs.getString("email");
                int age = rs.getInt("age");
                String createdAt = rs.getString("created_at");
                
                System.out.printf("ID: %d | Name: %s | Email: %s | Age: %d | Created: %s\n", 
                    id, name, email, age, createdAt);
            }
        } catch (SQLException e) {
            System.err.println("Error reading users: " + e.getMessage());
        }
    }
    
    // UPDATE - Update user data
    public static void updateUser(int id, String name, int age) {
        String sql = "UPDATE users SET name = ?, age = ? WHERE id = ?";
        
        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setString(1, name);
            stmt.setInt(2, age);
            stmt.setInt(3, id);
            
            int rowsUpdated = stmt.executeUpdate();
            if (rowsUpdated > 0) {
                System.out.println("✓ User updated successfully");
            }
        } catch (SQLException e) {
            System.err.println("Error updating user: " + e.getMessage());
        }
    }
    
    // DELETE - Remove user
    public static void deleteUser(int id) {
        String sql = "DELETE FROM users WHERE id = ?";
        
        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setInt(1, id);
            
            int rowsDeleted = stmt.executeUpdate();
            if (rowsDeleted > 0) {
                System.out.println("✓ User deleted successfully");
            }
        } catch (SQLException e) {
            System.err.println("Error deleting user: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        // Create
        createUser("Alice Johnson", "alice@example.com", 30);
        
        // Read
        readAllUsers();
        
        // Update
        updateUser(1, "John Updated", 26);
        
        // Read again
        readAllUsers();
        
        // Delete
        deleteUser(2);
    }
}
```

### Step 3: Connection with HikariCP (Connection Pool)

Create a file: `MySQLConnectionPool.java`

```java
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import java.sql.*;

public class MySQLConnectionPool {
    
    private static HikariDataSource dataSource;
    
    static {
        initializeConnectionPool();
    }
    
    private static void initializeConnectionPool() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/java_test_db");
        config.setUsername("root");
        config.setPassword("your_password");
        config.setMaximumPoolSize(10);
        config.setMinimumIdle(5);
        config.setConnectionTimeout(30000);
        config.setIdleTimeout(600000);
        config.setMaxLifetime(1800000);
        
        dataSource = new HikariDataSource(config);
        System.out.println("✓ HikariCP Connection Pool initialized");
    }
    
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }
    
    public static void closePool() {
        if (dataSource != null && !dataSource.isClosed()) {
            dataSource.close();
            System.out.println("✓ Connection pool closed");
        }
    }
    
    public static void main(String[] args) {
        try (Connection conn = getConnection()) {
            System.out.println("✓ Got connection from pool");
            
            // Use connection
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT COUNT(*) FROM users");
            if (rs.next()) {
                System.out.println("Total users: " + rs.getInt(1));
            }
            
        } catch (SQLException e) {
            System.err.println("Error: " + e.getMessage());
        } finally {
            closePool();
        }
    }
}
```

---

## Remote Connection

### Step 1: Configure MySQL Server for Remote Access

#### On MySQL Server Machine

1. **Edit MySQL Configuration File**

   **Windows** (`my.ini`):
   ```
   [mysqld]
   bind-address = 0.0.0.0
   port = 3306
   ```

   **Linux/macOS** (`/etc/mysql/mysql.conf.d/mysqld.cnf`):
   ```
   [mysqld]
   bind-address = 0.0.0.0
   port = 3306
   ```

2. **Restart MySQL Service**

   **Windows**:
   ```powershell
   net stop MySQL80
   net start MySQL80
   ```

   **Linux**:
   ```bash
   sudo systemctl restart mysql
   ```

3. **Create Remote User**

   ```sql
   -- Login as root
   mysql -u root -p
   
   -- Create remote user (allow access from any IP)
   CREATE USER 'remoteuser'@'%' IDENTIFIED BY 'secure_password';
   
   -- Grant privileges
   GRANT ALL PRIVILEGES ON java_test_db.* TO 'remoteuser'@'%';
   FLUSH PRIVILEGES;
   
   -- Or grant from specific IP
   CREATE USER 'remoteuser'@'192.168.1.100' IDENTIFIED BY 'secure_password';
   GRANT ALL PRIVILEGES ON java_test_db.* TO 'remoteuser'@'192.168.1.100';
   FLUSH PRIVILEGES;
   
   -- Verify user
   SELECT HOST, USER FROM mysql.user WHERE USER = 'remoteuser';
   ```

4. **Configure Firewall** (if needed)

   **Windows Firewall**:
   ```powershell
   netsh advfirewall firewall add rule name="MySQL" dir=in action=allow protocol=tcp localport=3306
   ```

   **Linux (ufw)**:
   ```bash
   sudo ufw allow 3306/tcp
   ```

### Step 2: Remote Connection from Java Application

Create a file: `MySQLRemoteConnection.java`

```java
import java.sql.*;

public class MySQLRemoteConnection {
    
    // Remote database parameters
    private static final String REMOTE_HOST = "192.168.1.50"; // Server IP
    private static final int REMOTE_PORT = 3306;
    private static final String REMOTE_DB = "java_test_db";
    private static final String REMOTE_USER = "remoteuser";
    private static final String REMOTE_PASSWORD = "secure_password";
    
    public static void main(String[] args) {
        Connection connection = null;
        
        try {
            // Step 1: Build connection URL
            String connectionUrl = String.format(
                "jdbc:mysql://%s:%d/%s?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true",
                REMOTE_HOST, REMOTE_PORT, REMOTE_DB
            );
            
            System.out.println("Attempting to connect to: " + connectionUrl);
            
            // Step 2: Load driver
            Class.forName("com.mysql.cj.jdbc.Driver");
            System.out.println("✓ MySQL JDBC Driver loaded");
            
            // Step 3: Establish connection
            connection = DriverManager.getConnection(
                connectionUrl,
                REMOTE_USER,
                REMOTE_PASSWORD
            );
            System.out.println("✓ Connected to remote MySQL database");
            
            // Step 4: Verify connection
            if (connection != null) {
                DatabaseMetaData metadata = connection.getMetaData();
                System.out.println("Database: " + metadata.getDatabaseProductName());
                System.out.println("Server Version: " + metadata.getDatabaseProductVersion());
                System.out.println("Driver Version: " + metadata.getDriverVersion());
            }
            
        } catch (ClassNotFoundException e) {
            System.err.println("✗ JDBC Driver not found: " + e.getMessage());
        } catch (SQLException e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
            System.err.println("Common issues:");
            System.err.println("1. Host/Port incorrect");
            System.err.println("2. Database/User/Password incorrect");
            System.err.println("3. Firewall blocking connection");
            System.err.println("4. MySQL not configured for remote access");
        } finally {
            try {
                if (connection != null && !connection.isClosed()) {
                    connection.close();
                    System.out.println("✓ Connection closed");
                }
            } catch (SQLException e) {
                System.err.println("Error closing connection: " + e.getMessage());
            }
        }
    }
}
```

### Step 3: Remote Connection with SSL (Secure)

Create a file: `MySQLRemoteConnectionSSL.java`

```java
import java.sql.*;

public class MySQLRemoteConnectionSSL {
    
    public static void main(String[] args) {
        Connection connection = null;
        
        try {
            // URL with SSL enabled
            String connectionUrl = "jdbc:mysql://192.168.1.50:3306/java_test_db" +
                "?useSSL=true" +
                "&serverTimezone=UTC" +
                "&serverSslCert=/path/to/server-cert.pem" +
                "&clientCertificateKeyStoreUrl=file:/path/to/keystore.jks" +
                "&clientCertificateKeyStorePassword=keystorePassword" +
                "&allowPublicKeyRetrieval=true";
            
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection(
                connectionUrl,
                "remoteuser",
                "secure_password"
            );
            
            System.out.println("✓ Secure remote connection established");
            
        } catch (Exception e) {
            System.err.println("✗ Connection failed: " + e.getMessage());
        } finally {
            try {
                if (connection != null && !connection.isClosed()) {
                    connection.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### Step 4: Test Connection with Configuration File

Create `database.properties`:

```properties
# Local Configuration
db.local.url=jdbc:mysql://localhost:3306/java_test_db
db.local.user=root
db.local.password=root_password

# Remote Configuration
db.remote.url=jdbc:mysql://192.168.1.50:3306/java_test_db
db.remote.user=remoteuser
db.remote.password=secure_password
db.remote.useSSL=false
db.remote.serverTimezone=UTC
```

Create a file: `ConfigurableDBConnection.java`

```java
import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class ConfigurableDBConnection {
    
    public static void main(String[] args) throws IOException {
        
        // Load properties
        Properties props = new Properties();
        try (InputStream input = ConfigurableDBConnection.class
                .getClassLoader().getResourceAsStream("database.properties")) {
            if (input == null) {
                System.out.println("Sorry, unable to find database.properties");
                return;
            }
            props.load(input);
        }
        
        // Choose environment
        String environment = "remote"; // or "local"
        String url = props.getProperty("db." + environment + ".url");
        String user = props.getProperty("db." + environment + ".user");
        String password = props.getProperty("db." + environment + ".password");
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection conn = DriverManager.getConnection(url, user, password);
            System.out.println("✓ Connected using " + environment + " configuration");
            conn.close();
        } catch (Exception e) {
            System.err.println("✗ Error: " + e.getMessage());
        }
    }
}
```

### Step 5: Spring Boot Connection using application.properties

#### Maven Dependencies for Spring Boot

Add these to your `pom.xml`:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <!-- Spring Boot JDBC Starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    
    <!-- MySQL Driver -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.33</version>
    </dependency>
    
    <!-- For JPA/Hibernate (Optional) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
```

#### application.properties - Local Connection

Create `src/main/resources/application.properties`:

```properties
# ========================================
# Local MySQL Database Configuration
# ========================================

# JDBC URL
spring.datasource.url=jdbc:mysql://localhost:3306/java_test_db
spring.datasource.username=root
spring.datasource.password=your_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Connection Pool - HikariCP (Default in Spring Boot)
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000

# MySQL Specific Settings
spring.datasource.hikari.data-source-properties.cachePrepStmts=true
spring.datasource.hikari.data-source-properties.prepStmtCacheSize=250
spring.datasource.hikari.data-source-properties.prepStmtCacheSqlLimit=2048
spring.datasource.hikari.data-source-properties.useServerPrepStmts=true

# JPA/Hibernate Properties (if using Spring Data JPA)
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# Logging
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

#### application.properties - Remote Connection

```properties
# ========================================
# Remote MySQL Database Configuration
# ========================================

# JDBC URL - Remote Server
spring.datasource.url=jdbc:mysql://192.168.1.50:3306/java_test_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
spring.datasource.username=remoteuser
spring.datasource.password=secure_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Connection Pool - HikariCP
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.connection-test-query=SELECT 1

# JPA/Hibernate Properties
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# Connection Validation
spring.datasource.hikari.pool-name=RemoteMySQLPool
spring.datasource.hikari.health-check-registry=true
```

#### application.yml Alternative (Optional)

Create `src/main/resources/application.yml`:

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/java_test_db
    username: root
    password: your_password
    driver-class-name: com.mysql.cj.jdbc.Driver
    
    hikari:
      maximum-pool-size: 10
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
      pool-name: MySQLPool
      
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        format_sql: true
        dialect: org.hibernate.dialect.MySQLDialect

logging:
  level:
    org.hibernate.SQL: DEBUG
```

#### Spring Boot Application Example

Create a file: `SpringBootMySQLApp.java`

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.CommandLineRunner;
import org.springframework.beans.factory.annotation.Autowired;
import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.DatabaseMetaData;

@SpringBootApplication
public class SpringBootMySQLApp implements CommandLineRunner {
    
    @Autowired
    private DataSource dataSource;
    
    public static void main(String[] args) {
        SpringApplication.run(SpringBootMySQLApp.class, args);
    }
    
    @Override
    public void run(String... args) throws Exception {
        // Test the database connection
        try (Connection connection = dataSource.getConnection()) {
            System.out.println("✓ Database connected successfully!");
            
            DatabaseMetaData metaData = connection.getMetaData();
            System.out.println("Database: " + metaData.getDatabaseProductName());
            System.out.println("Version: " + metaData.getDatabaseProductVersion());
            System.out.println("URL: " + metaData.getURL());
            System.out.println("Driver: " + metaData.getDriverName());
            System.out.println("Driver Version: " + metaData.getDriverVersion());
            
        } catch (Exception e) {
            System.err.println("✗ Database connection failed: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

#### Repository Example with JdbcTemplate

Create a file: `UserRepository.java`

```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.List;

@Repository
public class UserRepository {
    
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    // CREATE
    public int createUser(String name, String email, int age) {
        String sql = "INSERT INTO users (name, email, age) VALUES (?, ?, ?)";
        return jdbcTemplate.update(sql, name, email, age);
    }
    
    // READ - Count
    public int getUserCount() {
        String sql = "SELECT COUNT(*) FROM users";
        return jdbcTemplate.queryForObject(sql, Integer.class);
    }
    
    // READ - All
    public List<String> getAllUserNames() {
        String sql = "SELECT name FROM users";
        return jdbcTemplate.queryForList(sql, String.class);
    }
    
    // UPDATE
    public int updateUser(int id, String name, int age) {
        String sql = "UPDATE users SET name = ?, age = ? WHERE id = ?";
        return jdbcTemplate.update(sql, name, age, id);
    }
    
    // DELETE
    public int deleteUser(int id) {
        String sql = "DELETE FROM users WHERE id = ?";
        return jdbcTemplate.update(sql, id);
    }
}
```

#### JPA Entity Example

Create a file: `User.java`

```java
import jakarta.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, length = 100)
    private String name;
    
    @Column(unique = true, length = 100)
    private String email;
    
    private Integer age;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    // Constructors
    public User() {}
    
    public User(String name, String email, Integer age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }
    
    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public Integer getAge() { return age; }
    public void setAge(Integer age) { this.age = age; }
    
    public LocalDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(LocalDateTime createdAt) { this.createdAt = createdAt; }
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
    
    @Override
    public String toString() {
        return String.format("User[id=%d, name='%s', email='%s', age=%d]", 
            id, name, email, age);
    }
}
```

Create a file: `UserJpaRepository.java`

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface UserJpaRepository extends JpaRepository<User, Long> {
    
    // Custom query methods
    List<User> findByName(String name);
    List<User> findByEmail(String email);
    List<User> findByAgeGreaterThan(int age);
}
```

#### Environment-Specific Configuration

**application.properties** (default):
```properties
spring.profiles.active=dev
```

**application-dev.properties** (development):
```properties
# Local Development Database
spring.datasource.url=jdbc:mysql://localhost:3306/java_test_db
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

**application-prod.properties** (production):
```properties
# Production Remote Database
spring.datasource.url=jdbc:mysql://192.168.1.50:3306/production_db
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false

# Enhanced Connection Pool for Production
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=10
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
```

---

## Connection Best Practices

1. **Always use PreparedStatements** to prevent SQL injection
2. **Use Connection Pooling** (HikariCP) for production
3. **Close resources properly** with try-with-resources
4. **Handle exceptions gracefully**
5. **Use configuration files** for credentials
6. **Enable timeouts** to prevent hanging connections
7. **Use SSL for remote connections**
8. **Implement retry logic** for failed connections
9. **Monitor connection usage**
10. **Validate credentials** before deployment

---

## Troubleshooting

### Issue: "No suitable driver found"
**Solution**: Add MySQL JDBC driver to classpath
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

### Issue: "Access denied for user 'root'@'localhost'"
**Solution**: Verify username and password
```sql
mysql -u root -p
-- Enter correct password
```

### Issue: "Connection timed out" (Remote)
**Solution**: Check firewall and MySQL configuration
```bash
# Check port is open
netstat -an | grep 3306

# Verify bind-address in my.ini/my.cnf
```

### Issue: "Unknown database 'java_test_db'"
**Solution**: Create the database first
```sql
CREATE DATABASE java_test_db;
```

### Issue: "SSL connection error"
**Solution**: Add SSL parameters to URL
```
&useSSL=false&allowPublicKeyRetrieval=true
```

---

## Summary

| Aspect | Local | Remote |
|--------|-------|--------|
| Connection URL | `jdbc:mysql://localhost:3306/db` | `jdbc:mysql://host:3306/db` |
| Default Port | 3306 | 3306 |
| Configuration | System default | bind-address = 0.0.0.0 |
| User Permissions | Local users | Remote users (user@'%') |
| Security | Less critical | Use SSL |
| Performance | Fast | Network dependent |

---

Next: Explore [MongoDB Connection Guide](../mongodb/01-MongoDB-Connection-Guide.md)
