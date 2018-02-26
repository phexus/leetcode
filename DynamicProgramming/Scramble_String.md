# Scramble String

Tags: String, Dynamic Programming, Hard

## Problem

Given a string *s1*, we may represent it as a binary tree by partitioning it to two non-empty substrings recursively.

Below is one possible representation of *s1* = `"great"`:

```
    great
   /    \
  gr    eat
 / \    /  \
g   r  e   at
           / \
          a   t
```

To scramble the string, we may choose any non-leaf node and swap its two children.

For example, if we choose the node `"gr"` and swap its two children, it produces a scrambled string `"rgeat"`.

```
    rgeat
   /    \
  rg    eat
 / \    /  \
r   g  e   at
           / \
          a   t
```

We say that `"rgeat"` is a scrambled string of `"great"`.

Similarly, if we continue to swap the children of nodes `"eat"` and `"at"`, it produces a scrambled string `"rgtae"`.

```
    rgtae
   /    \
  rg    tae
 / \    /  \
r   g  ta  e
       / \
      t   a
```

We say that `"rgtae"` is a scrambled string of `"great"`.

Given two strings *s1* and *s2* of the same length, determine if *s2* is a scrambled string of *s1*.

## Solution 1 - 深搜 - 剪枝

又是一道不看答案，完全没有思路的题，一直想用 DP 来解，然而我尝试的二维 DP 完全找不到状态转移方程。。

下面先探讨递归的解决方案。

分析这道题的时候，不要被表现形式所迷惑，尽管题目明确指出了比较由字符串生成的树，但是我们没有必要去构造这样的树型结构，而是要抓住本质。下面以 “乱序” 来指代这种操作，摘自[参考](http://blog.csdn.net/jin_kwok/article/details/51136509)

```
题意在于判断一个字符串是否为另一个字符串“乱序”得到，这种乱序采用的方式是将一个字符串从某个位置“割开”，形成两个子串，然后对两个子串进行同样的“割开”操作，直到到达叶子节点，无法再分割。然后对非叶子节点的左右孩子节点进行交换，最后重新从左至右组合形成新的字符串，由于这个过程中存在字符位置的变化，因此，原来的字符串顺序可能会被打乱，当然也可能没有（同一个非叶子节点的左右孩子交换0次或偶数次，就无变化）。需要注意的点：

    1、原字符串每次被割开的位置并不确定可能为[1,s.size()-1]，所以必然需要遍历所有可能割开的位置；

    2、原字符串从第i个位置被割开（i在区间[1,s.size()-1]），形成的两个子串s.substr(0,i)和s.substr(i,s.size()-i)，如果这两个子串不全为空，则它们的母串（这里指原字符串）就是所谓的非叶子节点，这两个子串可以左右交换（按照二叉树的展开方式）；对于两个子串，可继续割裂，直到形成叶子节点。

    3、可以想见，原字符串对应的“乱序”串s1，定然满足如下规律：如果将“乱序”串同样从第i个位置割开，他同样可以形成两个子串，s1.substr(0,i)和s1.substr(i,s1.size()-i)，并且满足：

  s1.substr(0,i)是s.substr(0,i)的“乱序”且s1.substr(i,s1.size()-i)是s.substr(i,s.size()-i)的“”乱序

   或者（因为左右交换的原因）

  s1.substr(0,i)是s.substr(s.size()-i)的“乱序”且s1.substr(i)是s.substr(0,s.size()-i)的“乱序”

    4、如上分析，我们就可以将大问题分解成小问题，通过递归调用，便可以判断两个字符串是否互为“乱序”，还需注意一个问题就是“剪枝”操作，二叉树形式分解，层次很深，每一层都应满足（3）中的两种可能之一，如果不满足便不再继续下一层，直接返回false，这便是一种剪枝操作，可以极大的提升效率。

    5、“剪枝”约束，若两个字符串s1和s2互为“乱序”，需满足s1和s2包含的字符及数量应完全相同，如果不同，则不可能构成“乱序”，因此这个条件可作为剪枝条件；

    6、两个工具：判断两个字符串是否包含完全相同的字母，用到了“哈希表”；割裂形成子串用到STL函数：substr(i,n);表示获得字符串从第i个位置开始的n个字符，如果n空缺，默认为到字符串末尾。
```

 具体处理时，没有用 stl 的哈希表，而是直接用一维数组来记录每个字母出现的次数。

i 的 终止条件应该为 i <= s1.size() - 1; 因为这里的 i 代表的是分割长度，以 0 来分割或这是以全部长度来分割就没有意义了，还会陷入死循环。最后如果在 for loop 里都没有返回 true ，结尾应该返回 false ，代表任何一种可能都尝试过了，但是还是没有找到。

```cpp
class Solution {
public:
    bool isScramble(string s1, string s2) {
        if (s1.size() != s2.size()) return false;
        if (s1 == s2) return true;
        
        int count[26] = {0};
        for (int i = 0; i < s1.size(); ++i) {
            ++count[s1[i] - 'a'];
            --count[s2[i] - 'a'];
        }
        
        for (int i = 0; i < 26; ++i) {
            if (count[i] != 0) return false;
        }
        
        for (int i = 1; i <= s1.size() - 1; ++i) {
            if (isScramble(s1.substr(0, i), s2.substr(0, i)) && isScramble(s1.substr(i), s2.substr(i)) ||
                isScramble(s1.substr(0, i), s2.substr(s1.size() - i)) && isScramble(s1.substr(i), s2.substr(0, s1.size() - i)))
                return true;
        }
        return false;
    }
};
```

## Solution 2 - 三维DP

前面提到了尝试二维 DP 无果，是因为本题要用 3 维数组才能表示完。[参考1](https://leetcode.com/problems/scramble-string/discuss/29394/My-C++-solutions-(recursion-with-cache-DP-recursion-with-cache-and-pruning)-with-explanation-(4ms)) [参考2](https://leetcode.com/problems/scramble-string/discuss/29396/Simple-iterative-DP-Java-solution-with-explanation)

`dp[i][j][len]`，表示从s1的第i个字符开始长度为len的子串，和从s2的第j个字符开始长度为len的子串，是否互为scramble。

boundary case：`dp[i][j][1] = s1[i] == s2[j];` 其余都应该初始化为 false

normal case：判断 `dp[i][j][k]` 和解法 1 有点类似，也是要把 k 长度的子串从 1 到 k - 1 一次分割开，两者对比，也是要处理相等和对称这两种情形。

具体实现时，很容易数组越界，如果 for loop 按照 先 i 再 j 再 k 的顺序的话，k 的上界很难确定，因为既要考虑 i 也要考虑 j 。所以下面的做法很机智，把 k 放在了最外层，i 和 j 分别处理 `i + k <= n` 和 `j + k <= n` 就好了，但是要注意是可以取到 n 的。(我在做的时候就因为这个边界问题卡了很久)  原因如下：

在设置 dp 长度时就要充分考虑每一维度代表的意思，i ，j 为下标，所以从 0 取到 n - 1，但是 k 代表长度，所以要从 0 取到 n。如果我们 `i + k <= n` 不取到 n 的话，当 k 取 n 时，i 和 j 就没有能够满足的条件了，这样以来就无法刷新到 `dp[0][0][n]` 了，导致结果一直返回初始值 false。



```cpp
class Solution {
public:
    bool isScramble(string s1, string s2) {
        if (s1.size() != s2.size()) return false;
        if (s1 == s2) return true;
        
        int n = s1.size();
        vector<vector<vector<bool>>> dp(n, vector<vector<bool>> (n, vector<bool> (n + 1, false)));
        
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                dp[i][j][1] = s1[i] == s2[j];
            }
        }
        
        for (int k = 2; k <= n; ++k) {
            for (int i = 0; i + k <= n; ++i) {
                for (int j = 0; j + k <= n; ++j) {
                    for (int l = 1; l < k && !dp[i][j][k]; ++l) { // 已经为 true 就不用做了
                        dp[i][j][k] = dp[i][j][l] && dp[i + l][j + l][k - l] || dp[i][j + k - l][l] && dp[i + l][j][k - l];
                    }
                }
            }
        }
        
        return dp[0][0][n];
    }
};
```

但是，经过测试，dp 的做法在 leetcode 上跑了 32 ms，而递归版本 4 ms就能跑完，大概是 毕竟 4 重循环