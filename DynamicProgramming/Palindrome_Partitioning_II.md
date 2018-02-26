# Palindrome Partitioning II

Tags: String, Dynamic Programming, Hard

## Problem

Given a string *s*, partition *s* such that every substring of the partition is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of *s*.

For example, given *s* = `"aab"`,
Return `1` since the palindrome partitioning `["aa","b"]` could be produced using 1 cut.

## Solution 1  

这道题一开始我是用 Palindrome Partitioning 的 backtracking 思路递归来解，果不其然在字符串较长时TLE，不妨贴出来看看

### TLE

```cpp
class Solution {
public:
    int minCut(string s) {
        if (s.empty()) return 0;
        
        int ans = 0x7fffffff;
        backtracking(s, 0, 0, ans);
        return ans;
    }
    
    void backtracking(string& s, int start, int current, int& ans) {
        if (isPalindrome(s, start, s.size() - 1)) {
            ans = min(ans, current);
            return;
        } 
        for (int i = start; i < s.size(); ++i) {
            if (isPalindrome(s, start, i)) {
                backtracking(s, i + 1, current + 1, ans);
            }
        }
    }
    
    bool isPalindrome(const string& s, int start, int end) {
        while (start < end) {
            if (s[start++] != s[end--]) return false;
        }
        return true;
    }
};
```

面对这种需要寻找 min max 之类的题目要能联想到 DP，建立 一维 DP，dp[i] 表示前 i 个字符组成的字符串所需要的最小切割数。

状态转移方程 `dp[i] = min(dp[j] + 1), where j < i and substr[j, i] is palindrome` 即 对所有 0<=j<i，且s[j: i-1]为palindrome。这个还是比较难理解的，注意一般这种一维 dp 的 i 都是表示元素的个数，和最后的下标刚好是相差 1 的。我们在求 dp[i] 的时候，从 s.substr(i -1) 开始判断是否是回文，若是回文的话，此时的分割数就应该是 dp[i - 1] + 1，因为把最后一个新添的元素单独划分出去了。这也就是 j <= i - 1 的缘故，然后 --j ，直到 j = 0。然而这里又有一个很坑的地方，我们一定需要把 dp[0] 赋值为 -1，因为，当 s.substr(0, i) 都为回文的话，就说明了新加的字符和原来整体本身就构成了回文字符串，所以也就不需要分割了，输出结果应该是 0 。套用之前的 dp[0] + 1，此时只有 dp[0] = -1 的时候，才能凑出来 0。

另外，因为要求最小值，所以除了 dp[0] 其余都应该初始化为最大的0x7fffffff

### TLE

```cpp
class Solution {
public:
    int minCut(string s) {
        if (s.empty()) return 0;
        int n = s.size();
        vector<int> dp(n + 1, 0x7fffffff);
        dp[0] = -1;
        
        for (int i = 1; i <= n; ++i) {
            for (int j = i - 1; j >= 0; --j) {
                if (isPalindrome(s, j, i - 1)) {
                    dp[i] = min(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[n];
    }
    
    bool isPalindrome(const string& s, int start, int end) {
        while (start < end) {
            if (s[start++] != s[end--]) return false;
        }
        return true;
    }    
};
```

然而上面的实现又超时了，败给了最后一个超长字符串。

[解答1](https://algorithm.yuanbin.me/zh-hans/dynamic_programming/palindrome_partitioning_ii.html) [解答2](http://bangbingsyb.blogspot.com/2014/11/leetcode-palindrome-partitioning-i-ii.html) 在参考了这两篇解答之后，发现虽然我们在分割字符串的时候已经用了dp，看似优化空间不大，但是算算时间复杂度，由于判断回文的复杂度为 $$O(n)$$, 两个 for loop 嵌套，为 $$O(n^2)$$ ，所以总的时间复杂度为 $$O(n^3)$$ 

我们只能在判断回文字符串这里做文章了，而且综合来看整个过程，这部分的确有大量重复的计算。发现这部分也可以用 dp 来做。

```
定义状态 PaMat[i][j] 为区间 [i,j] 是否为回文的标志, 对应此状态的子问题可从回文的定义出发，如果字符串首尾字符相同且在去掉字符串首尾字符后字符串仍为回文，则原字符串为回文，相应的状态转移方程 PaMat[i][j] = s[i] == s[j] && PaMat[i+1][j-1], 由于状态转移方程中依赖比i大的结果，故实现中需要从索引大的往索引小的递推，另外还需要考虑一些边界条件和初始化方式，做到 bug-free 需要点时间。

```

具体实现要注意的细节还挺多，感觉还是挺难的

```cpp
class Solution {
public:
    int minCut(string s) {
        if (s.empty()) return 0;
        int n = s.size();
        
        vector<vector<bool>> paMat(n, vector<bool> (n, false));
        for (int i = n - 1; i >= 0; --i) {
            for (int j = i; j < n; ++j) {
                if (i == j) paMat[i][j] = true;
                else if (i + 1 == j) paMat[i][j] = s[i] == s[j];
                else paMat[i][j] = s[i] == s[j] && paMat[i + 1][j - 1];
            }
        }
        
        vector<int> dp(n + 1, 0x7fffffff);
        dp[0] = -1;
        
        for (int i = 1; i <= n; ++i) {
            for (int j = i - 1; j >= 0; --j) {
                if (paMat[j][i - 1]) {
                    dp[i] = min(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[n];
    } 
};
```

可以进一步把两个 for loop 合到一起 [参考](https://leetcode.com/problems/palindrome-partitioning-ii/discuss/42199/My-DP-Solution-(-explanation-and-code)) 但我感觉还是分开写清楚点，不要为了合并而合并。

下面为了合并，先遍历了列，再遍历了行。

```cpp
class Solution {
public:
    int minCut(string s) {
        const int N = s.size();
        if(N<=1) return 0;
        int i,j;
        bool isPalin[N][N];
        fill_n(&isPalin[0][0], N*N, false);
        int minCuts[N+1];
        for(i=0; i<=N; ++i) minCuts[i] = i-1;

        for(j=1; j<N; ++j)
        {
            for(i=j; i>=0; --i)
            {
                if( (s[i] == s[j]) && ( ( j-i < 2 ) || isPalin[i+1][j-1] ) )
                {
                    isPalin[i][j] = true;
                    minCuts[j+1] = min(minCuts[j+1], 1 + minCuts[i]);
                }
            }
        }
        return minCuts[N];

    }
};
```



### 复杂度

每个 for loop 都是 $$O(n^2)$$，所以最终时间复杂度 $$O(n^2)$$



## Solution 2 -

还有另一种据说更快的解法？

[参考1](https://leetcode.com/problems/palindrome-partitioning-ii/discuss/42212/Two-C++-versions-given-(one-DP-28ms-one-Manancher-like-algorithm-10-ms))

[参考 2](https://leetcode.com/problems/palindrome-partitioning-ii/discuss/42198/My-solution-does-not-need-a-table-for-palindrome-is-it-right-It-uses-only-O(n)-space.)