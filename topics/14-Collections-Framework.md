# 14. COLLECTIONS FRAMEWORK

## Collection Hierarchy

```
Collection (I)
├── List (I)
│   ├── ArrayList
│   ├── LinkedList
│   └── Vector
├── Set (I)
│   ├── HashSet
│   ├── LinkedHashSet
│   └── TreeSet
└── Queue (I)
    ├── PriorityQueue
    └── Deque (I)
        └── ArrayDeque

Map (I)
├── HashMap
├── LinkedHashMap
└── TreeMap
```

---

## List Interface

### ArrayList - Internal Implementation

**How it works:**
- Uses dynamic array internally
- Backed by `Object[] elementData`
- Grows automatically when capacity exceeded

```java
// How ArrayList grows internally
class ArrayList<E> {
    private Object[] elementData;  // Internal array
    private int size;              // Actual elements
    
    public void add(E element) {
        if (size == elementData.length) {
            // Array full - need to grow
            Object[] newArray = new Object[elementData.length * 1.5];  // 1.5x growth
            System.arraycopy(elementData, 0, newArray, 0, elementData.length);
            elementData = newArray;
        }
        elementData[size++] = element;
    }
    
    public E get(int index) {
        return (E) elementData[index];  // O(1) direct access
    }
}

// Usage
List<String> list = new ArrayList<>();
list.add("A");  // elementData = [null, null, ...]
list.add("B");  // elementData = ["A", "B", null, ...]
```

**Features:**
- **Random access**: O(1) - can jump to any index
- **Insertion/Deletion**: O(n) - need to shift elements
- **Memory**: Contiguous, cache-friendly
- **Growth**: Increases by ~50% when full

### LinkedList - Internal Implementation

**How it works:**
- Uses doubly-linked list
- Each node has data + references to prev/next

```java
// How LinkedList stores data
class LinkedList<E> {
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;
        
        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
    
    private Node<E> first;
    private Node<E> last;
    
    public void add(E element) {
        Node<E> node = new Node<>(last, element, null);
        if (first == null) {
            first = node;
        } else {
            last.next = node;
        }
        last = node;  // O(1) append
    }
    
    public E get(int index) {
        // Must traverse from first or last
        Node<E> x = node(index);
        return x.item;  // O(n) random access
    }
}

// Usage
LinkedList<String> list = new LinkedList<>();
list.add("A");  // first → ["A"] ← last
list.add("B");  // first → ["A"] ↔ ["B"] ← last
list.addFirst("Z");  // Efficient: O(1)
```

**Features:**
- **Insert/Delete at ends**: O(1) - just update pointers
- **Random access**: O(n) - must traverse
- **Memory**: Non-contiguous, scattered
- **Overhead**: Extra memory for prev/next pointers

---

## Set Interface

### HashSet - Internal Implementation

**How it works:**
- Uses HashMap internally
- Stores elements as HashMap keys with dummy value
- Hash function determines bucket location

```java
// How HashSet is implemented
class HashSet<E> {
    private HashMap<E, Object> map;  // Backing store
    private static final Object PRESENT = new Object();
    
    public HashSet() {
        map = new HashMap<>();
    }
    
    public boolean add(E e) {
        return map.put(e, PRESENT) == null;  // O(1) average
    }
    
    public boolean contains(Object o) {
        return map.containsKey(o);  // O(1) average
    }
}

// Hash collision resolution (chaining)
// Bucket array: [head1] → node → node
//               [head2] → node
//               [head3] (empty)
// If hash("A") == hash("B"), they chain in same bucket

Set<Integer> set = new HashSet<>();
set.add(1);  // hash(1) % 16 → bucket 1
set.add(1);  // Duplicate check - returns false
set.add(2);  // hash(2) % 16 → bucket 2
```

**Features:**
- **Add/Remove/Contains**: O(1) average case
- **Load Factor**: Resizes when 75% full
- **Hash Collisions**: Handled by chaining (linked list in bucket)
- **Unordered**: Depends on hash values

### TreeSet - Internal Implementation

**How it works:**
- Backed by TreeMap (which uses Red-Black tree)
- Elements stored in sorted order using TreeNode

```java
// How TreeSet stores data
class TreeSet<E> {
    private TreeMap<E, Object> m;  // Red-Black tree
    private static final Object PRESENT = new Object();
    
    public TreeSet() {
        m = new TreeMap<>();
    }
    
    public boolean add(E e) {
        return m.put(e, PRESENT) == null;  // O(log n)
    }
    
    public Iterator<E> iterator() {
        return m.keySet().iterator();  // Returns sorted order
    }
}

// Red-Black Tree structure
//        [5]
//       /   \
//     [3]   [7]
//     / \   / \
//   [1][4][6][8]
// Always maintains sorted order

TreeSet<Integer> set = new TreeSet<>();
set.add(5);  // O(log n) insertion
set.add(1);  // Rebalances tree
set.add(3);  // Always sorted internally
// Iteration: 1, 3, 5 (sorted!)
```

**Features:**
- **Add/Remove/Contains**: O(log n)
- **Sorted**: Always in ascending order
- **Self-balancing**: Red-Black tree maintains balance
- **No null**: Comparator needs to handle ordering

---

## Map Interface

### HashMap - Internal Implementation

**How it works:**
- Uses hash table (array of linked lists/trees)
- Distributes keys across buckets using hash function
- Handles collisions with chaining

```java
// How HashMap is implemented
class HashMap<K, V> {
    private Node<K, V>[] table;  // Bucket array
    private int size;
    
    static class Node<K, V> {
        final K key;
        V value;
        Node<K, V> next;  // For collision chaining
        
        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }
    
    public V put(K key, V value) {
        int hash = hash(key);
        int index = hash % table.length;  // Which bucket?
        
        // Traverse bucket for existing key
        Node<K, V> node = table[index];
        while (node != null) {
            if (node.key.equals(key)) {
                V old = node.value;
                node.value = value;  // Update
                return old;
            }
            node = node.next;
        }
        
        // Not found - add new
        Node<K, V> newNode = new Node<>(key, value);
        newNode.next = table[index];  // Insert at head
        table[index] = newNode;
        return null;
    }
    
    public V get(K key) {
        int hash = hash(key);
        int index = hash % table.length;
        
        Node<K, V> node = table[index];
        while (node != null) {
            if (node.key.equals(key)) {
                return node.value;  // Found!
            }
            node = node.next;
        }
        return null;  // Not found
    }
}

// Visual representation:
// table = [bucket0] → (K1,V1) → (K2,V2)
//         [bucket1] → (K3,V3)
//         [bucket2] (empty)

Map<String, Integer> map = new HashMap<>();
map.put("A", 1);  // hash("A") % 16 → bucket 5
map.put("B", 2);  // hash("B") % 16 → bucket 12
map.get("A");     // O(1) - direct bucket access
```

**Features:**
- **Put/Get/Remove**: O(1) average case (O(n) worst)
- **Resizing**: When load factor > 0.75, doubles size
- **Collisions**: Handled by chaining or tree (Java 8+)
- **Hash collisions**: If multiple keys hash to same bucket

### TreeMap - Internal Implementation

**How it works:**
- Uses Red-Black tree (self-balancing BST)
- Keys stored in sorted order
- No hash function needed - uses Comparable

```java
// How TreeMap works
class TreeMap<K, V> {
    private TreeNode<K, V> root;
    private Comparator<? super K> comparator;
    
    static class TreeNode<K, V> {
        K key;
        V value;
        TreeNode<K, V> left, right, parent;
        boolean red = true;
    }
    
    public V put(K key, V value) {
        // Navigate tree using comparator
        if (root == null) {
            root = new TreeNode<>(key, value);
            return null;
        }
        
        // Find correct position and insert
        TreeNode<K, V> node = root;
        while (true) {
            int cmp = compare(key, node.key);
            if (cmp < 0) {
                if (node.left == null) {
                    node.left = new TreeNode<>(key, value);
                    return null;
                }
                node = node.left;
            } else {
                if (node.right == null) {
                    node.right = new TreeNode<>(key, value);
                    return null;
                }
                node = node.right;
            }
        }
    }
    
    public V get(K key) {
        // Binary search in tree
        TreeNode<K, V> node = root;
        while (node != null) {
            int cmp = compare(key, node.key);
            if (cmp < 0) node = node.left;
            else if (cmp > 0) node = node.right;
            else return node.value;  // Found!
        }
        return null;
    }
}

// Tree structure (always sorted):
//        [M]
//       /   \
//     [D]   [S]
//    /  \   /  \
//  [A][G][O][X]
// In-order traversal: A, D, G, M, O, S, X (sorted!)

TreeMap<String, Integer> map = new TreeMap<>();
map.put("C", 3);  // O(log n) - tree insertion
map.put("A", 1);  // O(log n) - rebalances
map.put("B", 2);  // O(log n)
// Always sorted by key: A→1, B→2, C→3
```

**Features:**
- **Put/Get/Remove**: O(log n)
- **Sorted**: Always maintains key order
- **Red-Black Tree**: Self-balancing for consistent O(log n)
- **Range queries**: Can get subMap(k1, k2)

---

## Queue Interface

### PriorityQueue - Heap Implementation

**How it works:**
- Uses min-heap (binary heap) internally
- Parent ≤ Children always
- Complete binary tree in array

```java
// How PriorityQueue is implemented
class PriorityQueue<E> {
    private Object[] queue;  // Heap array
    private int size;
    
    public void add(E element) {
        // 1. Add to end
        queue[size] = element;
        size++;
        
        // 2. Bubble up to maintain heap property
        siftUp(size - 1);  // O(log n)
    }
    
    private void siftUp(int k) {
        while (k > 0) {
            int parent = (k - 1) / 2;
            if (queue[parent] <= queue[k]) break;
            
            // Swap with parent
            swap(k, parent);
            k = parent;
        }
    }
    
    public E poll() {
        E min = queue[0];  // Root is minimum
        
        // 1. Move last element to root
        queue[0] = queue[size - 1];
        size--;
        
        // 2. Bubble down to restore heap
        siftDown(0);  // O(log n)
        
        return min;
    }
    
    private void siftDown(int k) {
        while (2 * k + 1 < size) {
            int left = 2 * k + 1;
            int right = 2 * k + 2;
            int smallest = k;
            
            if (queue[left] < queue[smallest]) smallest = left;
            if (right < size && queue[right] < queue[smallest]) smallest = right;
            
            if (smallest == k) break;
            swap(k, smallest);
            k = smallest;
        }
    }
}

// Heap visualization (min-heap):
//           [1]
//         /     \
//       [2]     [3]
//      /  \    /
//    [5][6][9]
// Array: [1, 2, 3, 5, 6, 9]

PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.add(5);   // [5]
pq.add(1);   // Bubble up → [1, 5]
pq.add(3);   // [1, 5, 3]
pq.poll();   // Remove min (1) → [3, 5]
```

**Features:**
- **Add/Remove**: O(log n) - heap rebalancing
- **Peek min**: O(1)
- **Complete binary tree**: Efficient space usage
- **Min-heap by default**: Custom Comparator for max-heap

### ArrayDeque - Circular Array Implementation

**How it works:**
- Uses circular array (ring buffer)
- Head and tail pointers
- Efficient for both ends

```java
// How ArrayDeque works
class ArrayDeque<E> {
    private Object[] elements;
    private int head;  // Front pointer
    private int tail;  // Rear pointer
    
    public void addFirst(E e) {
        // Insert at head
        elements[head = (head - 1) & (elements.length - 1)] = e;
    }
    
    public void addLast(E e) {
        // Insert at tail
        elements[tail] = e;
        tail = (tail + 1) & (elements.length - 1);
    }
    
    public E removeFirst() {
        E result = (E) elements[head];
        elements[head] = null;
        head = (head + 1) & (elements.length - 1);
        return result;
    }
}

// Circular buffer visualization:
// Before: [A][B][C][null][null]
//         head=0        tail=3
// addLast(D): [A][B][C][D][null]
//             head=0        tail=4
// addFirst(Z): [A][B][C][D][Z]  (wraps around)
//              head=4, tail wraps

Deque<Integer> deque = new ArrayDeque<>();
deque.addFirst(1);  // O(1)
deque.addLast(2);   // O(1)
deque.removeFirst(); // O(1)
```

**Features:**
- **Add/Remove at both ends**: O(1)
- **Circular array**: No wasted space
- **Resizing**: Doubles when full
- **Better than LinkedList for Queue**: Less memory overhead

---

## Comparison Table

| Collection | Duplicates | Ordered | Sorted | Null | Performance |
|------------|------------|---------|--------|------|-------------|
| ArrayList | Yes | Insertion | No | Yes | O(1) access |
| LinkedList | Yes | Insertion | No | Yes | O(1) insert |
| HashSet | No | No | No | One null | O(1) add/remove |
| TreeSet | No | Sorted | Yes | No | O(log n) |
| HashMap | No (keys) | No | No | One null key | O(1) |
| TreeMap | No (keys) | Sorted | Yes | No | O(log n) |

---

## Common Operations

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);

// Iterate
for (Integer num : list) {
    System.out.println(num);
}

// forEach (Java 8+)
list.forEach(System.out::println);

// Sorting
Collections.sort(list);
Collections.reverse(list);

// Searching
int index = Collections.binarySearch(list, 3);

// Convert to array
Integer[] arr = list.toArray(new Integer[0]);

// Convert array to list
List<String> names = Arrays.asList("A", "B", "C");
```

---

## Iterator - Safe Collection Traversal

**What is Iterator?**
- Interface for traversing collections safely
- Provides hasNext(), next(), remove() methods
- Prevents ConcurrentModificationException

**How it works:**
```java
// Iterator interface
interface Iterator<E> {
    boolean hasNext();      // More elements?
    E next();              // Get next element
    void remove();         // Remove current element
}

// How ArrayList implements Iterator
class ArrayListIterator<E> implements Iterator<E> {
    private ArrayList<E> list;
    private int cursor = 0;
    private int expectedModCount;
    
    ArrayListIterator(ArrayList<E> list) {
        this.list = list;
        this.expectedModCount = list.modCount;  // Track modifications
    }
    
    public boolean hasNext() {
        return cursor < list.size();
    }
    
    public E next() {
        if (expectedModCount != list.modCount) {
            throw new ConcurrentModificationException();
        }
        return list.get(cursor++);
    }
    
    public void remove() {
        list.remove(cursor - 1);  // Safe removal
        expectedModCount++;  // Update to match new modCount
    }
}
```

**Iterator vs For Loop - Key Differences:**

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

// ❌ DANGEROUS - ConcurrentModificationException
for (String item : list) {
    if ("B".equals(item)) {
        list.remove(item);  // Modifying during iteration → CRASH!
    }
}

// ✅ SAFE - Using Iterator
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if ("B".equals(item)) {
        iterator.remove();  // Safe removal through iterator
    }
}
System.out.println(list);  // [A, C]

// Why Iterator is safe?
// - Iterator tracks modCount (number of modifications)
// - When modCount changes, iterator detects it
// - Throws ConcurrentModificationException if collection modified outside iterator
```

**Use Cases:**

```java
// 1. Safe removal during iteration
Iterator<Integer> iter = numbers.iterator();
while (iter.hasNext()) {
    if (iter.next() < 0) {
        iter.remove();  // Only safe way to remove during iteration
    }
}

// 2. Collection-agnostic traversal
void printAll(Iterator<String> iter) {
    while (iter.hasNext()) {
        System.out.println(iter.next());
    }
}
// Works with ArrayList, LinkedList, HashSet, etc.

// 3. ListIterator - bidirectional iteration
ListIterator<String> listIter = list.listIterator();
while (listIter.hasNext()) {
    listIter.next();
}
// Can now go backwards
while (listIter.hasPrevious()) {
    System.out.println(listIter.previous());
}
```

**Performance Comparison:**

```java
// LinkedList - performance difference
LinkedList<Integer> linkedList = new LinkedList<>();
for (int i = 0; i < 1000; i++) linkedList.add(i);

// ❌ SLOW: O(n²) - get() requires traversal each time
for (int i = 0; i < linkedList.size(); i++) {
    System.out.println(linkedList.get(i));  // O(n) × n times
}

// ✅ FAST: O(n) - iterator traverses once
Iterator<Integer> iter = linkedList.iterator();
while (iter.hasNext()) {
    System.out.println(iter.next());  // O(1) per element
}

// ✅ FAST: O(n) - enhanced for-loop uses iterator
for (Integer num : linkedList) {
    System.out.println(num);
}
```

---

## Comparable vs Comparator - Sorting Objects

**Comparable - Natural Ordering (built-in)**

```java
// Student class implements Comparable
class Student implements Comparable<Student> {
    String name;
    int age;
    
    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public int compareTo(Student other) {
        // Natural order: by age
        // Returns: negative if this < other
        //          zero if equal
        //          positive if this > other
        return this.age - other.age;
    }
    
    @Override
    public String toString() {
        return name + "(" + age + ")";
    }
}

// Usage
List<Student> students = new ArrayList<>(Arrays.asList(
    new Student("Alice", 22),
    new Student("Bob", 20),
    new Student("Charlie", 25)
));

Collections.sort(students);  // Uses compareTo() - sorts by age
System.out.println(students);  // [Bob(20), Alice(22), Charlie(25)]

// Can also use TreeSet - automatically sorted
TreeSet<Student> sortedSet = new TreeSet<>(students);
// Always keeps students sorted by age
```

**Comparator - Custom Ordering (flexible)**

```java
// Define different comparators
class StudentComparators {
    // Sort by name
    static Comparator<Student> byName() {
        return (s1, s2) -> s1.name.compareTo(s2.name);
    }
    
    // Sort by age descending
    static Comparator<Student> byAgeDesc() {
        return (s1, s2) -> Integer.compare(s2.age, s1.age);  // Reversed
    }
    
    // Sort by age, then by name
    static Comparator<Student> byAgeAndName() {
        return (s1, s2) -> {
            if (s1.age != s2.age) {
                return Integer.compare(s1.age, s2.age);
            }
            return s1.name.compareTo(s2.name);
        };
    }
}

// Usage - different sortings of same data
List<Student> students = new ArrayList<>(Arrays.asList(
    new Student("Alice", 22),
    new Student("Bob", 20),
    new Student("Charlie", 22)
));

// Sort by name
Collections.sort(students, StudentComparators.byName());
// [Alice(22), Bob(20), Charlie(22)]

// Sort by age descending
Collections.sort(students, StudentComparators.byAgeDesc());
// [Alice(22), Charlie(22), Bob(20)]

// Sort by age then name
Collections.sort(students, StudentComparators.byAgeAndName());
// [Bob(20), Alice(22), Charlie(22)]
```

**Comparable vs Comparator - Comparison Table:**

| Aspect | Comparable | Comparator |
|--------|-----------|-----------|
| **How used** | `class X implements Comparable<X>` | `Comparator<X> c = new...` |
| **Method** | `compareTo(Object)` | `compare(Object, Object)` |
| **Natural order** | YES - defines natural order | NO - multiple orderings |
| **Flexibility** | One ordering only | Unlimited custom orderings |
| **Change code** | Must modify class | No need to modify class |
| **Use case** | Age as natural order for Student | Sort by name, age, GPA, etc. |
| **Multiple collections** | Same ordering for all | Different orderings possible |

**Real-World Example:**

```java
// Without Comparable - rigid
class Product {
    String name;
    double price;
    // No compareTo() - can't sort!
}

// With Comparable - one ordering
class Product implements Comparable<Product> {
    String name;
    double price;
    
    public int compareTo(Product other) {
        return Double.compare(this.price, other.price);  // By price
    }
}

// With Comparators - flexible
class Product {
    String name;
    double price;
    
    static Comparator<Product> byPrice() {
        return (p1, p2) -> Double.compare(p1.price, p2.price);
    }
    
    static Comparator<Product> byName() {
        return (p1, p2) -> p1.name.compareTo(p2.name);
    }
    
    static Comparator<Product> byPriceDesc() {
        return (p1, p2) -> Double.compare(p2.price, p1.price);  // Reversed
    }
}

List<Product> products = new ArrayList<>();
products.sort(Product.byName());       // Sort by name
products.sort(Product.byPrice());      // Sort by price
products.sort(Product.byPriceDesc()); // Sort by price descending

// With TreeMap - sorts keys automatically
TreeMap<Product, Integer> inventory = new TreeMap<>(Product.byName());
inventory.put(p1, 10);  // Automatically sorted by name
```

**Comparator Chaining (Java 8+):**

```java
// Chain multiple comparators
Comparator<Student> comparator = 
    Comparator.comparingInt(Student::getAge)           // Primary: age
        .thenComparing(Student::getName);              // Secondary: name

students.sort(comparator);
// Sorts by age first, then by name if ages equal

// More complex
Comparator<Student> complex = 
    Comparator.comparingInt(Student::getAge)
        .reversed()  // Descending age
        .thenComparing(Student::getName);

students.sort(complex);
```

---

## Key Takeaways

✓ **List:** Ordered, allows duplicates (ArrayList, LinkedList)
✓ **Set:** Unique elements (HashSet, TreeSet)
✓ **Map:** Key-value pairs (HashMap, TreeMap)
✓ **Queue:** FIFO/priority (PriorityQueue, ArrayDeque)
✓ Use appropriate collection based on use case

**Next:** [15. Sorting Algorithms](15-Sorting-Algorithms.md)
