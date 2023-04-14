# Quick sort partition

Partition function is used in quick sort to find pivot index. It returns the correct index of a random element in array. This element's position is determined if were in the sorted array.&#x20;

Also, all elements before that index is less than _arr\[index]_, all elements after that index is greater then _arr\[index]._ Time complexity is in O(n)

```cpp
int Partition(int arr[], int length, int start, int end) {    
    if (arr == nullptr || length < 1 || start < 0 || end >= length || start > end)
        throw "Invalid parameters";
    
    int index = rand() % (end - start + 1) + start;
    swap(&arr[start], &arr[index]);
    
    int pivot = arr[start];
    int i = start, j = end + 1;
    
    while (true) {
        
        // find the first num >= pivot
        while (arr[i++] < pivot) {
            if (i == end) break;
        }
        
        // find the first num <= pivot
        while (arr[--j] > pivot) {
            if (j == start) break;
        }
        
        // check two ptr not cross
        if (i >= j) {
            break;
        }
        
        // arrange smaller left, larger right
        swap(&arr[i], &arr[j]);
    }
    return j;    
}
```

The algs4 Java version:

```java
private static int partition(Comparable[] a, int lo, int hi) {
        int i = lo;
        int j = hi + 1;
        Comparable v = a[lo];
        while (true) {

            // find item on lo to swap
            while (less(a[++i], v)) {
                if (i == hi) break;
            }

            // find item on hi to swap
            while (less(v, a[--j])) {
                if (j == lo) break;      // redundant since a[lo] acts as sentinel
            }

            // check if pointers cross
            if (i >= j) break;

            exch(a, i, j);
        }

        // put partitioning item v at a[j]
        exch(a, lo, j);

        // now, a[lo .. j-1] <= a[j] <= a[j+1 .. hi]
        return j;
    }
```



