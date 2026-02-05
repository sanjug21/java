# 15. SORTING ALGORITHMS

## Selection Sort

**Find minimum element, place at current position**

**How It Works:**
1. Start with first position
2. Find minimum element in remaining unsorted array
3. Swap minimum with current position
4. Move to next position and repeat



```java
void selectionSort(int[] arr) {
    int n = arr.length;
    
    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;
        
        // Find minimum element in remaining unsorted array
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        
        // Swap minimum element with current position
        if (minIndex != i) {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}
```
**Visualization:**

```
Initial: [64, 34, 25, 12, 22, 11, 90]

Pass 1:  Find min in [64,34,25,12,22,11,90] = 11
         [11, 34, 25, 12, 22, 64, 90]  ← 11 and 64 swapped

Pass 2:  Find min in [34,25,12,22,64,90] = 12
         [11, 12, 25, 34, 22, 64, 90]  ← 12 and 34 swapped

Pass 3:  Find min in [25,34,22,64,90] = 22
         [11, 12, 22, 34, 25, 64, 90]  ← 22 and 25 swapped

Pass 4:  Find min in [34,25,64,90] = 25
         [11, 12, 22, 25, 34, 64, 90]  ← 25 and 34 swapped

Pass 5:  Find min in [34,64,90] = 34
         [11, 12, 22, 25, 34, 64, 90]  ← Already in place

Pass 6:  Find min in [64,90] = 64
         [11, 12, 22, 25, 34, 64, 90]  ← Already in place

Result:  [11, 12, 22, 25, 34, 64, 90]  ✓ Sorted!
```

**Implementation:**

**Key Characteristics:**
- Makes **exactly n-1 passes** (always)
- Makes **minimum number of swaps** (at most n-1)
- Good for when swap operation is expensive
- Cache-friendly due to sequential access pattern
- Unstable (equal elements may be reordered)

**Complexity:** Always O(n²), Space O(1), Not stable
**Best Use:** Small datasets, minimal swap requirement

---

## Insertion Sort

**Build sorted array by inserting elements one by one**

**How It Works:**
1. Start from second element (first is considered sorted)
2. Compare with elements in sorted portion
3. Shift larger elements right
4. Insert current element in correct position
5. Move to next element and repeat



**Implementation:**

```java
void insertionSort(int[] arr) {
    int n = arr.length;
    
    // Start from second element (index 1)
    for (int i = 1; i < n; i++) {
        int key = arr[i];  // Element to insert
        int j = i - 1;     // Index in sorted portion
        
        // Shift elements greater than key one position right
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];  // Shift right
            j--;
        }
        
        // Insert key in correct position
        arr[j + 1] = key;
    }
}
```
**Visualization:**

```
Initial: [64, 34, 25, 12, 22, 11, 90]

Pass 1:  Sorted: [64]  →  Insert 34
         64 > 34? Yes, shift 64 right
         [34, 64, 25, 12, 22, 11, 90]

Pass 2:  Sorted: [34,64]  →  Insert 25
         64 > 25? Yes, shift 64 → 34 > 25? Yes, shift 34
         [25, 34, 64, 12, 22, 11, 90]

Pass 3:  Sorted: [25,34,64]  →  Insert 12
         64 > 12? Yes, shift 64 → 34 > 12? Yes, shift 34
         25 > 12? Yes, shift 25
         [12, 25, 34, 64, 22, 11, 90]

Pass 4:  Sorted: [12,25,34,64]  →  Insert 22
         64 > 22? Yes, shift 64 → 34 > 22? Yes, shift 34
         25 > 22? Yes, shift 25 → 12 > 22? No, insert 22
         [12, 22, 25, 34, 64, 11, 90]

Pass 5:  Sorted: [12,22,25,34,64]  →  Insert 11
         Multiple shifts needed: 11 is smallest
         [11, 12, 22, 25, 34, 64, 90]

Pass 6:  Sorted: [11,12,22,25,34,64]  →  Insert 90
         90 > 64? No, insert at end (no shift needed)
         [11, 12, 22, 25, 34, 64, 90]

Result:  [11, 12, 22, 25, 34, 64, 90]  ✓ Sorted!
```

**Key Characteristics:**
- **Adaptive:** O(n) best case when array is already sorted
- **Stable:** Preserves relative order of equal elements
- **Online:** Can sort data as it receives it (streaming)
- **In-place:** Requires O(1) extra space
- Used in **hybrid algorithms** (TimSort uses it for small subarrays)
- Cache-friendly, minimal jumps in memory

**Complexity:** Best O(n), Avg/Worst O(n²), Space O(1), Stable
**Best Use:** Nearly sorted data, small datasets, online scenarios

---

## Comparison: Selection vs Insertion

| Aspect | Selection Sort | Insertion Sort |
|--------|----------------|----------------|
| **Pass Pattern** | Always n-1 passes | Variable passes (1 to n-1) |
| **Comparisons** | Always O(n²) | O(n²) worst, O(n) best |
| **Swaps** | Minimal (≤n-1) | Can be many (up to n²) |
| **Data Movement** | Few writes | Many writes |
| **Stable?** | No | Yes |
| **Adaptive?** | No (always O(n²)) | Yes (O(n) if sorted) |
| **Best For** | Minimizing writes | Nearly sorted, small data |
| **Worst For** | Reverse sorted | Reverse sorted arrays |

**Decision:**
- **Selection:** Use when swap cost is high, you need predictable O(n²)
- **Insertion:** Use for nearly sorted data or small online streams

---

## Bubble Sort

**Repeatedly swaps adjacent elements if they're in wrong order**

**Implementation:**

```java
void bubbleSort(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {
        boolean swapped = false;
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = true;
            }
        }
        if (!swapped) break;  // Optimization
    }
}
```

**Visualization:**

```
Initial: [5, 2, 8, 1, 9]

Pass 1: Compare adjacent pairs, largest "bubbles" to end
        [2, 5, 8, 1, 9]  → [2, 5, 1, 8, 9]  → [2, 5, 1, 8, 9]
        After: [2, 5, 1, 8, 9]  (9 is in correct position)

Pass 2: Ignore last element, bubble remaining
        [2, 5, 1, 8] → [2, 1, 5, 8]  → [2, 1, 5, 8]
        After: [2, 1, 5, 8]  (8, 9 in correct positions)

Pass 3: Bubble in remaining unsorted portion
        [2, 1, 5] → [1, 2, 5]
        After: [1, 2, 5]  (5, 8, 9 in correct positions)

Pass 4: Only one element left, already sorted
        [1, 2]

Result: [1, 2, 5, 8, 9]  ✓ Sorted!
```

**Complexity:** Best O(n), Avg/Worst O(n²), Space O(1), Stable

---

## Merge Sort

**Divide and conquer - split, sort, merge**

**Implementation:**

```java
void mergeSort(int[] arr, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

void merge(int[] arr, int left, int mid, int right) {
    int[] leftArr = Arrays.copyOfRange(arr, left, mid + 1);
    int[] rightArr = Arrays.copyOfRange(arr, mid + 1, right + 1);
    
    int i = 0, j = 0, k = left;
    while (i < leftArr.length && j < rightArr.length) {
        arr[k++] = (leftArr[i] <= rightArr[j]) ? leftArr[i++] : rightArr[j++];
    }
    
    while (i < leftArr.length) arr[k++] = leftArr[i++];
    while (j < rightArr.length) arr[k++] = rightArr[j++];
}
```

**Visualization:**

```
Initial: [38, 27, 43, 3, 9, 82, 10]

DIVIDE PHASE (Split into single elements):
         [38, 27, 43, 3, 9, 82, 10]
              /              \
         [38, 27, 43, 3]    [9, 82, 10]
           /        \         /      \
      [38, 27]   [43, 3]  [9, 82]  [10]
        /  \      /  \     /  \      |
      [38][27]  [43][3]  [9][82]   [10]

MERGE PHASE (Merge sorted subarrays):
      [27,38] [3,43]   [9,82]  [10]
         \      /        \      /
       [3,27,38,43]    [9,10,82]
              \              /
          [3,9,10,27,38,43,82]  ✓ Sorted!

Merge Example: [27,38] + [3,43]
  Compare 27 vs 3: 3 is smaller → [3]
  Compare 27 vs 43: 27 is smaller → [3,27]
  Compare 38 vs 43: 38 is smaller → [3,27,38]
  43 remaining → [3,27,38,43]
```

**Complexity:** Always O(n log n), Space O(n), Stable

---

## Quick Sort

**Partition around pivot, recursively sort partitions**

**Implementation:**

```java
void quickSort(int[] arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

int partition(int[] arr, int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    
    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;
    
    return i + 1;
}
```

**Visualization:**

```
Initial: [7, 2, 8, 1, 6, 3, 5]
         Choose rightmost as pivot: 5

PARTITION around pivot 5:
         Smaller: [2, 1, 3]  |  Pivot: [5]  |  Larger: [7, 8, 6]
         Result: [2, 1, 3, 5, 7, 8, 6]
         Pivot index: 3

RECURSIVE calls:
  LEFT:  [2, 1, 3]  (less than 5)
         Pivot: 3
         Result: [1, 2, 3]
         
  RIGHT: [7, 8, 6]  (greater than 5)
         Pivot: 6
         [7, 8] > 6 → stays
         Result: [6, 7, 8]

MERGE all parts: [1, 2, 3] + [5] + [6, 7, 8]
                 = [1, 2, 3, 5, 6, 7, 8]  ✓ Sorted!
```

**Complexity:** Best/Avg O(n log n), Worst O(n²), Space O(log n), Not stable

---

## Heap Sort

**Build max heap, extract max repeatedly**

**Implementation:**

```java
void heapSort(int[] arr) {
    int n = arr.length;
    
    // Build max heap
    for (int i = n / 2 - 1; i >= 0; i--) {
        heapify(arr, n, i);
    }
    
    // Extract elements from heap
    for (int i = n - 1; i > 0; i--) {
        int temp = arr[0];
        arr[0] = arr[i];
        arr[i] = temp;
        heapify(arr, i, 0);
    }
}

void heapify(int[] arr, int n, int i) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;
    
    if (left < n && arr[left] > arr[largest]) largest = left;
    if (right < n && arr[right] > arr[largest]) largest = right;
    
    if (largest != i) {
        int temp = arr[i];
        arr[i] = arr[largest];
        arr[largest] = temp;
        heapify(arr, n, largest);
    }
}
```

**Visualization:**

```
Initial Array: [4, 10, 3, 5, 1, 2]

STEP 1: BUILD MAX HEAP
        
        Array:        [4, 10, 3, 5, 1, 2]
        Binary Tree:       10
                          /  \
                         5    3
                        / \   /
                       4   1 2
        
        After heapify:    [10, 5, 3, 4, 1, 2]
                              10
                            /  \
                           5    3
                          / \   /
                         4   1 2

STEP 2: EXTRACT MAX (10) and Heapify
        Swap 10 ↔ 2:  [2, 5, 3, 4, 1, |10|]
        Heapify:       [5, 4, 3, 2, 1, |10|]
        
STEP 3: EXTRACT MAX (5)
        Swap 5 ↔ 1:   [1, 4, 3, 2, |5, 10|]
        Heapify:      [4, 2, 3, 1, |5, 10|]
        
Continue until:    [1, 2, 3, 4, 5, 10]  ✓ Sorted!
```

**Complexity:** Always O(n log n), Space O(1), Not stable

---

## Counting Sort

**Count occurrences, reconstruct sorted array**

**Implementation:**

```java
void countingSort(int[] arr, int max) {
    int[] count = new int[max + 1];
    
    // Count occurrences
    for (int num : arr) {
        count[num]++;
    }
    
    // Reconstruct array
    int idx = 0;
    for (int i = 0; i <= max; i++) {
        while (count[i]-- > 0) {
            arr[idx++] = i;
        }
    }
}
```

**Visualization:**

```
Initial: [3, 1, 4, 1, 5, 9, 2, 6]  (max = 9)

STEP 1: COUNT OCCURRENCES
        Index: 0  1  2  3  4  5  6  7  8  9
        Count: 0  2  1  1  1  1  1  1  0  1
        
        Explanation:
        - 0 appears 0 times
        - 1 appears 2 times (at positions 1, 3)
        - 2 appears 1 time
        - 3 appears 1 time
        - 4 appears 1 time
        - 5 appears 1 time
        - 6 appears 1 time
        - 9 appears 1 time

STEP 2: RECONSTRUCT ARRAY
        Read count array from index 0 to 9:
        - count[0] = 0 times   → skip
        - count[1] = 2 times   → [1, 1, ...]
        - count[2] = 1 time    → [1, 1, 2, ...]
        - count[3] = 1 time    → [1, 1, 2, 3, ...]
        - count[4] = 1 time    → [1, 1, 2, 3, 4, ...]
        - count[5] = 1 time    → [1, 1, 2, 3, 4, 5, ...]
        - count[6] = 1 time    → [1, 1, 2, 3, 4, 5, 6, ...]
        - count[9] = 1 time    → [1, 1, 2, 3, 4, 5, 6, 9]

Result: [1, 1, 2, 3, 4, 5, 6, 9]  ✓ Sorted!
```

**Complexity:** O(n + k), Space O(k), Stable
**Note:** Works only for non-negative integers, k = max value

---

## Complexity Comparison

| Algorithm | Best | Average | Worst | Space | Stable | Use Case |
|-----------|------|---------|-------|-------|--------|----------|
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No | Minimizing swaps |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes | Nearly sorted, small data |
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes | Educational only |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | Linked lists, guaranteed performance |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Arrays, general purpose |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No | In-place sorting |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes | Limited range integers |

---

## Built-in Sorting

```java
int[] arr = {5, 2, 8, 1, 9};

// Arrays.sort() - uses Dual-Pivot QuickSort for primitives
Arrays.sort(arr);

// Arrays.sort() - uses TimSort for objects (stable)
Integer[] objArr = {5, 2, 8, 1, 9};
Arrays.sort(objArr);

// Custom comparator
Arrays.sort(objArr, (a, b) -> b - a);  // Descending order

// Collections.sort() - for List
List<Integer> list = Arrays.asList(5, 2, 8, 1, 9);
Collections.sort(list);
```

---

## Key Takeaways

✓ **Merge Sort:** Guaranteed O(n log n), stable
✓ **Quick Sort:** Fast average case, in-place
✓ **Heap Sort:** In-place, guaranteed O(n log n)
✓ **Counting Sort:** Linear time for limited range
✓ Use `Arrays.sort()` for production code

**Next:** [16. Generics](16-Generics.md)
