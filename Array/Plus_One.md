# Plus One

Tags: Array, Math, Easy

## Question

- leetcode: [Plus One | LeetCode OJ](https://leetcode.com/problems/plus-one/)
- lintcode: [(407) Plus One](http://www.lintcode.com/en/problem/plus-one/)
- nowcoder: [Plus One](https://www.nowcoder.com/practice/4d135ddb2e8649ddb59ee7ac079aa882?tpId=46&tqId=29111&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given a non-negative number represented as an array of digits, plus one to the number.

The digits are stored such that the most significant digit is at the head of the list.

#### Example

Given [1,2,3] which represents 123, return [1,2,4].

Given [9,9,9] which represents 999, return [1,0,0,0].

## 题解 - 整数相加进位

```cpp
class Solution {
public:
    vector<int> plusOne(vector<int> &digits) {
        int carry = 1;
        int length = digits.size();
            
        for (int i = length - 1; i >= 0; -- i) {
            int sum = digits[i] + carry;
            carry = sum / 10;
            digits[i] = sum % 10;
        }
        
        if (carry > 0) {
            digits.insert(digits.begin(), carry);
        }
        
        return digits;
    }
};

// 还可扩展至 plus N，结果用新的数组表示
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        return plusN(digits, 1);
    }
    
    vector<int> plusN(vector<int>& digits, int n) {
        int carry = n;
        vector<int> result(digits.size(), 0);
        
        for (int i = digits.size() - 1; i >= 0; --i) {
            int sum = digits[i] + carry;
            result[i] = sum % 10;
            carry = sum / 10;
        }
        
        if (carry)
            result.insert(result.begin(), carry);
        
        return result;
    }
};
```

### 源码分析

注意将相加的数处理成进位，不必单独区分最后一位。最后要处理最高位的进位问题。

leetcode 上的 Discuss 上还有一种判别每一位是否是 9 的做法，最后在末尾补 0，首位变 1 的解法。但感觉不是很通用，[参考链接](https://leetcode.com/problems/plus-one/discuss/24084)。

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.
