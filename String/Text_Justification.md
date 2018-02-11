# Text Justification

Tags: String, Hard

## Problem

Given an array of words and a length *L*, format the text such that each line has exactly *L* characters and is fully (left and right) justified.

You should pack your words in a greedy approach; that is, pack as many words as you can in each line. Pad extra spaces `' '` when necessary so that each line has exactly *L* characters.

Extra spaces between words should be distributed as evenly as possible. If the number of spaces on a line do not divide evenly between words, the empty slots on the left will be assigned more spaces than the slots on the right.

For the last line of text, it should be left justified and no extra space is inserted between words.

For example,
**words**: `["This", "is", "an", "example", "of", "text", "justification."]`
**L**: `16`.

Return the formatted lines as:

```
[
   "This    is    an",
   "example  of text",
   "justification.  "
]

```

**Note:** Each word is guaranteed not to exceed *L* in length.

[click to show corner cases.](https://leetcode.com/problems/text-justification/description/#)

Corner Cases:

- A line other than the last line might contain only one word. What should you do in this case?
  In this case, that line should be left-justified.

## Solution

首先要确定每行能放下的单词数，下面的解法默认了不管每行限制的字符数是多少，每行都至少填一个单词。（leetcode 上没有做这个的 corner case，所以可以通过。若限制字符数小于单词长度，理应返回空）

长度为 maxWordNum 个单词的长度 + maxWordNum - 1 个空格长度 <= maxWidth

该行还需要填充的空格数就是 maxWidth - 刚刚算得的长度。之后通过除法和取余均匀分配空格。

几个需要注意的 corner case：

1. 在算每行最大单词数的时候一定要加判断，防止越界！！！
2. 最后一行规则不同，要单独处理输出
3. 若该行只有 1 个单词的话，空格是要填充在这个单词之后的，也是要单独处理的
4. 几个单独处理的分支 ans 和 i 都要更新

```cpp
class Solution {
public:
    vector<string> fullJustify(vector<string>& words, int maxWidth) {
        vector<string> ans;
        
        int i = 0;
        while (i < words.size()) {
            int maxWordNum = 1;
            string lineStr;
            
            int lineWidth = words[i + maxWordNum - 1].size();
            // 这里访问数组不能越界！！
            while (i + maxWordNum < words.size() && lineWidth + 1 + words[i + maxWordNum].size() <= maxWidth) {
                lineWidth += words[i + maxWordNum].size() + 1;
                ++maxWordNum;
            }
            
            // 最后一行，规则不一样，要单独处理
            if (i + maxWordNum == words.size()) {
                while (i != words.size() - 1) {
                    lineStr.append(words[i++]);
                    lineStr.push_back(' ');
                }
                lineStr.append(words[i]);
                for (int j = lineStr.size(); j < maxWidth; ++j) lineStr.push_back(' ');
                ans.push_back(lineStr);
                return ans;
            }
            
            // 如果这一行只有一个单词，要把它放最左边，也要单独处理
            if (maxWordNum == 1) {
                lineStr.append(words[i]);
                for (int j = lineStr.size(); j < maxWidth; ++j) lineStr.push_back(' ');
                ans.push_back(lineStr);
                ++i;
                continue;
            }
            
            // 剩下要填充的空格，确保均匀填充
            int extraSpaceNum = maxWidth - lineWidth;
            int eachAddSpace = extraSpaceNum / (maxWordNum - 1);
            int leftSpaceNum = extraSpaceNum % (maxWordNum - 1);
            
                        
            for (int j = 0; j < maxWordNum - 1; ++j) {
                lineStr.append(words[i + j]);
                lineStr.push_back(' ');
                // 均匀填充多余的空格
                for (int k = 0; k < eachAddSpace + (j < leftSpaceNum ? 1 : 0); ++k) lineStr.push_back(' ');
            }
            lineStr.append(words[i + maxWordNum - 1]);
            
            ans.push_back(lineStr);
            i += maxWordNum;
        }
        return ans;
    }
};
```

### 改进

上面的解法实在是太繁琐了，处理起来很容易出错，这里贴出 leetcode 上的题解

这里没有把必须填的空格单独处理，而是统一一起算了。

`t += i + k >= n ? " " : string((maxWidth - l) / (k - 1) + (j <= (maxWidth - l) % (k - 1)), ' '); ` 这个语句的信息量巨大：

`i + k >= n` 代表是最后一行，所以只用填1个空格

`string((maxWidth - l) / (k - 1) + (j <= (maxWidth - l) % (k - 1)), ' ')` 则是在均匀分配空格数量，与上面解法思路相同

```cpp
class Solution {
public:
    vector<string> fullJustify(vector<string>& words, int maxWidth) {
        vector<string> ans;
        int n = words.size();
        for (int i = 0, k, l; i < n; i += k) { // k 为该行单词数目
            for (k = l = 0; i + k < n && l + words[i + k].size() <= maxWidth - k; k++) // 这里的 maxWidth - k 非常 niece
                l += words[i + k].size(); // l 为所有单词累加长度，不算空格
            string t = words[i]; // 先填入第一个单词
            for (int j = 1; j <= k - 1; j++) { // 填充空格 和 后续单词 注意是 k - 1次循环
                t += i + k >= n ? " " : string((maxWidth - l) / (k - 1) + (j <= (maxWidth - l) % (k - 1)), ' '); // 非常精炼的三元表达式
                t += words[i + j];
            }
            ans.push_back(t + string(maxWidth - t.size(), ' ')); //对最后一行来填充剩余空格
        }
        return ans;
    }
};
```

