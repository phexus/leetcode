# Generate Parentheses

Tags: String, Backtracking

## problem

Given *n* pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given *n* = 3, a solution set is:

```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```

## Solution 1 - Backtracking

还是回溯的思想，当 `(` 的个数小于 n 的时候，就可以不断添 `(` ，但是 `)` 的个数是应该 <= `(` 的个数的，所以一旦 rightnum < leftnum ，也就是落下了，就可以添加 `)` 了。

注意下面递归的顺序，一定保证先添加 左括号，再添加 右括号，这样就保证了生成的字符串的正确性。 

遇到这种回溯问题，画出树型图就很好理解了。

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        string s;
        backtracking(n, 0, 0, s, ans);
        return ans;
    }
    
    void backtracking(int n, int leftNum, int rightNum, string& s, vector<string>& ans) {
        if (rightNum == n) {
            ans.push_back(s);
            return;
        } else {
            if (leftNum < n) {
                s.push_back('(');
                backtracking(n, leftNum + 1, rightNum, s, ans);
                s.pop_back();
            }
            
            if (rightNum <  leftNum) {
                s.push_back(')');
                backtracking(n, leftNum, rightNum + 1, s, ans);
                s.pop_back();
            }
        }
    }
};
```

当然也可以不传递引用，这样就不用恢复现场了，更加简洁

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        backtracking(n, 0, 0, "", ans);
        return ans;
    }
    
    void backtracking(int n, int leftNum, int rightNum, string s, vector<string>& ans) {
        if (rightNum == n) {
            ans.push_back(s);
            return;
        } else {
            if (leftNum < n) backtracking(n, leftNum + 1, rightNum, s + "(", ans);
            
            if (rightNum < leftNum) backtracking(n, leftNum, rightNum + 1, s + ")", ans);
        }
    }
};
```

### 复杂度

it turns out this is the `n`-th Catalan number $$\frac{1}{n+1}\binom{2n}{n}$$, which is bounded asymptotically by $$\frac{4^n}{n\sqrt{n}}$$.

- Time Complexity : $$O(\frac{4^n}{\sqrt{n}})$$. Each valid sequence has at most `n` steps during the backtracking procedure.
- Space Complexity : $$O(\frac{4^n}{\sqrt{n}})$$, as described above, and using $$O(n)$$ space to store the sequence.

## Solution 2 - Brute Force





## Solution 3 - Closure Number

