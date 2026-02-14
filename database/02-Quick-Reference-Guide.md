# Database Connection Quick Reference

A side-by-side comparison and quick reference for MySQL and MongoDB connections in Java.

---

## 1. Maven Dependencies Comparison

### MySQL
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

### MongoDB
```xml
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongodb-driver-sync</artifactId>
    <version>4.10.0</version>
</dependency>
```

---

## 2. Connection Strings

### MySQL Local
```
jdbc:mysql://localhost:3306/database_name
```

### MySQL Remote
```
jdbc:mysql://192.168.1.50:3306/database_name?useSSL=false&serverTimezone=UTC
```

### MongoDB Local
```
mongodb://localhost:27017
```

### MongoDB Remote
```
mongodb://remoteuser:password@192.168.1.50:27017
```

### MongoDB Atlas
```
mongodb+srv://username:password@cluster.mongodb.net/database?retryWrites=true
```

---

## 3. Basic Connection Code

### MySQL (JDBC)
```java
String url = "jdbc:mysql://localhost:3306/db";
Class.forName("com.mysql.cj.jdbc.Driver");
Connection conn = DriverManager.getConnection(url, "root", "password");
```

### MongoDB
```java
String uri = "mongodb://localhost:27017";
MongoClient client = MongoClients.create(uri);
MongoDatabase db = client.getDatabase("db_name");
```

---

## 4. CRUD Operations

### CREATE

**MySQL**
```java
String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setString(1, "John");
    stmt.setString(2, "john@example.com");
    stmt.executeUpdate();
}
```

**MongoDB**
```java
Document user = new Document()
    .append("name", "John")
    .append("email", "john@example.com");
collection.insertOne(user);
```

### READ

**MySQL**
```java
String sql = "SELECT * FROM users WHERE id = ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setInt(1, 1);
    ResultSet rs = stmt.executeQuery();
    while (rs.next()) {
        System.out.println(rs.getString("name"));
    }
}
```

**MongoDB**
```java
Document query = new Document("_id", 1);
Document user = collection.find(query).first();
System.out.println(user.getString("name"));
```

### UPDATE

**MySQL**
```java
String sql = "UPDATE users SET name = ? WHERE id = ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setString(1, "Jane");
    stmt.setInt(2, 1);
    stmt.executeUpdate();
}
```

**MongoDB**
```java
Document filter = new Document("_id", 1);
Document update = new Document("$set", new Document("name", "Jane"));
collection.updateOne(filter, update);
```

### DELETE

**MySQL**
```java
String sql = "DELETE FROM users WHERE id = ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setInt(1, 1);
    stmt.executeUpdate();
}
```

**MongoDB**
```java
Document filter = new Document("_id", 1);
collection.deleteOne(filter);
```

---

## 5. Query Examples

### Find with Conditions

**MySQL**
```java
String sql = "SELECT * FROM users WHERE age > ? AND age < ?";
// Prepare and execute
```

**MongoDB**
```java
Bson filter = Filters.and(
    Filters.gt("age", 25),
    Filters.lt("age", 30)
);
collection.find(filter);
```

### Sorting

**MySQL**
```java
String sql = "SELECT * FROM users ORDER BY name ASC";
```

**MongoDB**
```java
collection.find().sort(Sorts.ascending("name"));
```

### Pagination

**MySQL**
```java
String sql = "SELECT * FROM users LIMIT ? OFFSET ?";
// Use LIMIT 10 OFFSET 0 for first page
```

**MongoDB**
```java
collection.find().skip(0).limit(10);
```

### Aggregation

**MySQL**
```java
String sql = "SELECT COUNT(*) FROM users WHERE age > 25";
```

**MongoDB**
```java
long count = collection.countDocuments(Filters.gt("age", 25));
```

---

## 6. Connection Pool Configuration

### MySQL (HikariCP)
```java
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:mysql://localhost:3306/db");
config.setUsername("root");
config.setPassword("password");
config.setMaximumPoolSize(10);
config.setMinimumIdle(5);
HikariDataSource ds = new HikariDataSource(config);
```

### MongoDB (Built-in Connection Pooling)
```java
// MongoClient automatically handles connection pooling
MongoClient client = MongoClients.create(uri);
// Connection pooling is managed internally
```

---

## 7. Error Handling

### MySQL Exception Handling
```java
try {
    // Database operation
} catch (ClassNotFoundException e) {
    System.err.println("Driver not found");
} catch (SQLException e) {
    System.err.println("SQL Error: " + e.getMessage());
} finally {
    try {
        if (conn != null) conn.close();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

### MongoDB Exception Handling
```java
try (MongoClient client = MongoClients.create(uri)) {
    MongoDatabase db = client.getDatabase("db");
    // Operations
} catch (MongoException e) {
    System.err.println("MongoDB Error: " + e.getMessage());
} catch (Exception e) {
    System.err.println("Error: " + e.getMessage());
}
```

---

## 8. When to Use Each

### Use MySQL When:
- ✓ You need ACID transactions
- ✓ Data structure is well-defined (fixed schema)
- ✓ Complex relationships between tables
- ✓ Need to enforce data integrity with constraints
- ✓ Simple to moderate data volumes
- ✓ Mature ecosystem and wide support

### Use MongoDB When:
- ✓ Flexible or evolving data structure
- ✓ Document-style data (JSON-like)
- ✓ Need horizontal scalability
- ✓ High write throughput required
- ✓ Rapid development with changing requirements
- ✓ Handling unstructured or semi-structured data

---

## 9. Performance Comparison

| Operation | MySQL | MongoDB |
|-----------|-------|---------|
| Insert Single | Very Fast | Very Fast |
| Bulk Insert | Moderate | Very Fast |
| Simple Query | Very Fast | Very Fast |
| Complex Join | Moderate | Slower |
| Aggregate | Good | Excellent |
| Index Search | Very Fast | Very Fast |
| Write Throughput | Good | Excellent |
| Read Throughput | Excellent | Very Good |
| Memory Usage | Lower | Higher |

---

## 10. Setup Checklist

### MySQL Setup
- [ ] MySQL Server installed
- [ ] Service running
- [ ] Database and tables created
- [ ] JDBC driver in classpath
- [ ] Credentials configured
- [ ] Connection tested

### MongoDB Setup
- [ ] MongoDB Server installed
- [ ] Service running
- [ ] Database and collections created
- [ ] MongoDB driver in classpath
- [ ] URI configured
- [ ] Connection tested

---

## 11. Remote Connection Checklist

### MySQL Remote
- [ ] bind-address = 0.0.0.0 in my.cnf/my.ini
- [ ] Port 3306 open in firewall
- [ ] Remote user created with proper privileges
- [ ] Network connectivity verified
- [ ] Connection string includes correct IP:port
- [ ] SSL configured if needed

### MongoDB Remote
- [ ] bind_ip = 0.0.0.0 in mongod.conf
- [ ] Port 27017 open in firewall
- [ ] Authentication user created if needed
- [ ] Network connectivity verified
- [ ] Connection URI includes correct IP:port
- [ ] SSL configured for production

---

## 12. Quick Troubleshooting

### MySQL Issues
| Issue | Solution |
|-------|----------|
| No suitable driver | Add MySQL JDBC to classpath |
| Access denied | Verify username/password |
| Unknown database | Create database first |
| Connection timeout | Check firewall and network |
| SSL error | Add `?useSSL=false` to URL |

### MongoDB Issues
| Issue | Solution |
|-------|----------|
| Connection refused | Verify MongoDB is running |
| Authentication failed | Create user in MongoDB |
| Timeout | Check network and firewall |
| No servers available | Verify bind-address=0.0.0.0 |
| Invalid URI | Check connection string format |

---

## 13. Security Best Practices

### Both Databases
1. **Use strong passwords** - minimum 12 characters
2. **Use SSL/TLS** for remote connections
3. **Enable authentication** in production
4. **Create specific users** with limited privileges
5. **Store credentials** securely (not hardcoded)
6. **Use environment variables** for sensitive data
7. **Enable firewall rules** to restrict access
8. **Regular backups** of data
9. **Monitor access logs**
10. **Update regularly** to latest versions

### MySQL Specific
- Use prepared statements to prevent SQL injection
- Change default root password immediately
- Remove anonymous users
- Disable remote root login

### MongoDB Specific
- Enable authentication (create admin user)
- Use role-based access control
- Enable encryption at rest (MongoDB 4.2+)
- Use connection string authentication
- Whitelist IP addresses for Atlas

---

## 14. Resource References

### MySQL Resources
- [MySQL Official Documentation](https://dev.mysql.com/doc/)
- [MySQL JDBC Driver](https://github.com/mysql/mysql-connector-j)
- [HikariCP Connection Pool](https://github.com/brettwooldridge/HikariCP)

### MongoDB Resources
- [MongoDB Official Documentation](https://docs.mongodb.com/)
- [MongoDB Java Driver](https://github.com/mongodb/mongo-java-driver)
- [Spring Data MongoDB](https://spring.io/projects/spring-data-mongodb)
- [MongoDB Compass](https://www.mongodb.com/products/compass)

---

## 15. Configuration Files Examples

### database.properties
```properties
# MySQL Configuration
mysql.url=jdbc:mysql://localhost:3306/java_test_db
mysql.user=root
mysql.password=password
mysql.pool.size=10

# MongoDB Configuration
mongodb.url=mongodb://localhost:27017
mongodb.database=java_test_db
mongodb.username=admin
mongodb.password=password
```

### application.yml (Spring Boot)
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/java_test_db
    username: root
    password: password
    driver-class-name: com.mysql.cj.jdbc.Driver
  
  data:
    mongodb:
      uri: mongodb://localhost:27017
      database: java_test_db
```

---

**Next Steps**: 
- Read [MySQL Connection Guide](mysql/01-MySQL-Connection-Guide.md) for detailed MySQL setup
- Read [MongoDB Connection Guide](mongodb/01-MongoDB-Connection-Guide.md) for detailed MongoDB setup
