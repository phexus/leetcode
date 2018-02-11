# Wildcard Matching

Tags: String, Hard, DP

## Problem

Implement wildcard pattern matching with support for `'?'` and `'*'`.

```
'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "*") → true
isMatch("aa", "a*") → true
isMatch("ab", "?*") → true
isMatch("aab", "c*a*b") → false
```
## Solution 1 - 回溯

初做本题时，感觉跟 Regular_Expression_Matching 一题类似，也想着用 DFS 递归着来做，然而写出了下面 TLE 的代码：

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        if (p.empty()) return s.empty();
        
        bool first_match = (!s.empty()) && (s[0] == p[0] || p[0] == '?' || p[0] == '*');
        
        if (p[0] == '*') {
            return isMatch(s, p.substr(1)) || (first_match && isMatch(s.substr(1), p));
        } else {
            return first_match && isMatch(s.substr(1), p.substr(1));
        }
    }
};
```

试了几个例子，都没问题，然而提交的时候，下面这个例子超时了，真是忧伤。。

```
Last executed input:
"aaabbbaabaaaaababaabaaabbabbbbbbbbaabababbabbbaaaaba"
"a*******b"
```

[参考思路题解2（题解1有错）](https://algorithm.yuanbin.me/zh-hans/string/wildcard_matching.html)重新思考这个问题，'`?`'表示匹配单一字符，'`*`'可匹配任意多字符串(包含零个)。要匹配的字符串设为`s`, 模式匹配用的字符串设为`p`，那么如果是普通字符，两个字符串索引向前推进一位即可，如果`p`中的字符是`?`也好办，同上处理，向前推进一位。所以现在的关键就在于如何处理'`*`' 。

因为`*`可匹配0, 1, 2...个字符，所以遇到`*`时，`s`应该尽可能的向前推进，注意到`p`中`*`后面可能跟有其他普通字符，故`s`向前推进多少位直接与`p`中`*`后面的字符相关。同时此时两个字符串的索引处即成为回溯点，如果后面的字符串匹配不成功，则`s`中的索引向前推进，向前推进的字符串即表示和`p`中`*`匹配的字符个数。

下面的解法真的是不可思议的简洁，用 start 来表示 * 的后一个元素，ss 记录当时遇到 * 时，i 的位置。

第一个 if 代表正常匹配，i，j 都 ++，第二个 if 则记录 * 相关的回溯点，第三个 if 则是不能正常匹配时才会进入的逻辑，即回溯，j 回溯到 当时 * 后面的元素，i 则回溯到 ss 后面的一个元素，代表将原来记录的回溯点用 '*' 来匹配，再重新做这一轮的匹配

[nowcoder](https://www.nowcoder.net/questionTerminal/e96f1a44d4e44d9ab6289ee080099322?toCommentId=1162698) 上有比较详细的解释。

```
链接：https://www.nowcoder.net/questionTerminal/e96f1a44d4e44d9ab6289ee080099322?toCommentId=1162698
来源：牛客网

s跟p指针当前位置都是字符的话，没啥好说的，相同就指针后移，不同就失配。
p 指针 当前位置是？的话，直接略过比较，s指针后移。
p 指针 当前位置是*的话，需要尝试不匹配，和匹配多个字符的可能。

这里对*有个策略
比如：
ababaaab a*b*b
p中的*b*有两种匹配可能
第一种：
ababaaab 
*b* 
第二种：
ababaaab
  *b*
这两种都是正确解，而本题只要判断是否匹配即可
所以*号可以按正则里的最少匹配策略来
而且最重要的一点*b*中，假如*b找到了可能的匹配，后续处理中左边的*不需要做任何的回溯，因为右边的*能匹配掉所有之前失配的字符串，简单来说就是只管回溯最右边的*。

不过这方法还是O（NM）复杂度的，但数据比较弱，可以过。
比如：
aaaaaaaaaaaaaaaaaaaaaa *aaaaaaaaab*
每次碰到p串中的b都会回溯，然后*吃掉s串中一个a字符，继续匹配。只要s串或者p串的a足够长，仍然会TLE。
```



```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int i = 0, j = 0, j_recall = 0, i_recall = 0;
        while (s[i]) {
            if (p[j] == '?' || s[i] == p[j]) {++i; ++j; continue;}
            if (p[j] == '*') {i_recall = i; j_recall = ++j; continue;}
            if (j_recall) {i = ++i_recall; j = j_recall; continue;}         
            return false;
        }
        while (p[j] == '*') ++j;
        return !p[j];
    }
};
```

leetcode 上其他解答

```cpp
class Solution {
public:
    // 插曲：发现不少backtracking的题目同时也具备dynamic programming的标签，动态规划是有套路的：最优子结构，子问题重复计算，因为动态规划很多时候就是为了优化递归枚举的效率
    // 个人思路：对s和p分别设置一个游标指示各自当前待处理的字符，假设为i和j，主要看p[j]当前的字符是什么：
    //（1）如果是普通字符且互相匹配，那么i和j均向前进一步即可
    //（2）如果p[j]是?，那么也互相进一步
    //（3）如果p[j]是*，此时应该匹配s的哪一部分？或者不管匹配哪一部分都无法找到合理的匹配方案？另外注意*匹配的是字符序列，序列长度>=0，此处其实也没那么复杂，首先对于连续出现的*，先归一成一个*，然后看s的某个尾部是否匹配到p的*接下来的内容
    // 注意，本题包含这些tag：dynamic programming、backtracking、greedy，说明可以用动态规划、回溯、贪心法求解。
    // 先参考下leetcode-cpp.pdf的解法，注意有哪些地方没想通的，需要反思
    bool isMatch(string s, string p) {
        /* // 下面这些是自己写的
        if (s.empty() || p.empty()) {
            return false;
        }
        return isMatch(s, p, 0, 0);*/
        
        // 递归版会超时，应该还是因为递归导致子问题重复计算
        return isMatch(s.c_str(), p.c_str());        
    }
    
    // 迭代版
    bool isMatch(const char *s, const char *p) {
        bool star = false;
        const char *str, *ptr;
        for (str = s, ptr = p; *str != '\0'; str++, ptr++) {
            switch (*ptr) {
                case '?':
                    break;  // 继续前进即可
                case '*':
                    star = true;
                    s = str, p = ptr;
                    while (*p == '*') {
                        p++;
                    }
                    if (*p == '\0') {
                        return true;
                    }
                    str = s - 1;
                    ptr = p - 1;
                    break;
                default:
                    if (*str != *ptr) {
                        if (!star) {    // 前面没有*，则失败
                            return false;
                        }
                        s++;
                        str = s - 1;
                        ptr = p - 1;
                    }
            }
        }
        while (*ptr == '*') {
            ptr++;
        }
        return (*ptr == '\0');
    }
    
    /* // 递归版，会超时
    bool isMatch(const char *s, const char *p) {
        if (*p == '*') {
            while (*p == '*') {
                p += 1; // 此时p指向下一个*的下一个字符，也就是这里其实是过掉*
            }
            if (*p == '\0') {   // p最后一个字符为*，那么匹配任意序列，此处直接返回成功
                return true;
            }
            // 到这里，相当于是s和p又重新开始了普通的匹配了，这里的思路是对s剩下的序列不断调用isMatch，因为p中出现一个*，所以对于s来说，只要忽略n个字符能实现匹配效果就行，所以这里是不断尝试，看看忽略多少个字符，可以成功，循环退出的条件是s没有到尽头，且当前策略不行
            while (*s != '\0' && !isMatch(s, p)) {
                ++s;
            }
            return *s != '\0';  // 忽略了全部字符还是失败，那么返回匹配失败
        } else if (*p == '\0' || *s == '\0') {  // 其中一个到尽头了，那么另一个也需要到尽头，才能返回true
            return *p == *s;
        } else if (*p == *s || *p == '?') { // ?可以匹配任何字符
            return isMatch(++s, ++p);   // 向前进一步
        } else {    // 返回错误
            return false;
        }
    }*/
    
    /* // 下面这些是自己写的
    bool isMatch(const string& s, const string& p, int i, int j) {
        if (i >= s.size() || j >= p.size()) {
            return false;
        }
        while (p[j] == '*' && j < p.size() - 1 && p[j + 1] == '*') {
            j++;
        }
        
        char c_s = s[i], c_p = p[j];
        if (isEqual(c_s, c_p)) {
            if (i == s.size() - 1 && j == p.size() - 1) {
                return true;
            }
            if (c_p == '*') {
                while (i < s.size() - 1) {
                    if (isMatch(s, p, i + 1, j)) {
                        return true;
                    }
                    return isMatch(s, p, i + 1, j);
                }
            }
            return isMatch(s, p, i + 1, j + 1);
        }
        return false;
    }*/
    
    /* // 下面这些是自己写的
    bool isEqual(char c1, char c2) {
        if (c2 == '*' || c2 == '?') {
            return true;
        }
        if (c1 == c2) {
            return true;
        }
        return false;
    }*/
};
```

## Solution 2 - DP