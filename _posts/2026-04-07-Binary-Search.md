---
layout: post
title: "二分查找算法详解"
date: 2026-04-08 10:00:00 +0800
categories: [算法]
tags: [二分查找, 算法, 数据结构]
---
# 二分查找

## 一、什么是二分查找

### 1.1 定义
二分查找（Binary Search）是一种在有序数组中查找目标值的算法。它通过将搜索范围不断缩小，直到找到目标值或确定目标值不在数组中。

### 1.2 适用场景
- 当数组有序时，二分查找是一种高效的选择。
- 当数组元素数量较大时，效率优势更加明显。

## 二、基本二分查找实现

### 2.1 基本思想

二分查找的基本步骤：
1. **初始化搜索范围**：左边界 `left` 为 0，右边界 `right` 为数组长度减 1
2. **计算中间位置**：`mid = left + (right - left) / 2`
3. **比较中间元素与目标值**：
   - 如果中间元素等于目标值，返回其索引
   - 如果中间元素小于目标值，在右半部分继续查找
   - 如果中间元素大于目标值，在左半部分继续查找
4. **重复步骤 2-3**：直到找到目标值或搜索范围为空

### 2.2 循环不变量

循环不变量是指在循环的每次迭代前后都保持为真的条件。在二分查找中，循环不变量通常描述了搜索区间的性质。

**核心思想**：在每一次循环中，我们都维护一个不变的性质，每次循环时都看准left左边的元素和right右边的元素，保证其性质不发生改变。循环不变量是二分查找算法的核心。

### 2.3 C++ 实现（闭区间写法[left, right]）

```cpp
int binarySearch(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return -1; // 目标值不存在
}
```

### 2.4 循环不变量分析

在闭区间写法 `[left, right]` 中，循环不变量可以描述为：

- **left 左边的元素**：所有索引小于 `left` 的元素都严格小于 `target`
- **right 右边的元素**：所有索引大于 `right` 的元素都严格大于 `target`
- **当前搜索区间**：如果 `target` 存在于数组中，它必定在 `[left, right]` 区间内

**注意**：这个循环不变量假设数组中没有重复元素。如果数组中有重复的 `target` 值，上述描述可能不完全准确，需要使用专门的变式算法来处理重复元素的情况。

### 2.5 复杂度分析

- **时间复杂度**：O(log n)，其中 n 是数组长度
- **空间复杂度**：O(1)，只需要常数级的额外空间

## 三、不同区间写法（目标值存在1个或者不存在实例）

二分查找的重点是找准循环不变量和终止时left和right的关系。

### 3.1 闭区间写法（[left, right]）

最常见的二分查找实现使用闭区间 `[left, right]`，其中 `left` 和 `right` 都是有效的数组索引。

```cpp
int binarySearch(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    
    while (left <= right) {  // 当 left > right 时，区间为空
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;  // 目标在右半区间
        } else {
            right = mid - 1;  // 目标在左半区间
        }
    }
    
    return -1;  // 目标不存在
}
```
**终止位置**：left在right的右边。

**循环不变量**：在每次循环开始时，满足以下条件：
- 所有索引小于 `left` 的元素都严格小于 `target`
- 所有索引大于 `right` 的元素都严格大于 `target`  
- 如果 `target` 存在于数组中，它必定在 `[left, right]` 区间内

**注意**：这个循环不变量同样假设数组中没有重复元素。

### 3.2 开区间写法（[left, right)）

另一种常见的实现使用左闭右开区间 `[left, right)`，其中 `left` 是有效索引，而 `right` 是边界外的索引。

```cpp
int binarySearchOpen(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size();
    
    while (left < right) {  // 当 left == right 时，区间为空
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;  // 目标在右半区间 [mid+1, right)
        } else {
            right = mid;  // 目标在左半区间[left,right)
        }
    }
    
    return -1;  // 目标不存在
}
```

**终止位置**：left和right在同一点。

**循环不变量**：在每次循环开始时，满足以下条件：
- 所有索引小于 `left` 的元素都严格小于 `target`
- 所有索引大于等于 `right` 的元素都严格大于 `target`
- 如果 `target` 存在于数组中，它必定在 `[left, right)` 区间内

**注意**：这个循环不变量同样假设数组中没有重复元素。

### 3.3 两种写法的比较

| 特性 | 闭区间写法 | 开区间写法 |
|------|------------|------------|
| 初始区间 | [0, len-1] | [0, len) |
| 循环条件 | left <= right | left < right |
| 右边界更新 | right = mid - 1 | right = mid |
| 空区间判断 | left > right | left == right |
| 适用场景 | 一般情况 | 边界查找更直观 |

## 四、二分查找的变式题

### 4.1 查找第一个等于目标值的元素

#### 写法1
```cpp
int findFirst(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            result = mid;  // 记录找到的位置
            right = mid - 1;  // 继续向左查找
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```
#### 写法2
```cpp
int findFirst(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] < target) {
            left = mid + 1;
        }else {
            right = mid - 1;
        }
    }
    return nums[left] == target ? left : -1;
}
```
- left左边都为小于target的数，right的右边都为大于等于target的数，最终left在right的右边，所以left的位置是第一个target在的位置。
- 如果把return后面的result改为left，那么left的位置就是第一个大于等于target的数（如果target不存在就是大于target的数的最小索引）。
### 4.2 查找最后一个等于目标值的元素

```cpp
int findLast(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    int result = -1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            result = mid;  // 记录找到的位置
            left = mid + 1;  // 继续向右查找
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

### 4.3 查找第一个大于等于目标值的元素

```cpp
int findFirstGe(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    int result = nums.size();  // 初始化为数组长度，表示不存在
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] >= target) {
            result = mid;  // 记录可能的结果
            right = mid - 1;  // 继续向左查找
        } else {
            left = mid + 1;
        }
    }
    
    return result;
}
```

### 4.4 查找最后一个小于等于目标值的元素

```cpp
int findLastLe(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    int result = -1;  // 初始化为-1，表示不存在
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] <= target) {
            result = mid;  // 记录可能的结果
            left = mid + 1;  // 继续向右查找
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

## 五、二分查找的应用

### 5.1 搜索插入位置

题目描述：给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。[题目链接](https://leetcode.cn/problems/search-insert-position/description/?envType=study-plan-v2&envId=top-100-liked)

题目思路：等同于找到大于等于target的索引

代码示例

```cpp
int searchInsert(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] < target) {
            left = mid + 1;
        }else {
            right = mid - 1;
        }
    }
    return left;
}
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

### 5.2 搜索二维矩阵

题目描述：
给你一个满足下述两条属性的 m x n 整数矩阵：

- 每行中的整数从左到右按非严格递增顺序排列。
- 每行的第一个整数大于前一行的最后一个整数。
- 给你一个整数 target ，如果 target 在矩阵中，返回 true ；否则，返回 false 。[题目链接](https://leetcode.cn/problems/search-a-2d-matrix/description/?envType=study-plan-v2&envId=top-100-liked)

题目思路：
- 先将矩阵展开成一个有序数组，然后使用二分查找查找目标值。
- 如果目标值在矩阵中，返回 true；否则，返回 false。

代码示例
```cpp
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    int m = matrix.size();
    int n = matrix[0].size();
    
    int left = 0;
    int right = m * n - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int row = mid / n;
        int col = mid % n;
        
        if (matrix[row][col] == target) {
            return true;
        } else if (matrix[row][col] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return false;
}
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

### 5.3 在排序数组中查找元素的第一个和最后一个元素的下标

题目描述：给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。
如果数组中不存在目标值 target，返回 [-1, -1]。[题目链接](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/?envType=study-plan-v2&envId=top-100-liked)

题目思路：
- 先使用lower_bound找到第一个大于等于target的索引，作为开始位置。
- 再使用lower_bound找到第一个大于target的索引，减去1，作为结束位置。

代码示例
```cpp
class Solution {
public:
    int lower_bound(vector<int> &nums, int target){
        int n = nums.size();
        int left = 0;
        int right = n - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return left;
    }

    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans(2, -1);
        int a1 = lower_bound(nums, target);
        int a2 = lower_bound(nums, target + 1) - 1;
        if(a1 < nums.size() && a1 <= a2){
            ans[0] = a1;
            ans[1] = a2;
        }
        return ans;
    }
};
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

### 5.4 查找旋转排序数组中的最小值

题目描述：
已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,2,4,5,6,7] 在变化后可能得到：
- 若旋转 4 次，则可以得到 [4,5,6,7,0,1,2]
- 若旋转 7 次，则可以得到 [0,1,2,4,5,6,7]

注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。

给你一个元素值 互不相同 的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。
[题目链接](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/description/?envType=study-plan-v2&envId=top-100-liked)

代码示例
```cpp
int findMinRotated(vector<int>& nums) {
    int left = 0;
    int right = nums.size() - 1;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] > nums[right]) {
            // 最小值在右半部分
            left = mid + 1;
        } else {
            // 最小值在左半部分
            right = mid;
        }
    }
    
    return nums[left];
}
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

**注意**：这道题的循环条件是left < right，而不是left <= right。

- left < right：确保在二分查找中，left和right不会重叠，导致无限循环。
- left <= right：确保在二分查找中，left和right可以重叠，导致最终返回left或right。

### 5.5 查找旋转排序数组中的目标值

题目描述：

旋转排序数组是指将一个有序数组的某个位置作为旋转点，将数组分为两部分，然后交换这两部分的位置。例如，`[0,1,2,4,5,6,7]` 可能旋转为 `[4,5,6,7,0,1,2]`。

给你旋转后的数组nums和一个整数target，如果nums中存在这个目标值target，则返回它的下标，否则返回-1。
[题目链接](https://leetcode.cn/problems/search-in-rotated-sorted-array/description/?envType=study-plan-v2&envId=top-100-liked)

题目思路：：
- 先使用二分查找找到旋转点的索引i。
- 如果目标值target大于nums[i]，说明目标值在右半部分，否则在左半部分。
- 再使用二分查找在对应的半部分中查找目标值。

代码示例
```cpp
class Solution {
public:
    int searchIndex(vector<int> & nums){
        int n = nums.size();
        int left = 0;
        int right = n - 1;
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] <= nums.back()){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return left;
    }

    int lowerBound(vector<int> & nums, int left, int right, int target){
        while(left <= right){
            int mid = left + (right - left) / 2;
            if(nums[mid] >= target){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return nums[left] == target ? left : -1;
    }
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        int i = searchIndex(nums);
        if(target > nums[n - 1]){
           return lowerBound(nums, 0, i - 1, target);
        }else{
            return lowerBound(nums, i, n - 1, target);
        }
    }

};
```

- 时间复杂度：O(log n)
- 空间复杂度：O(1)

## 六、二分答案技巧

### 6.1 什么是二分答案

二分答案是一种通过二分查找来求解最优化问题的技巧。它的基本思想是：

1. 确定答案的可能范围
2. 对于每个中间值，判断是否满足条件
3. 根据判断结果调整搜索范围

### 6.2 例题：平方根计算

```cpp
int mySqrt(int x) {
    if (x < 2) {
        return x;
    }
    
    int left = 2;
    int right = x / 2;
    
    while (left <= right) {
        long long mid = left + (right - left) / 2;
        long long square = mid * mid;
        
        if (square == x) {
            return mid;
        } else if (square < x) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return right;  // 最后返回较小的整数
}
```

### 6.3 例题：koko 吃香蕉

```cpp
int minEatingSpeed(vector<int>& piles, int h) {
    int left = 1;
    int right = *max_element(piles.begin(), piles.end());
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        int hours = 0;
        
        // 计算以 mid 速度吃完所有香蕉需要的时间
        for (int pile : piles) {
            hours += (pile + mid - 1) / mid;
        }
        
        if (hours <= h) {
            // 可以吃完，尝试更小的速度
            right = mid;
        } else {
            // 吃不完，需要更大的速度
            left = mid + 1;
        }
    }
    
    return left;
}
```

## 七、时间复杂度分析

### 7.1 标准二分查找
- **时间复杂度**：O(log n)，其中 n 是数组长度
- **空间复杂度**：O(1)（迭代版本）或 O(log n)（递归版本）

### 7.2 二分答案
- **时间复杂度**：O(n log C)，其中 n 是数据规模，C 是答案的可能范围
- **空间复杂度**：O(1)

## 八、常见错误与注意事项

### 8.1 整数溢出

在计算 mid 时，使用 `left + (right - left) / 2` 而不是 `(left + right) / 2`，以避免整数溢出。

### 8.2 边界条件处理

- 确保循环条件正确（闭区间用 `<=`，开区间用 `<`）
- 正确更新左右边界（闭区间更新为 `mid ± 1`，开区间右边界更新为 `mid`）
- 处理目标值不存在的情况

### 8.3 重复元素的处理

当数组中存在重复元素时，需要明确查找目标（第一个、最后一个或任意一个），并相应地调整算法。

### 8.4 旋转排序数组的特殊情况

- 当数组没有旋转时（即已经有序）
- 当数组中有重复元素时

## 九、总结

二分查找是一种高效的搜索算法，其核心思想是通过不断缩小搜索范围来快速定位目标值。掌握二分查找的不同写法（闭区间和开区间）以及循环不变量的概念，对于理解和应用二分查找至关重要。

二分查找的变式题和二分答案技巧在解决各种算法问题时非常有用，特别是在处理有序数据和最优化问题时。通过练习和理解这些技巧，我们可以更高效地解决各种复杂的算法问题。

**关键要点**：
- 明确循环不变量
- 选择合适的区间写法
- 正确处理边界条件
- 灵活应用二分思想解决变式问题
- 注意避免常见错误

