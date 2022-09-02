---
description: Binary Search cheat sheet
---

# Binary Search

### **1. Find an exact target in sorted array**

```cpp
int find(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target)
            return mid;
        else if (nums[mid] < target) 
            left = mid + 1;
        else // nums[mid] > target
            right = mid;
    }
    return -1;
}
```

### **2. Find first number not smaller than (>=) target**

```cpp
int find(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] < target) 
            left = mid + 1;
        else // nums[mid] >= target
            right = mid;
    }
    return right;
}
```

Noted that no need to check `nums[mid] == target`. An alternative in C++ [STL](https://cplusplus.com/reference/algorithm/lower\_bound/) `lower_bound`:

```cpp
std::vector<int>::iterator low;
low = std::lower_bound (v.begin(), v.end(), 20); 
```

Transformation: find last number smaller then target.

```cpp
 return right - 1;
```

### **3. Find first number larger than (>) target**

```cpp
int find(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] <= target)
            left = mid + 1;
        else // nums[mid] > target
            right = mid;
    }
    return right;
}
```

Similarly, a transformation is to **find last number not larger than (<=) target**.

```cpp
  return right - 1;
```

### 4. Find the closest element in array

```cpp
int find(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] < target)
            left = mid + 1;
        else 
            right = mid;
    }
    
    left -= 1;
    right = left + 1;
    
    return right;
}
```

For this transformation, C++ [STL](https://cplusplus.com/reference/algorithm/upper\_bound/) `upper_bound`:

```cpp
std::vector<int>::iterator low,up;
low= std::lower_bound (v.begin(), v.end(), 20); 
up = std::upper_bound (v.begin(), v.end(), 20);
```

low is the iterator points to the first elements >= val, up  is the iterator points to the first elements > val.

#### Difference

![](https://user-images.githubusercontent.com/73092222/178014411-a1d023d3-b59e-4115-9c34-b499b2fcb02f.jpg)

Correction: std::upper\_bound should return 4 in above picture.

#### References

[LeetCode Binary Search Summary](https://www.cnblogs.com/grandyang/p/6854825.html)
