# Range Sum Query - Immutable

Tags: Dynamic Programming, Segment Tree

## Problem

Given an integer array *nums*, find the sum of the elements between indices *i* and *j* (*i* ≤ *j*), inclusive.

**Example:**

```
Given nums = [-2, 0, 3, -5, 2, -1]

sumRange(0, 2) -> 1
sumRange(2, 5) -> -1
sumRange(0, 5) -> -3

```

**Note:**

1. You may assume that the array does not change.
2. There are many calls to *sumRange* function.

## Solution 1 - TLE

想到用一个二维数组来存，然而在最后一个例子超时了。。

```cpp
class NumArray {
public:
    NumArray(vector<int> nums) {
        int n = nums.size();
        dp = vector<vector<int>> (n, vector<int> (n, 0));
        
        for (int i = 0; i < n; ++i) dp[i][i] = nums[i];
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                dp[i][j] = dp[i][j - 1] + nums[j];
            }
        }
    }
    
    int sumRange(int i, int j) {
        return dp[i][j];
    }
    
private:
    vector<vector<int>> dp;
    
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```

## Solution 2 - caching - optimized

参考了[solution](https://leetcode.com/problems/range-sum-query-immutable/solution/) ，$$sumRange(i,j)=sum[j+1]−sum[i]$$ 

比较 trick 的一个地方是，dp 数组占用了 n + 1 的空间，避免了当 i = j 的情况时 dp[j] - dp[i] = 0 永远成立，从而无法区分。

`Notice in the code above we inserted a dummy 0 as the first element in the *sum* array. This trick saves us from an extra conditional check in *sumRange* function.` 

相当于把 dp[0] 看成了 dummy node

```cpp
class NumArray {
public:
    NumArray(vector<int> nums) {
        int n = nums.size();
        dp = vector<int> (n + 1, 0);
        
        for (int i = 0; i < n; ++i) {
            dp[i + 1] = dp[i] + nums[i];
        }
    }
    
    int sumRange(int i, int j) {
        return dp[j + 1] - dp[i];
    }
    
private:
    vector<int> dp;
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```

## 复杂度

时间复杂度：

构造函数需要 $$O(n)$$ ，每次查询只需要 $$O(1)$$

空间复杂度：$$O(n)$$

## Solution 3 - segment tree

```cpp
class SegmentTree {
public:
    SegmentTree *left, *right;
    int start, end;
    int sum;
    SegmentTree(int start, int end, int sum=0): left(NULL), right(NULL), start(start), end(end), sum(sum) {};
    
    static SegmentTree *build(int start, int end, vector<int> &a) {
        if (start > end)
            return NULL;

        SegmentTree* node = new SegmentTree(start, end, a[start]);
        if (start == end) 
            return node;

        int mid = (start + end) / 2;
        node->left = build(start, mid, a);
        node->right = build(mid+1, end, a);
        node->sum = node->left->sum + node->right->sum;

        return node;
    }

    static int query(SegmentTree *root, int start, int end) {
        if (start <= root->start &&  root->end <= end) {
            return root->sum;
        }
        
        if (root->left->end >= end) {
            return query(root->left, start, end);
        }
        if (root->right->start <= start) {
            return query(root->right, start, end);
        }
        
        int leftsum = query(root->left, start, root->left->end);
        int rightsum = query(root->right, root->right->start, end);
        return leftsum + rightsum;
    }
    
    static void update(SegmentTree* root, int i, int val) {
        if (!root || root -> start > i || root -> end  < i) return;
        
        if (root -> start == i && root -> end == i) {
            root -> sum = val;
            return;
        }
        
        update(root -> left, i, val);
        update(root -> right, i, val);
        
        root -> sum = root -> left -> sum + root -> right -> sum;
    }
};

class NumArray {
public:
    NumArray(vector<int> nums) {
        root = SegmentTree :: build(0, nums.size() - 1, nums);
    }
    
    void update(int i, int val) {
        SegmentTree :: update(root, i, val);
    }
    
    int sumRange(int i, int j) {
        return SegmentTree :: query(root, i, j);
    }
    
private:
    SegmentTree* root;
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(i,val);
 * int param_2 = obj.sumRange(i,j);
 */
```

