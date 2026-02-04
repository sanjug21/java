# 15. SORTING ALGORITHMS

## Bubble Sort

**Repeatedly swaps adjacent elements if they're in wrong order**

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

**Complexity:** Best O(n), Avg/Worst O(n²), Space O(1), Stable

---

## Merge Sort

**Divide and conquer - split, sort, merge**

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

**Complexity:** Always O(n log n), Space O(n), Stable

---

## Quick Sort

**Partition around pivot, recursively sort partitions**

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

**Complexity:** Best/Avg O(n log n), Worst O(n²), Space O(log n), Not stable

---

## Heap Sort

**Build max heap, extract max repeatedly**

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

**Complexity:** Always O(n log n), Space O(1), Not stable

---

## Counting Sort

**Count occurrences, reconstruct sorted array**

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

**Complexity:** O(n + k), Space O(k), Stable
**Note:** Works only for non-negative integers, k = max value

---

## Complexity Comparison

| Algorithm | Best | Average | Worst | Space | Stable | Use Case |
|-----------|------|---------|-------|-------|--------|----------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes | Small datasets |
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
