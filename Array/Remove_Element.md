# Remove Element

Tags: Array, Two Pointers, Easy

## Question

- leetcode: [Remove Element](https://leetcode.com/problems/remove-element/)
- lintcode: [Remove Element](http://www.lintcode.com/en/problem/remove-element/)
- nowcoder: [Remove Element](https://www.nowcoder.com/practice/1e1b7d86039e4427b4b6f7cbb856c301?tpId=46&tqId=29152&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given an array and a value, remove all instances of that value in place and
return the new length.

Do not allocate extra space for another array, you must do this in place with
constant memory.

The order of elements can be changed. It doesn't matter what you leave beyond
the new length.

**Example:**

Given input array _nums_ = `[3,2,2,3]`, _val_ = `3`

Your function should return length = 2, with the first two elements of _nums_
being 2.

  1. Try two pointers.
  2. Did you use the property of "the order of elements can be changed"?
  3. What happens when the elements to remove are rare?


## 解法1 - 待删除元素沉底

由于数组元素的顺序可变，头指针表示当前元素遍历的位置，尾指针表示当前待删除元素替换的位置，头尾指针相遇后，则遍历完成，尾指针索引则为新数组的长度。

### C++
```cpp
class Solution {
public:
    int removeElement(int A[], int n, int elem) {
        // 处理非法输入，返回0
        if (A == NULL || n <= 0) {
            return 0;
        }
         
        int length = n;
        for (int i = 0; i < length; ++ i) {
            if (A[i] == elem) {
                //待remove的元素沉底
                swap( A[i], A[length - 1]);
                 
                -- length;
                -- i; //由于 for 循环体的 ++,需回退一格
            }
        }
         
        return length;
    }
};


// 更简洁的写法
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {

        int last = nums.size() - 1;
        for (int i = 0; i <= last; ++i) {
            if (nums[i] == val) {
                swap(nums[i --], nums[last --]);
            }
        }
        return last + 1;
    }
};

```

### 源码分析

自减 length 的同时，需自减 i，因为还需要检查从尾部替换回来的新元素，即需回退一格。

### 复杂度分析

此方法只遍历一次数组，因此时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.

## 解法2 - STL 的 erase()
```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == val) {
                nums.erase(nums.begin() + i);
                --i;
            }
        }
        return nums.size();
    }
};
```

### 源码分析

注意 erase 之后也需要回退一格，否则会漏掉元素。

## 解法3 - 两根指针从前往后遍历

使用两根指针从前往后遍历，一根指向当前遍历元素，一根指向新数组的下一个元素索引值,元素不为给定元素时完成覆写,直至遍历结束。

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int j = 0;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == val) {
                continue;
            }
            nums[j] = nums[i];
            ++ j;
        }
        return j;
    }
};


// 更简洁的写法
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int j = 0;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] != val) {
                nums[j++] = nums[i];
            }
        }
        return j;
    }
};

```

### 源码分析

由于给定值在数组中出现次数极少时这种方法效率不高，因此我们可以想办法减少赋值及自增操作，即采用解法1

### 复杂度分析

遍历数组一次，最坏情况下需要赋值及 j 自增，故最好最坏情况下时间复杂度均为 $$O(n)$$, 空间复杂度为 $$O(1)$$.