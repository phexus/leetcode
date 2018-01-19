# Best Time to Buy and Sell Stock II

Tags: Array, Easy

## Question

- leetcode: [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

### Problem Statement

Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

## 题解1 - 比较前后两个值，累加所有涨幅

与 Best Time to Buy and Sell Stock 不同，本题允许多次买入卖出。需要注意的是，连续递增可以合起来看为一次买入卖出操作，所以统计所有递增量即可。

```cpp
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        const int length = prices.size();
        
        if (length < 2) return 0;
        
        // 这里必须初始化！！！
        int profit = 0;
        
        for (int i = 1; i < length; ++ i) {
            if (prices[i] > prices[i - 1]) profit += prices[i] - prices[i - 1];
        }
        
        return profit;
    }
};
```

### 源码分析

这里的 `int profit = 0;` 必须初始化，不能直接写成 `int profit;`

《C++ Primer》一书中指出，`内置变量是否自动初始化取决于变量定义的位置，在函数体外定义的变量都初始化为 0，在函数体内定义的内置类型变量不进行自动初始化。`

经过测试，如果 `int profit;` 写在 main() 内或全局变量，会自动初始化为 0，而写在函数体或类里，则为随机值。永远不要依赖未定义行为！！！

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.