# Distinct Subsequences

Tags: String, Dynamic Programming, Hard

## Problem

Given a string **S** and a string **T**, count the number of distinct subsequences of **S** which equals **T**.

A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, `"ACE"` is a subsequence of `"ABCDE"` while `"AEC"` is not).

Here is an example:
**S** = `"rabbbit"`, **T** = `"rabbit"`

Return `3`.

## Solution 1 - 递归 - 深搜 - TLE

这种解法是超时了的。但是它的思想有点想之前的 backtracking 的剪枝，这不过这里由于要求 subsequence，子串可以是不连续的，所以传递的参数不是引用，而是拷贝，这样就不用回退了。

逐个比较 s 和 t 的首字符，相等的话就都往后走一格递归调用自身，不等的话，s 往后走一格，直到 t 被比完。

画出递归调用栈，就比较好理解了。只有 s.size() >= t.size() 才会进循环

t 为空串时返回 1，因为空串本身就是任何一个串的子序列。

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        if (t.empty()) return 1;
        if (s.empty()) return 0;
        if (s.size() < t.size()) return 0;
        
        int ans = 0;
        
        for (int i = 0; i < s.size(); ++i) {
            if (s[i] == t[0]) {
                ans += numDistinct(s.substr(i + 1), t.substr(1));
            }
        }
        
        return ans;
    }
};
```

### 复杂度

最好情况是 s 和 t 没有相同的字符，此时时间复杂度 $$O(n)$$

最差情况下，s 和 t 完全相同，有大量重复的计算，时间复杂度很高

## Solution 2 - DP

上面的做法存在较多的重叠子状态，这种两个串的模式匹配问题要联想到用动态规划来做。[参考](https://algorithm.yuanbin.me/GLOSSARY.html#dptwosequence) 给出了双序列动规的思路。

双序列动规一般用于解决有两个数组或两个字符串，计算其匹配关系，可以用 `dp[i][j]` 来表示前一个数组前 i 位 与后一个数组的前 j 位之间的子问题关系。

套用到这道题里，有两种思路，即到底由 s 来做行还是由 t 来做行。一般在做递推时每行每行的处理更加符合直觉，所以这里的选择还是比较关键的。

先讨论 s 做行的情况，即定义 `dp[i][j]` 为 s[0 , i] 中 t[0, j] 的子序列的个数，注意这里的 i j 均表示长度而不是下标。摘自 [参考](https://algorithm.yuanbin.me/GLOSSARY.html#dptwosequence) 给出的分析：

```
状态转移应从 dp[i-1][j],dp[i-1][j-1], dp[i][j-1] 中寻找，接着寻找突破口——dp[i] 和 dp[j] 的关系。

1. S[i] == T[j]: 两个字符串的最后一个字符相等，我们可以选择 S[i] 和 T[j] 配对，那么此时有 dp[i][j] = dp[i-1][j-1]; 若不使 S[i] 和 T[j] 配对，而是选择 S[0:i-1] 中的某个字符和 T[j] 配对，那么 dp[i][j] = dp[i-1][j]. 综合以上两种选择，可得知在S[i] == T[j]时有 dp[i][j] = dp[i-1][j-1] + dp[i-1][j]
2. S[i] != T[j]: 最后一个字符不等时，S[i] 不可能和 T[j] 配对，故 dp[i][j] = dp[i-1][j]

为便于处理第一个字符相等的状态(便于累加)，初始化dp[i][0]为1, 其余为0. 空串是任何字符串的子串。
```

感觉上面的解析中不好理解的一点是 为什么 S[i] == T[j] 时，会有两个状态叠加。首先当相等时，我们当然可以就然它俩配对，但是一旦这样选择之后，实际上并没有增加子串的个数，也就后之前的 `dp[i - 1][j - 1]` 的值是一样的，所以这部分的值一定要累积；另外一个选择就是 s[i] 和 t[j] 不配对，那么新增的 s[i] 我们不去管到底和 s[0 , i - 1] 的哪个字符匹配（甚至不匹配都可以，感觉上面的解析不清晰），这样新增的这个字符就跟没增前一样，但是 j 要增加的，所以就是 `dp[i-1][j]` 。最终结果是 `dp[i][j] = dp[i - 1][j - 1] + dp [i - 1][j]`

若是不相等，那么一定无法匹配了，所以也只可能是 `dp[i][j] = dp[i - 1][j]` 了。

到具体实现时，发现当前状态和上一轮状态和上一轮的前一个状态相关，所以就从上往下，从左往右遍历即可。

因为 i j 代表的是长度，所以初始化长度为 size + 1

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        if (t.empty()) return 1;
        if (s.empty()) return 0;
        if (s.size() < t.size()) return 0;
        
        vector<vector<int>> dp(s.size() + 1, vector<int> (t.size() + 1, 0));
        dp[0][0] = 1; // 0 0 单独处理为 1
        for (int i = 1; i <= s.size(); ++i) {
            dp[i][0] = 1; // 第一列代表 t 为空串
            for (int j = 1; j <= t.size(); ++j) {
                if (s[i - 1] == t[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        
        return dp[s.size()][t.size()];
    }
};
```

### 优化

同样可以改用滚动数组来优化空间复杂度。这种优化都是有套路的，注意好不要把参与转移的状态误刷新掉就好，这代替就得倒着来，从右往左。

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        if (t.empty()) return 1;
        if (s.empty()) return 0;
        if (s.size() < t.size()) return 0;
        
        vector<int> dp(t.size() + 1, 0);
        dp[0] = 1;
        for (int i = 1; i <= s.size(); ++i) {
            for (int j = t.size(); j >= 0; --j) {
                if (s[i - 1] == t[j - 1]) {
                    dp[j] += dp[j - 1];
                }
            }
            dp[0] = 1;
        }
        
        return dp[t.size()];
    }
};
```

### 另一种思路，行列刚好相反

[discuss](https://leetcode.com/problems/distinct-subsequences/discuss/37327/Easy-to-understand-DP-in-Java) 

[参考](http://blog.csdn.net/abcbc/article/details/8978146)

这里把 t 作为行了，可以通过做图来看出其中的规律，比较直观

```
    r a b b b i t

  1 1 1 1 1 1 1 1

r 0 1 1 1 1 1 1 1

a 0 0 1 1 1 1 1 1

b 0 0 0 1 2 3 3 3

b 0 0 0 0 1 3 3 3

i 0 0 0 0 0 0 3 3

t 0 0 0 0 0 0 0 3 
```

```
从这个表可以看出，无论T的字符与S的字符是否匹配，dp[i][j] = dp[i][j - 1].就是说，假设S已经匹配了j - 1个字符，得到匹配个数为dp[i][j - 1].现在无论S[j]是不是和T[i]匹配，匹配的个数至少是dp[i][j - 1]。除此之外，当S[j]和T[i]相等时，我们可以让S[j]和T[i]匹配，然后让S[j - 1]和T[i - 1]去匹配。所以递推关系为：

dp[0][0] = 1; // T和S都是空串.

dp[0][1 ... S.length() - 1] = 1; // T是空串，S只有一种子序列匹配。

dp[1 ... T.length() - 1][0] = 0; // S是空串，T不是空串，S没有子序列匹配。

dp[i][j] = dp[i][j - 1] + (T[i - 1] == S[j - 1] ? dp[i - 1][j - 1] : 0).1 <= i <= T.length(), 1 <= j <= S.length()
```

```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        vector<vector<int>> dp(t.size() + 1, vector<int> (s.size() + 1));
        
        for (int i = 0; i <= s.size(); ++i) dp[0][i] = 1;
        
        for (int i = 1; i <= t.size(); ++i) {
            for (int j = 1; j <= s.size(); ++j) {
                dp[i][j] = dp[i][j - 1] + (s[j - 1] == t[i - 1] ? dp[i - 1][j - 1] : 0);
            }
        }
        
        return dp[t.size()][s.size()];
    }
};
```

