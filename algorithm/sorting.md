---
description: Sorting algorithms
---

# Sorting

// TODO: heap sort, radix sort

### Quick Sort

```cpp
void quickSort(vector<int>& nums, int lo, int hi) {
    if (lo >= hi) return;
    int j = partition(nums, lo, hi);
    quickSort(nums, lo, j - 1);
    quickSort(nums, j, hi);
}

// widely used with binary search to O(n)
// 1. position a element in array to where it should be if array is sorted
// 2. place all elements smaller than it to the left, all elements larger right
// 3. return that element index.
void partition(vector<int>& nums, int lo, int hi) {
    int idx = rand() % (hi - lo + 1) + lo;
    swap(nums[idx], nums[lo]);
    
    int pivot = nums[lo];
    int i = lo, j = hi + 1;
    
    while (true) {
        while (nums[++i] < pivot) 
            if (i == hi) break;
        while (nums[j--] > pivot)
            if (i == lo) break;
            
        if (i >= j) break;
        
        swap(nums[i], nums[j]);
    }
    swap(nums[lo], nums[j]);
    return j;    
}
```

### Merge Sort

```cpp
void mergeSort(vector<int>& nums, int l, int r) {
    if (r <= l) return;
    int mid = l + (r - l) >> 1;
    mergeSort(nums, l, mid);
    mergeSort(nums, mid + 1, r);
    merge(nums, l, mid, r);
}

void merge(vector<int>& nums, int l, int m, int r) {
    vector<int> tmp(r - l + 1);
    int p1 = 0, p2 = m + 1;
    int i = 0;
    
    while (p1 <= m && p2 <= r) {
        if (nums[p1] < nums[p2])
            tmp[i] = nums[p1++];
        else
            tmp[i] = nums[p2++];
        i++;        
    }
    
    while (p1 <= m) 
        tmp[i++] = nums[p1++];
    while (p2 <= r) 
        tmp[i++] = nums[p2++];
        
    for (int j = 0; j < tmp.size(); j++) 
        nums[i + 1] = tmp[j];
}
```

### Bucket Sort

```cpp
void bucketSort(vector<int>& nums) {
    int n = nums.size();
    vector<int> buckets(n + 1);
    for (int num : nums) {
        buckets[num]++;
    }
    
    int index = 0;
    for (int i = 0; i <= n; i++) {
        for (int j = 0; j < bucket[i]; j++) 
            nums[index++] = i;
    }
}
```
