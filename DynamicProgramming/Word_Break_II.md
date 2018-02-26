# Word Break II

Tags: String, Dynamic Programming, Backtracking, Medium

## Problem

Given a **non-empty** string *s* and a dictionary *wordDict* containing a list of **non-empty** words, add spaces in *s* to construct a sentence where each word is a valid dictionary word. You may assume the dictionary does not contain duplicate words.

Return all such possible sentences.

For example, given
*s* = `"catsanddog"`,
*dict* = `["cat", "cats", "and", "sand", "dog"]`.

A solution is `["cats and dog", "cat sand dog"]`.

## Solution

这道题还是比较典型的 backtracking 问题，先看看纯 backtracking 的实现。

没有做任何优化，存在大量的重复计算，碰见下面这样的例子就直接 TLE 了

```
"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
["a","aa","aaa","aaaa","aaaaa","aaaaaa","aaaaaaa","aaaaaaaa","aaaaaaaaa","aaaaaaaaaa"]
```

### TLE

```cpp
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        if (s.empty()) return vector<string> ();
        unordered_set<string> wordSet;
        for (string& str : wordDict) wordSet.insert(str);
        vector<string> ans;
        vector<string> curr;
        
        help(s, wordSet, 0, curr, ans);
        return ans;
    }
    
    void help(string& s, unordered_set<string>& wordSet, int start, vector<string>& curr, vector<string>& ans) {
        if (start == s.size()) {
            string tmp = curr[0];
            for (int i = 1; i < curr.size(); ++i) {
                tmp.push_back(' ');
                tmp.append(curr[i]);
            }
            ans.push_back(tmp);
            return;
        }
        
        for (int i = start; i < s.size(); ++i) {
            string sub = s.substr(start, i + 1 - start);
            if (wordSet.count(sub)) {
                curr.push_back(sub);
                help(s, wordSet, i + 1, curr, ans);
                curr.pop_back();
            }
        }
    }
};
```

### 错误实现

```cpp
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        if (s.empty()) return vector<string> ();
        unordered_set<string> wordSet;
        for (string& str : wordDict) wordSet.insert(str);
        vector<string> ans;
        vector<string> curr;
        
        int n = s.size();
        vector<bool> dp(n + 1, false);
        dp[0] = true;
        for (int i = 1; i <= n; ++i) {
            for (int j = i - 1; j >= 0; --j) {
                if (wordSet.count(s.substr(j, i - j)) && dp[j]) dp[i] = true;
            }
        }
        
        help(s, wordSet, 0, curr, ans, dp);
        return ans;
    }
    
    void help(string& s, unordered_set<string>& wordSet, int start, vector<string>& curr, vector<string>& ans, vector<bool>& dp) {
        if (start == s.size()) {
            string tmp = curr[0];
            for (int i = 1; i < curr.size(); ++i) {
                tmp.push_back(' ');
                tmp.append(curr[i]);
            }
            ans.push_back(tmp);
            return;
        }
        
        for (int i = start; i < s.size(); ++i) {
            if (dp[i + 1]) {
                curr.push_back(s.substr(start, i - start + 1));
                help(s, wordSet, i + 1, curr, ans, dp);
                curr.pop_back();
            }
        }
    }
};
```

像如下的例子就会输出完全错误的解答

```
Your input
"catsanddog"
["cat","cats","and","sand","dog"]

Your answer
["cat s and dog","cat s anddog","cat sand dog","cat sanddog","cats and dog","cats anddog","catsand dog","catsanddog"]anddog","catsand dog","catsanddog"]

Expected answer
["cats and dog","cat sand dog"]
```

原因是我们只是把 word break 里的 一维 dp 直接拿过来用了，但实际上一维 dp 仅仅标识了从开头到 i - 1 的子串是否能在字典里查到，而具体怎么分割并没有关注，这样一来在输出答案的时候就会把很多错误分割也给囊括进来了。

这个题还是太难了。。[参考](https://leetcode.com/problems/word-break-ii/discuss/44178/11ms-C++-solution-(concise)) 

牛客网的输出顺序实在是拿不准，还有用 dp 来解的，后续再补充吧。。

```cpp
class Solution {
public:
    unordered_map<string, vector<string>> map;
    
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        if (s.empty()) return vector<string> ();
        unordered_set<string> wordSet;
        for (string& str : wordDict) wordSet.insert(str);
        
        return help(s, wordSet);
    }
    
    vector<string> help(string s, unordered_set<string>& wordSet) {
        if (map.count(s)) return map[s];
        vector<string> result;
        if (wordSet.count(s)) result.push_back(s);
        
        for (int i = 1; i < s.size(); ++i) {
            string word=s.substr(i);

            if (wordSet.count(word)) {
                string rest = s.substr(0,i);
                vector<string> prev = combine(word, help(rest,wordSet));
                result.insert(result.end(), prev.begin(), prev.end());
            }
        }
        map[s] = result;
        return result;
    }

    vector<string> combine(string word, vector<string> prev){
        for(int i=0;i<prev.size();++i){
            prev[i]+=" "+word;
        }
        return prev;
    }
};
```

