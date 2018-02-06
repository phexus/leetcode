# Longest Valid Parentheses

Tags: Stack, Array，Hard

## Problem

Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.

For "(()", the longest valid parentheses substring is "()", which has length = 2.

Another example is ")()())", where the longest valid parentheses substring is "()()", which has length = 4.

本题在 [leetcode solution](https://leetcode.com/problems/longest-valid-parentheses/solution/) 有多达 4 种解法，包括 DP，有人提到用栈来解有时候会超时，推荐用 DP 来解，之后有空再补全。

## 解法1 - stack - last

本题不像上一题，需要返回的是一个连续的合法子串的长度，所以如果 栈中存的还是字符的话就么办法知道一个连续子串的长度。这里还是用到了栈，但是非常机智地存了 该字符在字符串中的 index，这样一来就可以通过比较 index 的差来得到长度。不过需要注意的是 `与栈顶元素之间的距离(因为中间可能有些括号已经被消掉了)，因为栈可能为空，所以需要记录下起始记录点last。` 这里的 last 代表最后一个不满足要求的标号

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        // stack 保存左括弧的 index
        stack<int> st;
        
        int result = 0;
        // last初始值为 -1
        int last = -1;
        
        for (int i = 0; i < s.size(); ++i) {
            // 遇到左括弧就压栈
            if (s[i] == '(') st.push(i);
            else {
                // 栈空，代表之前的都消完了，此时的）是最后一不满足要求的标号，所以 置 last 为 i
                if (st.empty()) last = i;
                else {
                    // 与之前入栈的 （ 消解
                    st.pop();
                    // 此时栈空，需要和 last 相减才是此子串的长度
                    if (st.empty()) result = max(result, i - last);
                    else result = max(result, i - st.top());
                }
            }
        }
        
        return result;
    }
};
```

## 解法2 - stack - 提前入栈 -1

上面的解法虽然比较清晰，但是有多层 if esle 嵌套，写起来还是很容易出错的，这里贴出 leetcode solution 推荐的栈解法。这里充分利用了栈的空间，栈低永远存的是上一题所说的 last，这样以来当 s[i] 为 ） 时，就不需要判断 栈是否为空了，因为此时一定不为空，弹栈之后如果空了，在把当前 标号 入栈（需要注意的是，如果此时栈能为空，一定代表此时的 ) 是没有 ( 和它匹配的，因为栈底的标号不是代表 (）

改写后的逻辑非常清晰，少了一层判断嵌套。

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        stack<int> st;
        st.push(-1);
        int result = 0;
        
        for (int i = 0; i < s.size(); ++i) {
            if (s[i] == '(') st.push(i);
            else {
                st.pop();
                if (st.empty()) st.push(i);
                else result = max(result, i - st.top());
            }
        }
        
        return result;
    }
};
```

###　复杂度

时间复杂度 $$O(n)$$
空间复杂度 $$O(n)$$

