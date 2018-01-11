# 4 Sum II

Tags: Array, Hash Table, Medium

## Question

- leetcode: [4Sum | LeetCode OJ](https://leetcode.com/problems/4sum-ii/description/)

### Problem Statement

Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l) there are such that A[i] + B[j] + C[k] + D[l] is zero.

To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of $$-2^28$$ to $$2^28$$ - 1 and the result is guaranteed to be at most $$2^31$$ - 1.

## 题解 - 哈希表

我之前的思路是每个数组用两个指针指向头尾，每次去找最小的和次小的头指针，最大和次大的头指针，判断这四个数的和与 0 比较，再挪动最小和最大的指针，不断缩小遍历尝试。但是在实现过程中，去找最小和次小的头指针就比较麻烦。参考了 discuss 的思路，有点类似 TwoSum 的哈希表解法，map 的 key 代表和，val 代表出现的次数。

```cpp
class Solution {
public:
    int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
        unordered_map<int,int> map;
        
        for (int i = 0; i < A.size(); ++ i) {
            for (int j = 0; j < B.size(); ++ j) {
                ++ map[A[i] + B[j]];
            }
        }
        
        int count = 0;
        
        for (int i = 0; i < C.size(); ++ i) {
            for (int j = 0; j < D.size(); ++ j) {
                if (map.find(0 - C[i] - D[j]) != map.end()) {
                    count += map[0 - C[i] - D[j]];
                }
            }
        }
        
        return count;
    }
};

// 另一种写法 auto
int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
        unordered_map<int, int>  abSum;
        for(auto a : A) {
            for(auto b : B) {
                ++abSum[a+b];
            }
        }
        int count = 0;
        for(auto c : C) {
            for(auto d : D) {
                // unordered_map<int, int>::iterator it;
                auto it = abSum.find(0 - c - d);
                if(it != abSum.end()) {
                    count += it->second;
                }
            }
        }
        return count;
    }
```

### 源码分析

1. 异常处理。
2. 使用 C++ 11 中的哈希表实现`unordered_map`映射值和索引。Python 中的`dict`就是天然的哈希表。
3. 学习 auto 的用法。
4. 学习 it -> second 的用法。

### 复杂度分析

引入了哈希表，空间复杂度为 $$O(n)$$, 2 个 for 循环遍历2次数组，时间复杂度为 $$O(n^2)$$.