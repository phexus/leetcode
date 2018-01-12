# Pascal's Triangle II

Tags: Array, Easy

## Question

- leetcode: [Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/description/)
- nowcoder: [Pascal's Triangle II](https://www.nowcoder.com/practice/a60ee4a1c8a04c3a93f1de3cf9c16f19?tpId=46&tqId=29061&tPage=2&rp=1&ru=%2Fta%2Fleetcode&qru=%2Fta%2Fleetcode%2Fquestion-ranking)

### Problem Statement

Given an index k, return the kth row of the Pascal's triangle.

For example, given k = 3,
Return [1,3,3,1].

### Note：

Could you optimize your algorithm to use only O(k) extra space?

## 题解 - 从后往前迭代生成

只能使用O(k)的空间。所以不能用前面二维数组的方式，只能使用一位数组滚动计算。
从前往后生成，会出现数据覆盖的问题，由 [1,2,1] 向 [1,3,3,1] 生成时， 会错误生成为 [1,3,4,1]。有两种思路来解决：1.开辟临时变量存储上一层的结果；2. 从后往前生成。
这里采用第二种，更简洁

```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> result(rowIndex + 1);
        
        for (int i = 0; i <= rowIndex; ++ i ) {
            result[0] = 1;
            result[i] = 1;
            
            for (int j = i - 1; j > 0; -- j) {
                result[j] += result[j - 1];
            }
        }
        
        return result;
    }
};

// 另一种写法
public:
    vector<int> getRow(int rowIndex) {
        //提前全部初始化为 1
        vector<int> result(rowIndex + 1， 1);
        
        for (int i = 0; i <= rowIndex; ++ i ) {
            for (int j = i - 1; j > 0; -- j) {
                result[j] += result[j - 1];
            }
        }
        
        return result;
    }
};
```

### 源码分析

从后往前滚动生成，更加简洁。


### 复杂度分析

时间复杂度是 $$O(n^2)$$, 空间复杂度为 $$O(1)$$.

最初我的比较笨的解法，又开辟了一块空间，空间复杂度 $$O(k)$$ 不推荐

```cpp
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> result(rowIndex + 1);
        
        for (int row = 0; row <= rowIndex; ++ row) {
            vector<int> lastRow(result);
            
            result[0] = 1;
            result[row] = 1;
            
            for (int col = 1; col < row; ++ col) {
                
                if (row > 1) {
                    result[col] = lastRow[col - 1] + lastRow[col];
                }
            }
        }
        
        return result;
    }
};
```