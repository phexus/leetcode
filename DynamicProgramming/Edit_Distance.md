# Edit Distance

Tags: String, Dynamic Programming, Hard

## Problem

Given two words *word1* and *word2*, find the minimum number of steps required to convert *word1* to *word2*. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

a) Insert a character
b) Delete a character
c) Replace a character

## Solution

这道题如果不往 DP 的方向去思考，就会觉得难以入手，因为 从一个 字符串 变成 另一个字符串 的所可以做的改变实在是太多了，而 DP 最大的优势在于把复杂的问题拆解成一个一个小问题，而后面的问题依赖前面的问题递归来解决。本题还是典型的双序列动态规划。

这篇 [discuss](https://leetcode.com/problems/edit-distance/discuss/25846/20ms-Detailed-Explained-C++-Solutions-(O(n)-Space)) 对于本问题讲解的非常透彻，下面是参考思路。

`dp[i][j]` 代表从 `word1[0..i - 1]` to `word2[0..j - 1]` 的编辑距离。

boundary case 比较简单 ：

1. `dp[i][0] = i`;
2. `dp[0][j] = j`.

general case：

重点研究 `dp[i - 1][j - 1]` 和 `dp[i][j]`，几乎和 Distinct_Subsequences 一题是一样的思路，去考虑  `word[i - 1]` and `word2[j - 1]` 是否相等来分情况讨论：

1.  `word[i - 1] = word2[j - 1]` ，那么不需要做任何的改变，他们完全一样，所以 `dp[i][j] = dp[i - 1][j - 1]`
2. `word[i - 1] != word2[j - 1]` ，又要分三种情况来讨论：
   - 用 `word2[j - 1]` 替换 `word1[i - 1]`，那么 `dp[i][j] = 1 + dp[i - 1][j - 1]`
   - 删除  `word1[i - 1]` 使得 `word1[0..i - 2] = word2[0..j - 1]` ，那么 `dp[i][j] = dp[i - 1][j] + 1`
   - 增加 `word2[j - 1]` 到  `word1[0..i - 1]` 后面，使得 `word1[0..i - 1] + word2[j - 1] = word2[0..j - 1]` ， 那么 ` dp[i][j] = dp[i][j - 1] + 1`

上面的第一张替换还是比较好理解的，而后两种就要点难了。其实动态规划之所以叫动态规划，我个人觉得还有一层意思，就是要让我们动态地来看问题。以删除为例，删除  `word1[i - 1]` 之所以能使得 `word1[0..i - 2] = word2[0..j - 1]` 是因为我们借助了之前的状态 `dp[i - 1][j]` ，其实上面这样表述还是有些问题的， `word1[0..i - 2] = word2[0..j - 1]` 当然不一定就相等，但是在 `dp[i - 1][j]` 的影响下总会以最小的代价使得他们相等，这样我们删除了新添加的 `word1[i - 1]` 自然就恢复到了 `dp[i - 1][j]` 所作用下的局面，而删除本身这个操作带来了代价 1；加法 同理。

不过需要注意的是，虽然上面罗列了 3 种做法，由于我们要求最短的，所以要取其中最小的即可。

上面的解释告诉我们对于这种问题我们只需要考虑临近的状态就好了，具体这种状态所对应的局面到底是怎样的，我们其实并不是很关心。

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();
        
        vector<vector<int>> dp(m + 1, vector<int> (n + 1， 0));
        for (int i = 0; i <= m; ++i) dp[i][0] = i;
        for (int j = 0; j <= n; ++j) dp[0][j] = j;
        
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = 1 + min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1]));
                }
            }
        }
        
        return dp[m][n];
    }
};
```

### 优化

综合分析，发现 `dp[i][j] ` 的状态取决于 `dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1])`  ，如果要按列来遍历降维的话，也就是上一轮的 j - 1， j ，以及这一轮的 j - 1，为了这一轮的 j - 1，只好从左往右遍历，但是又得记录上一轮的 j - 1 和 j ，这样又会被刷新掉，有冲突。

leetcode 的解法非常机制，按列不行，那么我么就按行来，按行来的话，只要保留上一轮的 i - 1就好了，下面的解法用 pre 来提前存了相关变量，注意 pre 初始化和变更的时机，还有因为只有一维，所以不要忘了 外层 loop 要置位 dp[0]

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();
        
        vector<int> dp(m + 1, 0);
        for (int i = 0; i <= m; ++i) dp[i] = i;
        int pre = 0;
        
        for (int j = 1; j <= n; ++j) {
            pre = dp[0];
            dp[0] = j;
            
            for (int i = 1; i <= m; ++i) {
                int tmp = dp[i];
                
                if (word1[i - 1] == word2[j - 1]) dp[i] = pre;
                else dp[i] = 1 + min(pre, min(dp[i - 1], dp[i]));
                
                pre = tmp;
            }
        }
        return dp[m];
    }
};
```

