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

### ArrayList

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add(1, "C");  // Insert at index 1
System.out.println(list);  // [A, C, B]
list.remove(0);  // Remove at index 0
System.out.println(list.get(0));  // C
```

**Features:** Dynamic array, fast random access, slow insertions/deletions

### LinkedList

```java
LinkedList<String> list = new LinkedList<>();
list.addFirst("A");
list.addLast("B");
list.add(1, "C");
System.out.println(list);  // [A, C, B]
```

**Features:** Doubly linked list, fast insertions/deletions, slow random access

---

## Set Interface

### HashSet

```java
Set<Integer> set = new HashSet<>();
set.add(1);
set.add(2);
set.add(1);  // Duplicate ignored
System.out.println(set);  // [1, 2] (unordered)
```

**Features:** No duplicates, unordered, O(1) add/remove/contains

### TreeSet

```java
Set<Integer> set = new TreeSet<>();
set.add(5);
set.add(1);
set.add(3);
System.out.println(set);  // [1, 3, 5] (sorted)
```

**Features:** Sorted, no duplicates, O(log n) operations

---

## Map Interface

### HashMap

```java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);
map.put("A", 3);  // Overwrites value
System.out.println(map.get("A"));  // 3
System.out.println(map.containsKey("B"));  // true
```

**Features:** Key-value pairs, O(1) operations, unordered

### TreeMap

```java
Map<String, Integer> map = new TreeMap<>();
map.put("C", 3);
map.put("A", 1);
map.put("B", 2);
System.out.println(map);  // {A=1, B=2, C=3} (sorted by key)
```

**Features:** Sorted by keys, O(log n) operations

---

## Queue Interface

### PriorityQueue

```java
Queue<Integer> pq = new PriorityQueue<>();
pq.add(5);
pq.add(1);
pq.add(3);
System.out.println(pq.poll());  // 1 (min heap)
System.out.println(pq.poll());  // 3
```

**Features:** Min heap by default, custom comparator possible

### ArrayDeque

```java
Deque<String> deque = new ArrayDeque<>();
deque.addFirst("A");
deque.addLast("B");
System.out.println(deque.pollFirst());  // A
System.out.println(deque.pollLast());   // B
```

**Features:** Double-ended queue, faster than LinkedList as queue/stack

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

## Iterator

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
Iterator<String> iterator = list.iterator();

while (iterator.hasNext()) {
    String item = iterator.next();
    if ("B".equals(item)) {
        iterator.remove();  // Safe removal
    }
}
System.out.println(list);  // [A, C]
```

---

## Comparable vs Comparator

### Comparable (Natural Ordering)

```java
class Student implements Comparable<Student> {
    String name;
    int age;
    
    public int compareTo(Student other) {
        return this.age - other.age;  // Sort by age
    }
}

List<Student> students = new ArrayList<>();
Collections.sort(students);  // Uses compareTo()
```

### Comparator (Custom Ordering)

```java
Comparator<Student> nameComparator = (s1, s2) -> s1.name.compareTo(s2.name);
Collections.sort(students, nameComparator);  // Sort by name
```

---

## Key Takeaways

✓ **List:** Ordered, allows duplicates (ArrayList, LinkedList)
✓ **Set:** Unique elements (HashSet, TreeSet)
✓ **Map:** Key-value pairs (HashMap, TreeMap)
✓ **Queue:** FIFO/priority (PriorityQueue, ArrayDeque)
✓ Use appropriate collection based on use case

**Next:** [15. Sorting Algorithms](15-Sorting-Algorithms.md)
