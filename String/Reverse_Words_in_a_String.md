# Reverse Words in a String

Tags: String, Medium

## Problem

Given an input string, reverse the string word by word.

For example,
Given s = "`the sky is blue`",
return "`blue is sky the`".

Clarification:

- What constitutes a word?
  A sequence of non-space characters constitutes a word.
- Could the input string contain leading or trailing spaces?
  Yes. However, your reversed string should not contain leading or trailing spaces.
- How about multiple spaces between two words?
  Reduce them to a single space in the reversed string.

## Solution

本题比 `剑指 offer` 面试题 42 的要求要多很多，翻转后的字符串要去除首、尾的空格，单词间隔部分只允许保留一个空格

下面的解法使用 stl 的迭代器来做的，调用了 stl 的 reverse 方法

由于要把尾部的空格都删掉，所以最后单独处理尾部的最后一个空格。单词之间的空格已经通过 `it = ++wordEnd;` 解决了，wordEnd 指向的是空格，++ 后指向空格后的一个元素。

```cpp
class Solution {
public:
    void reverseWords(string &s) {
        if (s.empty()) return;
        reverse(s.begin(), s.end());
        
        for (auto it = s.begin(); it < s.end(); ) {
            while (*it == ' ') s.erase(it);
            
            auto wordEnd = it;
            while (wordEnd < s.end() && *wordEnd != ' ') ++wordEnd;
            reverse(it, wordEnd);
            it = ++wordEnd;
        }
        
        auto it = s.end() - 1;
        while (*it == ' ') {
            s.erase(it);
            --it;
        }
    }
};
```

上面的解法采用了逐个 erase 的方法，而 erase 本身的时间复杂度就是 $$O(n)$$ ,最差情况下，整体的时间复杂度达到了 $$O(n^2)$$ (虽然 reverse 的时间复杂度也为 $$O(n)$$)

[参考](https://leetcode.com/problems/reverse-words-in-a-string/discuss/47740/In-place-simple-solution) 给出了另一种写法，不单独 erase，而是用两个指针不断重写，能降低一定的时间消耗。

注意这里的 storeIdx 存储的是下一个要存的字母的位置，i 为整体字符串遍历的指针，j 为遍历每个单词时的指针。

最后 `s.erase(s.begin() + storeIdx, s.end());` 即把后面已经遍历玩的元素都删除掉。

```cpp
class Solution {
public:
    void reverseWords(string &s) {
        if (s.empty()) return;
        reverse(s.begin(), s.end());
        
        int storeIdx = 0;
        
        for (int i = 0; i < s.size(); ++i) {
            if (s[i] != ' ') {
                if (storeIdx != 0) s[storeIdx++] = ' ';
                
                int j = i;
                while (j < s.size() && s[j] != ' ') s[storeIdx++] = s[j++];
                reverse(s.begin() + storeIdx - (j - i), s.begin() + storeIdx);
                
                i = j;
            }
        }
        s.erase(s.begin() + storeIdx, s.end());
    }
};
```



