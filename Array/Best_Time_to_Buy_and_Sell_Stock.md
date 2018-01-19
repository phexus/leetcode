# Best Time to Buy and Sell Stock

Tags: Array, Easy

## Question

- leetcode: [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)
- nowcoder: [Best Time to Buy and Sell Stock](https://www.nowcoder.com/practice/64b4262d4e6d4f6181cd45446a5821ec?tpId=46&tqId=29059&tPage=4&rp=4&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Say you have an array for which the ith element is the price of a given stock on day i.

If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.

**Example 1:**
Input: [7, 1, 5, 3, 6, 4]
Output: 5

max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)
**Example 2:**
Input: [7, 6, 4, 3, 1]
Output: 0

In this case, no transaction is done, i.e. max profit = 0.

## 题解1 - 笨解法两个 for 循环遍历

这种做法太笨，两个 for 循环带来的时间复杂度太高，

```cpp
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        const int length = prices.size();
        if (length < 2) return 0;
        
        int profit;
        
        for (int i = 0; i < length; ++i) {
            for (int j = i + 1; j < length; ++ j ) {
                if (prices[j] - prices[i] > profit) profit = prices[j] - prices[i];
            }
        }
        
        return profit;
    }
};
```

### 复杂度分析

时间复杂度是 $$O(n^2)$$, 空间复杂度为 $$O(1)$$.

## 题解2 - 一次遍历

考虑只遍历一次的方法，通过保存最小价格，来换取时间效率。

```cpp
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        const int length = prices.size();
        if (length < 2) return 0;
        
        int minPrice = prices[0];
        int profit;
        
        for (int i = 1; i < length; ++ i) {
            if (prices[i] < minPrice) minPrice = prices[i];
            else if (prices[i] > minPrice) profit = max(profit, prices[i] - minPrice);
        }
        
        return profit;
    }
};
```

### 源码分析

保存每个最小值点，更新维护该值后面较大值和最小值的差即可。

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.