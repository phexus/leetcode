# Letter Combinations of a Phone Number

Tags: String, Backtracking, Medium

## Problem

Given a digit string, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below.

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

```
Input:Digit string "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

```

**Note:**
Although the above answer is in lexicographical order, your answer could be in any order you want.

## Solution 1 - Backtracking

又是一道标准的回溯题，多了一个数字向字符串的映射，可以用 map 来做，也可以用 vector<string> 来做。

需要注意的是要在 backtracking 里对 错误输入进行处理，`if (strId < 2 || strId > 9) return;` 直接返回就好。

`if (digits.empty()) return vector<string>();` 如果不单独处理，返回的是 `[""]`，leetcode 要求返回 `[]`

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) return vector<string>();
        vector<string> ans;
        string charMap[] = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        
        string candidate;
        backtracking(charMap, digits, 0, candidate, ans);
        return ans;
    }
    
    void backtracking(string charmap[], string& digits, int index, string& candidate, vector<string>& ans) {
        if (candidate.size() == digits.size()) {
            ans.push_back(candidate);
            return;
        } else {
            int strId = digits[index] - '2';
            if (strId < 2 || strId > 9) return; // 对错误输入处理	
            for (int i = 0; i < charmap[strId].size(); ++i) {
                candidate.push_back(charmap[strId][i]);
                backtracking(charmap, digits, index + 1, candidate, ans);
                candidate.pop_back();
            }
        }
    }
};
```

## Solution 2 - Iterative

[参考](https://leetcode.com/problems/letter-combinations-of-a-phone-number/discuss/8090/Iterative-c++-solution-in-0ms)　有非常详细的解释

原理就是不断的把新来的 num 代表的字符串的每一个字符 和 原来的答案进行 重新排列组合。

注意：

1. ans 要初始化一个空字符串元素，是为了能够添加第一个字符。
2. 每做完一轮，把 tmp 换给 ans，两者互换不用创建新的字符串，节省开销，而 ans = tmp 开销较大。

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) return vector<string>();
        vector<string> ans;
        string charMap[] = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        
        ans.push_back(""); // 很关键！！！add a seed for the initial case
        
        for (int i = 0; i < digits.size(); ++i) {
            int num = digits[i] - '0';
            if (num < 2 || num > 9) break;
            
            string& candidate = charMap[num];
            vector<string> tmp;
            for (int j = 0; j < candidate.size(); ++j) {
                for (int k = 0; k < ans.size(); ++k) {
                    tmp.push_back(ans[k] + candidate[j]);
                }
            }
            ans.swap(tmp);
        }

        return ans;
    }
};
```

