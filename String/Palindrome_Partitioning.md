# Palindrome Partitioning

Tags: Medium, Medium, Backtracking

## Problem

Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

For example, given s = "aab",
Return

[
  ["aa","b"],
  ["a","a","b"]
]

## Solution 1 - recursive - DFS

`如果要求输出所有可能的解，往往都是要用深度优先搜索。如果是要求找出最优的解，或者解的数量，往往可以使用动态规划。`
因为要输出所有可能的解，而每个解必须遍历完整个字符串，所以这里用到了深度优先的思想，用递归实现。

实际上就是 backtracking，做好剪枝处理。

每次去判断 `s.substr(0, i)` 是否是回文，如果是回文，则继续递归调用字符串剩下的部分，`s.substr(i)`，不要忘了在最后要将这次 push 的子串 pop 掉。（因为是递归调用的，深层的调用要返回时证明就已经处理完毕了，而 curr 传递的是引用，如果不 pop 掉，就会修改结果，影响上层）

另外这类判断是否是回文串，用了 string 的内置方法 `rbegin()` 与 `rend()` 返回的是 `reverse_iterator` 类型，通过如下方式就拷贝构造了一个逆序的字符串。

```
    bool isPalindrome(string& s) {
        return s == string(s.rbegin(), s.rend());
    }
```
不过上面的写法要构造逆序字符串，效率不高，最好还是换成下面的解法吧

```cpp
    bool isPalindrome(string& s) {
        int i = 0, j = s.size() - 1;
        while (i < j) {
            if (s[i] != s[j]) return false;
            ++i;--j;
        }
        return true;
    }
```
注意，不能这样写 `dfs(s.substr(i), curr, ans);` 因为这里定义的是一个引用，而 s.substr(i) 是个临时变量，会报错。

```cpp
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        vector<string> curr;
        dfs(s, curr, ans);
        return ans;
    }
    
private:
    
    bool isPalindrome(string& s) {
        return s == string(s.rbegin(), s.rend());
    }
    
    void dfs(string& s, vector<string>& curr, vector<vector<string>>& ans) {
        if (s.empty()) {
            ans.push_back(curr);
            return;
        }
        
        int len = s.size();
        
        for (int i = 1; i <= len; ++i) {
            string sub = s.substr(0, i);
            if (isPalindrome(sub)) {
                curr.push_back(sub);
                string rest = s.substr(i);
                dfs(rest, curr, ans);
                curr.pop_back();
            }
        }
    }
};
```

### 优化

上面的 dfs 传递的是 分割后的字符串，字符串构造是个很花时间的操作，其实可以通过传 index，全部 in-place 来做,能把时间缩短到 6 ms

参考[leetcode解法]()

```cpp
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<vector<string> > ret;
        if(s.empty()) return ret;
        
        vector<string> path;
        dfs(0, s, path, ret);
        
        return ret;
    }
    
    void dfs(int index, string& s, vector<string>& path, vector<vector<string> >& ret) {
        if(index == s.size()) {
            ret.push_back(path);
            return;
        }
        for(int i = index; i < s.size(); ++i) {
            if(isPalindrome(s, index, i)) {
                path.push_back(s.substr(index, i - index + 1));
                dfs(i+1, s, path, ret);
                path.pop_back();
            }
        }
    }
    
    bool isPalindrome(const string& s, int start, int end) {
        while(start <= end) {
            if(s[start++] != s[end--])
                return false;
        }
        return true;
    }
};
```

### 复杂度

状态数最多 $$O(2^{n-1})$$, 故时间复杂度为 $$O(2^n)$$, 使用了current列表，空间复杂度为 $$O(n)$$.

## Solution 2 - DP 

画出递归树后，发现其实有很多计算重复了，

[参考1](https://leetcode.com/problems/palindrome-partitioning/discuss/41982/Java-DP-+-DFS-solution) [参考2](https://leetcode.com/problems/palindrome-partitioning/discuss/41974/My-Java-DP-only-solution-without-recursion.-O(n2))