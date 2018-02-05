# Unique Binary Search Trees

Tags: Tree, Medium, Math

## Problem

Given n, how many structurally unique BST's (binary search trees) that store values 1...n?

For example,
Given n = 3, there are a total of 5 unique BST's.

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

## 解法1 - 递推 - 双重 for 循环

本题与动态规划有着紧密的关系，初次做的时候完全没有思路，在参考了[解法](https://algorithm.yuanbin.me/zh-hans/math_and_bit_manipulation/unique_binary_search_trees.html)后顿时豁然开朗。
`以i为根节点的树，其左子树由[0, i-1]构成， 其右子树由[i+1, n]构成。`
动态规划状态为 count[i], count[i] 表示到正整数 i 为止的二叉搜索树个数。如果以 i 作为根节点，由基本的排列组合知识可知，其唯一 BST 个数为左子树的 BST 个数乘上右子树的 BST 个数。

count[0] = 1
count[1] = 1
count[2] = 2
coutn[3] =  count[0] * count[2] +
            count[1] * count[1] +
            count[2] * count[0] = 5

`对于两组有序数列「1, 2, 3] 和 [4, 5, 6]来说，这两个有序数列分别组成的 BST 个数必然是一样的，因为 BST 的个数只与有序序列的大小有关，而与具体值没有关系。`
可以得到以下递推公式：
$$count[i] = \sum _{j = 0} ^{i - 1} (count[j] \cdot count[i - j - 1])$$

```cpp
class Solution {
public:
    int numTrees(int n) {
        if (n < 0) return -1;
        
        vector<int> count(n + 1);
        count[0] = 1;
        
        for (int i = 1; i <= n; ++ i) {
            for (int j = 0; j < i; ++ j) {
                count[i] += count[j] * count[i - 1 - j];
            }
        }
        
        return count[n];
    }
};
```

### 源码分析

1. n < 0 为异常情况，这里返回没有意义的 -1
2. 初始化 count 长度为 n + 1，因为 count[0] 是有意义的，同时也应该把 count[0] 单独初始化为 1

### 复杂度

时间复杂度 $$O(n^2)$$
空间复杂度 $$O(n)$$

## 解法2 - 递归 - Time Limit Exceeded

这样做实际上会超时，不建议用递归来做。

```cpp
class Solution {
public:
    int numTrees(int n) {
        if (n < 0) return -1;
        if (n <= 1) return 1;
        
        int result = 0;
        for (int i = 0; i < n; ++ i) {
            result += numTrees(i) * numTrees(n - 1 - i);
        }
        
        return result;
    }
};
```

##　解法３ - 卡特兰数 - 待研究

此题为 Catalan number 的一种，除了平方时间复杂度的解法外还存在 $$O(n)$$ 的解法 [Wikipedia](http://en.wikipedia.org/wiki/Catalan_number)

~~然而 nowcoder 的几个解法在 leetcode 都过不了，很是尴尬，之后有空再研究下~~

发现问题原因了。。在leetcode上要用 double型来存，不然会溢出（估计是 noecode 的编译器与 leetcode 不同）

```
链接：https://www.nowcoder.net/questionTerminal/b2b6734cbc0b43088f6084785046b861
来源：牛客网

//参考卡特兰数
//递推公式：令h(0)=1,h(1)=1，catalan数满足递推式[2]  ：
//h(n)= h(0)*h(n-1)+h(1)*h(n-2) + ... + h(n-1)*h(0) (n>=2)
//例如：h(2)=h(0)*h(1)+h(1)*h(0)=1*1+1*1=2
//h(3)=h(0)*h(2)+h(1)*h(1)+h(2)*h(0)=1*2+1*1+2*1=5
//另类递推式[3]  ：
//h(n)=h(n-1)*(4*n-2)/(n+1);
```

```cpp
class Solution {
public:
    int numTrees(int n) {
        vector<double> dp(n + 1);
        dp[0] = 1;
        
        for(int i = 1;i <= n;i ++) {
            dp[i] = dp[i - 1] * (4 * i - 2) / (i + 1);
        }
        return int(dp[n]);
    }
};
```
