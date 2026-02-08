# 12. DESIGN PATTERNS - COMPLETE GUIDE

Java design patterns are reusable solutions for common programming problems. **23 Gang of Four (GoF) patterns** organized into 3 categories: Creational, Structural, and Behavioral.

---

## CREATIONAL PATTERNS (Object Creation)

### 1. Singleton

**Ensures only one instance of a class exists throughout application lifetime**

```java
// Eager initialization (thread-safe, simple, but loads even if unused)
public class Database {
    private static final Database instance = new Database();
    
    private Database() {
        System.out.println("Database initialized");
    }
    
    public static Database getInstance() {
        return instance;
    }
    
    public void connect() { System.out.println("Connected"); }
}

// Lazy initialization with double-checked locking (best practice)
public class Logger {
    private static volatile Logger instance;
    
    private Logger() { }
    
    public static Logger getInstance() {
        if (instance == null) {
            synchronized (Logger.class) {
                if (instance == null) {
                    instance = new Logger();
                }
            }
        }
        return instance;
    }
    
    public void log(String message) { 
        System.out.println("[LOG] " + message); 
    }
}

// Bill Pugh Singleton (ClassLoader synchronization - most recommended)
public class ConfigManager {
    private ConfigManager() { }
    
    static class SingletonHolder {
        static final ConfigManager instance = new ConfigManager();
    }
    
    public static ConfigManager getInstance() {
        return SingletonHolder.instance;
    }
}

// Usage
Logger logger1 = Logger.getInstance();
Logger logger2 = Logger.getInstance();
System.out.println(logger1 == logger2);  // true
```

**Use cases**: Database connection pools, Logger, Configuration manager, Thread-safe counters
**Pros**: Guaranteed single instance, controlled access, lazy loading possible
**Cons**: Hard to test, harder multithreading, singleton scope issues

---

### 2. Factory Method

**Creates objects without client specifying exact classes - abstracts instantiation**

```java
// Product interface
interface DatabaseDriver {
    void connect();
    void query(String sql);
}

// Concrete products
class MySQLDriver implements DatabaseDriver {
    public void connect() { System.out.println("MySQL connected"); }
    public void query(String sql) { System.out.println("MySQL: " + sql); }
}

class PostgreSQLDriver implements DatabaseDriver {
    public void connect() { System.out.println("PostgreSQL connected"); }
    public void query(String sql) { System.out.println("PostgreSQL: " + sql); }
}

// Factory method in abstract creator
abstract class DatabaseFactory {
    // Factory method - subclasses decide implementation
    abstract DatabaseDriver createDriver();
    
    public void setupConnection() {
        DatabaseDriver driver = createDriver();
        driver.connect();
    }
}

class MySQLFactory extends DatabaseFactory {
    @Override
    DatabaseDriver createDriver() {
        return new MySQLDriver();
    }
}

class PostgreSQLFactory extends DatabaseFactory {
    @Override
    DatabaseDriver createDriver() {
        return new PostgreSQLDriver();
    }
}

// Usage
DatabaseFactory factory = new MySQLFactory();
factory.setupConnection();
```

**Use cases**: Frameworks creating different implementations, DB drivers, UI components
**Pros**: Loose coupling, easy to extend with new types
**Cons**: More classes, slightly more complex

---

### 3. Abstract Factory

**Creates families of related objects without specifying concrete classes**

```java
// Abstract products
interface Button { void click(); }
interface Checkbox { void toggle(); }

// Concrete products for Windows
class WindowsButton implements Button {
    public void click() { System.out.println("Windows Button clicked"); }
}

class WindowsCheckbox implements Checkbox {
    public void toggle() { System.out.println("Windows Checkbox toggled"); }
}

// Concrete products for Mac
class MacButton implements Button {
    public void click() { System.out.println("Mac Button clicked"); }
}

class MacCheckbox implements Checkbox {
    public void toggle() { System.out.println("Mac Checkbox toggled"); }
}

// Abstract factory
interface UIComponentFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete factories
class WindowsFactory implements UIComponentFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

class MacFactory implements UIComponentFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

// Client - doesn't depend on concrete classes
class Application {
    private Button button;
    private Checkbox checkbox;
    
    public Application(UIComponentFactory factory) {
        this.button = factory.createButton();
        this.checkbox = factory.createCheckbox();
    }
    
    public void render() {
        button.click();
        checkbox.toggle();
    }
}

// Usage
UIComponentFactory factory = new WindowsFactory();
Application app = new Application(factory);
app.render();
```

**Use cases**: Multi-platform UIs, database abstraction layers, theme systems
**Pros**: Ensures consistency across product families, easy to switch implementations
**Cons**: More complex, hard to extend with new product types

---

### 4. Builder

**Constructs complex objects step by step with fluent, readable interface**

```java
class Pizza {
    private String dough;
    private String sauce;
    private String cheese;
    private String topping;
    
    private Pizza(Builder builder) {
        this.dough = builder.dough;
        this.sauce = builder.sauce;
        this.cheese = builder.cheese;
        this.topping = builder.topping;
    }
    
    @Override
    public String toString() {
        return String.format("Pizza{dough=%s, sauce=%s, cheese=%s, topping=%s}", 
            dough, sauce, cheese, topping);
    }
    
    static class Builder {
        private String dough;
        private String sauce = "Tomato";  // Default values
        private String cheese = "Mozzarella";
        private String topping = "None";
        
        Builder setDough(String dough) {
            this.dough = dough;
            return this;  // Method chaining
        }
        
        Builder setSauce(String sauce) {
            this.sauce = sauce;
            return this;
        }
        
        Builder setCheese(String cheese) {
            this.cheese = cheese;
            return this;
        }
        
        Builder setTopping(String topping) {
            this.topping = topping;
            return this;
        }
        
        Pizza build() {
            if (dough == null) throw new IllegalStateException("Dough required");
            return new Pizza(this);
        }
    }
}

// Usage - readable and flexible
Pizza pizza = new Pizza.Builder()
    .setDough("Thin Crust")
    .setSauce("BBQ")
    .setTopping("Pepperoni")
    .build();
```

**Use cases**: Complex objects with many optional parameters, configuration objects, HTTP requests
**Pros**: Readable, handles optional fields elegantly, immutable objects
**Cons**: More code, overkill for simple objects

---

### 5. Prototype

**Creates new objects by cloning existing object - avoids expensive creation**

```java
interface CloneableShape {
    CloneableShape clone();
}

class Shape implements CloneableShape {
    private String color;
    private String type;
    private int size;
    
    public Shape(String color, String type, int size) {
        System.out.println("Creating shape... (expensive operation)");
        this.color = color;
        this.type = type;
        this.size = size;
    }
    
    @Override
    public CloneableShape clone() {
        System.out.println("Cloning shape (fast operation)");
        return new Shape(this.color, this.type, this.size);
    }
    
    @Override
    public String toString() {
        return String.format("Shape{%s, %s, size=%d}", color, type, size);
    }
}

// Usage
CloneableShape original = new Shape("Red", "Circle", 10);  // Expensive
CloneableShape clone1 = original.clone();  // Fast
CloneableShape clone2 = original.clone();  // Fast
```

**Use cases**: Expensive object creation, undo functionality, game object spawning
**Pros**: Faster than creating new objects, useful for large objects
**Cons**: Shallow/deep copy issues, complexity for circular references

---

## STRUCTURAL PATTERNS (Object Composition)

### 6. Adapter (Wrapper)

**Converts interface of one class into another - bridges incompatible interfaces**

```java
// Old interface
interface OldPaymentProcessor {
    void process(int amount);
}

// New interface
interface ModernPaymentGateway {
    void performTransaction(double amount, String currency);
}

// New implementation
class StripeGateway implements ModernPaymentGateway {
    public void performTransaction(double amount, String currency) {
        System.out.println(String.format("Processing %.2f %s via Stripe", amount, currency));
    }
}

// Adapter - bridges old and new
class PaymentAdapter implements OldPaymentProcessor {
    private ModernPaymentGateway gateway;
    
    public PaymentAdapter(ModernPaymentGateway gateway) {
        this.gateway = gateway;
    }
    
    @Override
    public void process(int amount) {
        gateway.performTransaction((double) amount, "USD");
    }
}

// Usage
OldPaymentProcessor processor = new PaymentAdapter(new StripeGateway());
processor.process(100);
```

**Use cases**: Legacy code integration, incompatible interfaces, third-party library integration
**Pros**: Reusable with incompatible code, separation of concerns
**Cons**: Extra layer of abstraction, more classes

---

### 7. Bridge

**Separates abstraction from implementation - allows independent variation**

```java
// Implementation interface
interface DrawingAPI {
    void drawCircle(double x, double y, double radius);
}

class WindowsDrawingAPI implements DrawingAPI {
    public void drawCircle(double x, double y, double radius) {
        System.out.println(String.format("Windows: Drawing circle at (%.0f, %.0f) with radius %.0f", x, y, radius));
    }
}

// Abstraction - separated from implementation
abstract class Shape {
    protected DrawingAPI drawingAPI;
    
    public Shape(DrawingAPI drawingAPI) {
        this.drawingAPI = drawingAPI;
    }
    
    abstract void draw();
}

class Circle extends Shape {
    private double x, y, radius;
    
    public Circle(DrawingAPI api, double x, double y, double radius) {
        super(api);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }
    
    @Override
    void draw() {
        drawingAPI.drawCircle(x, y, radius);
    }
}

// Usage
DrawingAPI api = new WindowsDrawingAPI();
Shape circle = new Circle(api, 10, 20, 5);
circle.draw();
```

**Use cases**: Cross-platform abstraction, driver-based systems
**Pros**: Abstraction and implementation vary independently
**Cons**: Adds complexity, overkill for simple hierarchies

---

### 8. Composite

**Composes objects into tree structures to represent part-whole hierarchies**

```java
interface FileSystemComponent {
    void display(String indent);
    int getSize();
}

class File implements FileSystemComponent {
    private String name;
    private int size;
    
    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }
    
    @Override
    public void display(String indent) {
        System.out.println(indent + "File: " + name + " (" + size + "B)");
    }
    
    @Override
    public int getSize() {
        return size;
    }
}

class Folder implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> components = new ArrayList<>();
    
    public Folder(String name) {
        this.name = name;
    }
    
    public void add(FileSystemComponent component) {
        components.add(component);
    }
    
    @Override
    public void display(String indent) {
        System.out.println(indent + "Folder: " + name);
        for (FileSystemComponent component : components) {
            component.display(indent + "  ");
        }
    }
    
    @Override
    public int getSize() {
        return components.stream().mapToInt(FileSystemComponent::getSize).sum();
    }
}

// Usage
Folder root = new Folder("root");
root.add(new File("photo.jpg", 200));
root.display("");
```

**Use cases**: File systems, GUI component hierarchies, organizational structures
**Pros**: Treats individual and composite objects uniformly
**Cons**: Can overgeneralize, performance impact with deep trees

---

### 9. Decorator

**Adds new functionality to objects dynamically at runtime**

```java
interface Coffee {
    double cost();
    String description();
}

class SimpleCoffee implements Coffee {
    public double cost() { return 5.0; }
    public String description() { return "Simple Coffee"; }
}

abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;
    
    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
}

class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) { super(coffee); }
    
    public double cost() { return coffee.cost() + 1.5; }
    public String description() { return coffee.description() + ", Milk"; }
}

class VanillaDecorator extends CoffeeDecorator {
    public VanillaDecorator(Coffee coffee) { super(coffee); }
    
    public double cost() { return coffee.cost() + 2.0; }
    public String description() { return coffee.description() + ", Vanilla"; }
}

// Usage - stack decorators
Coffee coffee = new SimpleCoffee();  // 5.0
coffee = new MilkDecorator(coffee);  // 6.5
coffee = new VanillaDecorator(coffee); // 8.5

System.out.println(coffee.description() + " = $" + coffee.cost());
```

**Use cases**: UI components with variations, I/O stream wrapping, caching, logging
**Pros**: Flexible feature addition, avoids class explosion
**Cons**: Order matters, debugging complex chains

---

### 10. Facade

**Provides unified, simplified interface to complex subsystem**

```java
// Complex subsystem
class HomeTheaterInput { void activateInput(String input) { System.out.println("Input: " + input); } }
class Projector { void on() { System.out.println("Projector on"); } }
class Speaker { void on() { System.out.println("Speaker on"); } void setVolume(int v) { System.out.println("Volume: " + v); } }
class Lights { void dim(int level) { System.out.println("Lights: " + level + "%"); } }

// Facade - simplifies
class HomeTheaterFacade {
    private HomeTheaterInput input;
    private Projector projector;
    private Speaker speaker;
    private Lights lights;
    
    public HomeTheaterFacade(HomeTheaterInput in, Projector p, Speaker s, Lights l) {
        this.input = in;
        this.projector = p;
        this.speaker = s;
        this.lights = l;
    }
    
    public void watchMovie() {
        input.activateInput("HDMI");
        projector.on();
        speaker.on();
        speaker.setVolume(10);
        lights.dim(10);
    }
}

// Usage - simple interface
HomeTheaterFacade theater = new HomeTheaterFacade(
    new HomeTheaterInput(), new Projector(), new Speaker(), new Lights()
);
theater.watchMovie();
```

**Use cases**: Complex library APIs, system initialization, multi-step processes
**Pros**: Simplifies client code, hides complexity
**Cons**: Can become bloated, may hide important details

---

### 11. Flyweight

**Shares common state between objects to reduce memory usage**

```java
// Shared immutable state
class CharacterFont {
    private final String font;
    private final int size;
    
    public CharacterFont(String font, int size) {
        this.font = font;
        this.size = size;
    }
    
    public void display(int x, int y, char c) {
        System.out.println(String.format("'%c' at (%d,%d) in %s size %d", c, x, y, font, size));
    }
}

// Flyweight factory
class CharacterFontFactory {
    private static Map<String, CharacterFont> fonts = new HashMap<>();
    
    public static CharacterFont getFont(String font, int size) {
        String key = font + ":" + size;
        if (!fonts.containsKey(key)) {
            fonts.put(key, new CharacterFont(font, size));
        }
        return fonts.get(key);
    }
}

// Usage
CharacterFont font1 = CharacterFontFactory.getFont("Arial", 12);
CharacterFont font2 = CharacterFontFactory.getFont("Arial", 12);
System.out.println(font1 == font2);  // true
```

**Use cases**: Text editors, caching, game sprite management
**Pros**: Massive memory savings for many similar objects
**Cons**: Thread-safety, complexity, lookup overhead

---

### 12. Proxy

**Provides surrogate/placeholder for another object to control access**

```java
interface Image {
    void display();
}

class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        System.out.println("Loading image: " + filename);
        this.filename = filename;
    }
    
    public void display() {
        System.out.println("Displaying: " + filename);
    }
}

// Proxy - delays expensive operation
class ImageProxy implements Image {
    private String filename;
    private RealImage realImage;
    
    public ImageProxy(String filename) {
        this.filename = filename;
    }
    
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}

// Usage
Image image = new ImageProxy("large.jpg");
image.display();  // NOW loads
```

**Use cases**: Lazy loading, access control, remote objects, caching
**Pros**: Lazy initialization, access control, remote proxy
**Cons**: Extra layer, slight performance overhead

---

## BEHAVIORAL PATTERNS (Object Interaction & Responsibility)

### 13. Chain of Responsibility

**Passes request along chain of handlers until one processes it**

```java
abstract class SupportHandler {
    protected SupportHandler nextHandler;
    protected int level;
    
    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }
    
    public void handle(SupportRequest request) {
        if (request.getLevel() == level) {
            resolve(request);
        } else if (nextHandler != null) {
            nextHandler.handle(request);
        } else {
            System.out.println("Not handled");
        }
    }
    
    protected abstract void resolve(SupportRequest request);
}

class Level1Support extends SupportHandler {
    public Level1Support() { this.level = 1; }
    protected void resolve(SupportRequest request) {
        System.out.println("Level 1: " + request.getIssue());
    }
}

class SupportRequest {
    private String issue;
    private int level;
    
    public SupportRequest(String issue, int level) {
        this.issue = issue;
        this.level = level;
    }
    
    public String getIssue() { return issue; }
    public int getLevel() { return level; }
}

// Usage
SupportHandler h1 = new Level1Support();
h1.setNextHandler(new Level1Support());
h1.handle(new SupportRequest("Issue", 1));
```

**Use cases**: Event handling, logging with levels, approval workflows
**Pros**: Decouples handlers, dynamic chain building
**Cons**: Request not guaranteed to be handled, harder debugging

---

### 14. Command

**Encapsulates request as object - allows parameterization and queuing**

```java
interface Command {
    void execute();
    void undo();
}

class TextEditor {
    private StringBuilder text = new StringBuilder();
    
    public void insert(String str) { text.append(str); }
    public void delete(int count) { text.delete(text.length() - count, text.length()); }
    public String getText() { return text.toString(); }
}

class InsertCommand implements Command {
    private TextEditor editor;
    private String text;
    
    public InsertCommand(TextEditor editor, String text) {
        this.editor = editor;
        this.text = text;
    }
    
    public void execute() { editor.insert(text); }
    public void undo() { editor.delete(text.length()); }
}

// Usage
TextEditor editor = new TextEditor();
Command cmd = new InsertCommand(editor, "Hello");
cmd.execute();
cmd.undo();
```

**Use cases**: Undo/redo, macro recording, transaction management
**Pros**: Decouples invoker from receiver, enables queuing and logging
**Cons**: Complexity for many command variations

---

### 15. Iterator

**Accesses elements of collection sequentially without exposing structure**

```java
interface CustomIterator {
    boolean hasNext();
    Object next();
}

class MyCollection {
    private int[] data;
    
    public MyCollection(int[] data) {
        this.data = data;
    }
    
    public CustomIterator getIterator() {
        return new MyIterator();
    }
    
    private class MyIterator implements CustomIterator {
        private int index = 0;
        
        public boolean hasNext() { return index < data.length; }
        public Object next() { return data[index++]; }
    }
}

// Usage
MyCollection col = new MyCollection(new int[]{1, 2, 3});
CustomIterator iter = col.getIterator();
while (iter.hasNext()) {
    System.out.println(iter.next());
}
```

**Use cases**: List traversal, tree traversal, filtering collections
**Pros**: Encapsulates internal structure, multiple iterators possible
**Cons**: Java has built-in Iterator (this is more educational)

---

### 16. Mediator

**Centralizes complex communication between objects - reduces coupling**

```java
interface ChatMediator {
    void sendMessage(String msg, User user);
}

class ConcreteChatMediator implements ChatMediator {
    private List<User> users = new ArrayList<>();
    
    public void addUser(User user) {
        users.add(user);
        user.setMediator(this);
    }
    
    public void sendMessage(String msg, User sender) {
        System.out.println(sender.getName() + ": " + msg);
        for (User user : users) {
            if (user != sender) {
                user.receive(msg);
            }
        }
    }
}

class User {
    private String name;
    private ChatMediator mediator;
    
    public User(String name) { this.name = name; }
    
    public void setMediator(ChatMediator m) { this.mediator = m; }
    
    public void send(String msg) { mediator.sendMessage(msg, this); }
    public void receive(String msg) { System.out.println(getName() + " got: " + msg); }
    public String getName() { return name; }
}

// Usage
ChatMediator med = new ConcreteChatMediator();
User u1 = new User("Alice");
User u2 = new User("Bob");
med.addUser(u1);
med.addUser(u2);
u1.send("Hi!");
```

**Use cases**: Chat systems, dialog boxes, air traffic control
**Pros**: Reduces object coupling, centralizes communication logic
**Cons**: Mediator can become complex

---

### 17. Memento

**Captures and externalizes object state without violating encapsulation**

```java
class EditorMemento {
    private final String content;
    
    public EditorMemento(String content) {
        this.content = content;
    }
    
    public String getContent() { return content; }
}

class TextDocument {
    private String content = "";
    
    public void write(String text) { content += text; }
    
    public EditorMemento save() { return new EditorMemento(content); }
    
    public void restore(EditorMemento m) { content = m.getContent(); }
    
    public String getContent() { return content; }
}

class DocumentHistory {
    private Stack<EditorMemento> history = new Stack<>();
    
    public void save(TextDocument doc) { history.push(doc.save()); }
    
    public void undo(TextDocument doc) {
        if (!history.isEmpty()) {
            doc.restore(history.pop());
        }
    }
}

// Usage
TextDocument doc = new TextDocument();
DocumentHistory hist = new DocumentHistory();
doc.write("Hello ");
hist.save(doc);
doc.write("World");
hist.undo(doc);
```

**Use cases**: Undo/redo, save points in games, transaction rollback
**Pros**: Encapsulation preserved, easy undo/redo
**Cons**: Memory overhead for storing states

---

### 18. Observer

**One-to-many dependency: when one object changes, notify all dependents automatically**

```java
interface Observer {
    void update(String event);
}

class EventListener implements Observer {
    private String name;
    
    public EventListener(String name) { this.name = name; }
    
    public void update(String event) {
        System.out.println(name + " got: " + event);
    }
}

class EventSource {
    private List<Observer> observers = new ArrayList<>();
    
    public void attach(Observer obs) { observers.add(obs); }
    public void detach(Observer obs) { observers.remove(obs); }
    
    public void notifyObservers(String event) {
        for (Observer obs : observers) {
            obs.update(event);
        }
    }
}

// Usage
EventSource src = new EventSource();
src.attach(new EventListener("L1"));
src.attach(new EventListener("L2"));
src.notifyObservers("Click");
```

**Use cases**: Event listeners, MVC pattern, publish-subscribe systems
**Pros**: Loose coupling, dynamic subscriptions
**Cons**: Observer removal tricky, execution order uncertain

---

### 19. State

**Object changes behavior when internal state changes - appears to change class**

```java
interface State {
    void handle(Context ctx);
}

class StartState implements State {
    public void handle(Context ctx) {
        System.out.println("Starting...");
        ctx.setState(new RunningState());
    }
}

class RunningState implements State {
    public void handle(Context ctx) {
        System.out.println("Running...");
        ctx.setState(new StoppedState());
    }
}

class StoppedState implements State {
    public void handle(Context ctx) {
        System.out.println("Stopped");
    }
}

class Context {
    private State state;
    
    public Context(State initial) { this.state = initial; }
    
    public void setState(State s) { this.state = s; }
    
    public void request() { state.handle(this); }
}

// Usage
Context ctx = new Context(new StartState());
ctx.request();  // Starting
ctx.request();  // Running
ctx.request();  // Stopped
```

**Use cases**: State machines, TCP connection states, workflow states
**Pros**: Encapsulates state-specific behavior, eliminates large conditionals
**Cons**: Can increase number of classes

---

### 20. Strategy

**Family of interchangeable algorithms - select at runtime**

```java
interface PaymentStrategy {
    void pay(double amount);
}

class CreditCardStrategy implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Credit Card: $" + amount);
    }
}

class PayPalStrategy implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("PayPal: $" + amount);
    }
}

class ShoppingCart {
    private PaymentStrategy strategy;
    private double amount;
    
    public ShoppingCart(double amount) { this.amount = amount; }
    
    public void setStrategy(PaymentStrategy s) { this.strategy = s; }
    
    public void checkout() {
        if (strategy == null) throw new IllegalStateException("Set strategy first");
        strategy.pay(amount);
    }
}

// Usage
ShoppingCart cart = new ShoppingCart(99.99);
cart.setStrategy(new CreditCardStrategy());
cart.checkout();
cart.setStrategy(new PayPalStrategy());
cart.checkout();
```

**Use cases**: Payment methods, sorting algorithms, compression algorithms
**Pros**: Runtime algorithm selection, easy to test
**Cons**: Overkill for simple if-else logic

---

### 21. Template Method

**Defines algorithm skeleton in base class, subclasses provide specific steps**

```java
abstract class DataProcessor {
    public final void process() {
        readData();
        validateData();
        transformData();
        saveData();
    }
    
    protected abstract void readData();
    protected abstract void validateData();
    protected abstract void transformData();
    protected abstract void saveData();
}

class CSVProcessor extends DataProcessor {
    protected void readData() { System.out.println("Reading CSV"); }
    protected void validateData() { System.out.println("Validating CSV"); }
    protected void transformData() { System.out.println("Transform CSV"); }
    protected void saveData() { System.out.println("Save to DB"); }
}

// Usage
DataProcessor proc = new CSVProcessor();
proc.process();
```

**Use cases**: Framework templates, batch processing, workflows
**Pros**: Defines algorithm structure, code reuse
**Cons**: Inversion of control, tight coupling

---

### 22. Visitor

**Separates algorithm from object structure - add new operations without changing objects**

```java
interface ComputerPart {
    void accept(ComputerPartVisitor v);
}

class Computer implements ComputerPart {
    public void accept(ComputerPartVisitor v) { v.visit(this); }
}

class Mouse implements ComputerPart {
    public void accept(ComputerPartVisitor v) { v.visit(this); }
}

interface ComputerPartVisitor {
    void visit(Computer c);
    void visit(Mouse m);
}

class DisplayVisitor implements ComputerPartVisitor {
    public void visit(Computer c) { System.out.println("Display Computer"); }
    public void visit(Mouse m) { System.out.println("Display Mouse"); }
}

// Usage
ComputerPart part = new Computer();
part.accept(new DisplayVisitor());
```

**Use cases**: AST traversal, report generation, element processing
**Pros**: Add new operations easily, separates logic from structure
**Cons**: Complex implementation, hard to extend element types

---

### 23. Interpreter

**Defines grammar and interprets sentences in that grammar**

```java
interface Expression {
    boolean evaluate(String context);
}

class TerminalExpression implements Expression {
    private String data;
    
    public TerminalExpression(String data) { this.data = data; }
    
    public boolean evaluate(String context) {
        return context.contains(data);
    }
}

class OrExpression implements Expression {
    private Expression e1, e2;
    
    public OrExpression(Expression e1, Expression e2) {
        this.e1 = e1;
        this.e2 = e2;
    }
    
    public boolean evaluate(String context) {
        return e1.evaluate(context) || e2.evaluate(context);
    }
}

// Usage
Expression rule = new OrExpression(
    new TerminalExpression("male"),
    new TerminalExpression("female")
);
System.out.println(rule.evaluate("male"));  // true
```

**Use cases**: SQL parsing, regex interpretation, rule engines
**Pros**: Easy to extend grammar, separates grammar from logic
**Cons**: Complex grammar becomes inefficient

---

## PATTERN SUMMARY TABLE

| Pattern | Type | Purpose | Complexity |
|---------|------|---------|-----------|
| Singleton | Creational | Single instance | Low |
| Factory Method | Creational | Object creation | Low-Med |
| Abstract Factory | Creational | Family of objects | Medium |
| Builder | Creational | Complex construction | Medium |
| Prototype | Creational | Object cloning | Low |
| Adapter | Structural | Interface conversion | Low-Med |
| Bridge | Structural | Abstraction separation | Medium |
| Composite | Structural | Part-whole hierarchy | Medium |
| Decorator | Structural | Dynamic features | Low-Med |
| Facade | Structural | Simplified interface | Low |
| Flyweight | Structural | Shared state | High |
| Proxy | Structural | Controlled access | Low-Med |
| Chain of Resp. | Behavioral | Sequential handling | Medium |
| Command | Behavioral | Request encapsulation | Medium |
| Iterator | Behavioral | Sequential access | Low |
| Mediator | Behavioral | Centralized communication | Medium |
| Memento | Behavioral | State capture | Medium |
| Observer | Behavioral | Event notification | Low-Med |
| State | Behavioral | Behavior change | Medium |
| Strategy | Behavioral | Algorithm selection | Low |
| Template Method | Behavioral | Algorithm skeleton | Low-Med |
| Visitor | Behavioral | Operation separation | High |
| Interpreter | Behavioral | Grammar interpretation | High |

---

## KEY TAKEAWAYS

✓ **Choose patterns wisely** - Don't overuse (YAGNI principle)
✓ **Combine patterns** - Multiple patterns work together effectively
✓ **Know trade-offs** - Patterns add complexity and slight performance overhead
✓ **Recognize patterns** - In existing code, frameworks, and libraries
✓ **Practice implementation** - Build patterns from scratch to understand deeply

**Next:** [13. SOLID Principles](13-SOLID-Principles.md)
