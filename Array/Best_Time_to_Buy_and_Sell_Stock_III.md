# Best Time to Buy and Sell Stock III

Tags: Array, Hard, DP

## Question

- leetcode: [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)
- nowcoder: [Best Time to Buy and Sell Stock III](https://www.nowcoder.com/practice/03905f7b819241398b02ee39bef3e8f1?tpId=46&tqId=29057&rp=4&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete at most two transactions.

Note:
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

## 题解1 - 转换为累加问题

最多买卖两次，需要找出收益最高的两次。多重遍历会很复杂，难以理清逻辑。可以将问题转化为 “求数组中其中四个数和的最大值”，不同之处是部分元素会有负号。像是找4个索引满足min_idx1 <= max_idx1 <= min_idx2 <= max_idx2，且
要使得对应的（-min1）+ max1 +（-min2）+ max2最大。

```cpp
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        const int length = prices.size();
        
        if (length < 2) return 0;
        
        int buy1 = INT_MIN;
        int sell1 = 0;
        int buy2 = INT_MIN;
        int sell2 = 0;
        
        for (int i = 0; i < length; ++ i) {
            buy1 = max(buy1, - prices[i]);
            sell1 = max(sell1, buy1 + prices[i]);
            buy2 = max(buy2, sell1 - prices[i]);
            sell2 = max(sell2, buy2 + prices[i]);
        }
        
        return sell2;
    }
};
```

### 源码分析

[参考资料](https://www.nowcoder.com/questionTerminal/03905f7b819241398b02ee39bef3e8f1)

buy1 保存目前为至最便宜的股价，sell1 表示之前所有天只进行一次买卖的最大收益，buy2 表示已经交易过一次后买第二只股票后的收益，sell2 表示 2 次完整交易的最大利润。
由于是按照 buy1，sell1，buy2，sell2 顺序更新的，每个值都会跟原来的自身值对比更新，只会存最划算的投资，不会出现新的收益影响污染以前收益的情况；另外，其实顺序也是可以调整的，leetcode 的 discuss 的解法如下：

```cpp
public class Solution {
    public int maxProfit(int[] prices) {
        int hold1 = Integer.MIN_VALUE, hold2 = Integer.MIN_VALUE;
        int release1 = 0, release2 = 0;
        for(int i:prices){                              // Assume we only have 0 money at first
            release2 = Math.max(release2, hold2+i);     // The maximum if we've just sold 2nd stock so far.
            hold2    = Math.max(hold2,    release1-i);  // The maximum if we've just buy  2nd stock so far.
            release1 = Math.max(release1, hold1+i);     // The maximum if we've just sold 1nd stock so far.
            hold1    = Math.max(hold1,    -i);          // The maximum if we've just buy  1st stock so far. 
        }
        return release2; ///Since release1 is initiated as 0, so release2 will always higher than release1.
    }
}

```

### 复杂度分析

时间复杂度是 $$O(n)$$, 空间复杂度为 $$O(1)$$.

## 题解2 - DP

[参考资料](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/discuss/39608)

这里现贴出 DP 的解法，打一个 tag 标签，后续做 DP 时再具体探讨

```cpp
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        // f[k, ii] represents the max profit up until prices[ii] (Note: NOT ending with prices[ii]) using at most k transactions. 
        // f[k, ii] = max(f[k, ii-1], prices[ii] - prices[jj] + f[k-1, jj]) { jj in range of [0, ii-1] }
        //          = max(f[k, ii-1], prices[ii] + max(f[k-1, jj] - prices[jj]))
        // f[0, ii] = 0; 0 times transation makes 0 profit
        // f[k, 0] = 0; if there is only one price data point you can't make any money no matter how many times you can trade
        if (prices.size() <= 1) return 0;
        else {
            int K = 2; // number of max transation allowed
            int maxProf = 0;
            vector<vector<int>> f(K+1, vector<int>(prices.size(), 0));
            for (int kk = 1; kk <= K; kk++) {
                int tmpMax = f[kk-1][0] - prices[0];
                for (int ii = 1; ii < prices.size(); ii++) {
                    f[kk][ii] = max(f[kk][ii-1], prices[ii] + tmpMax);
                    tmpMax = max(tmpMax, f[kk-1][ii] - prices[ii]);
                    maxProf = max(f[kk][ii], maxProf);
                }
            }
            return maxProf;
        }
    }
};
```

### 复杂度分析

时间复杂度是 $$O(kn)$$, 空间复杂度为 $$O(kn)$$.