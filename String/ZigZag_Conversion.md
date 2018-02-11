# ZigZag Conversion

Tags: String, Medium

## Problem

The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```
P   A   H   N
A P L S I I G
Y   I   R
```
And then read line by line: "PAHNAPLSIIGYIR"
Write the code that will take a string and make this conversion given a number of rows:
```
string convert(string text, int nRows);
```
convert("PAYPALISHIRING", 3) should return "PAHNAPLSIIGYIR".

## Solution

网上其他的做法有 找规律 `对于首行和末行之间的行，还会额外重复一次，重复的这一次距离本周期起始字符的距离是 2 * nRows - 2 - 2 * i` ，还有 用一个delta表示正向还是反向 等等，都不如 [参考解法](https://leetcode.com/problems/zigzag-conversion/discuss/3403/Easy-to-understand-Java-solution) 清晰易懂。感觉只要想好怎么存储数据就好。

下面的做法定义了一个 `vector<string> vec(numRows);` 每行存一个 string

`i`游标用来表示处理到第几个元素了

while 循环 包 两个 for 循环，for 循环分别处理从上往下添加的，和 从下往上添加的。第二个 for loop 注意好游标；另个 for loop 都得判断 `i< size` 的情况。

```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        if (s.empty()) return "";
        
        string ans;
        vector<string> vec(numRows);
        int size = s.size();
        
        int i = 0;
        while (i < size) {
            for (int idx = 0; idx < numRows && i < size; ++idx) vec[idx].push_back(s[i++]);
            
            for (int idx = numRows - 2; idx > 0 && i < size; --idx) vec[idx].push_back(s[i++]);
        }
        
        for (string& ss : vec) ans.append(ss);
        
        return ans;
    }
};

```

### 复杂度

时间复杂度 $$O(n)$$

空间复杂度 $$O(n)$$ 