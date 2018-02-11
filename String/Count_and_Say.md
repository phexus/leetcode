# Count and Say

Tags: String, Easy

## Problem

The count-and-say sequence is the sequence of integers with the first five terms as following:

1.     1
2.     11
3.     21
4.     1211
5.     111221
  1 is read off as "one 1" or 11.
  11 is read off as "two 1s" or 21.
  21 is read off as "one 2, then one 1" or 1211.
  Given an integer n, generate the nth term of the count-and-say sequence.

Note: Each term of the sequence of integers will be represented as a string.

Example 1:

Input: 1
Output: "1"
Example 2:

Input: 4
Output: "1211"

## Solution 1 - 迭代

弄懂题意就比较好写了，生成后一行的原则就是把前一行表示为 重复次数 + 数本身。

先将 ans 赋值为 1，从第二行开始遍历。

每次遍历从第二个字符开始，因为要累积前一个元素的个数；当遇到相同元素时 count 自增，知道遇到不同元素后，将前一个元素的重复次数 和 该元素本身追加到 ans 后。（因为有游标控制，所以可以 in-place，共用一个 ans，最后再 erase 前面的部分）

循环结束后，还需要单独处理最后一个元素。

> `ans.append(to_string(count));` int 转 字符串，初次做的时候我认为重复的次数有可能 > 9，所以就没有用 `ans.push_back('0' + count);` 但是仔细想想生成规则，就不难发现数字由 1 - 9 组成 ，那么重复次数肯定也就不会 > 9了，所以完全可以用后者的写法。
>
> 容易证明 `cnt` 不会超过3，因为若出现`1111`，则逆向可得两个连续的1，而根据规则应为`21`，其他如`2222`推理方法类似。

```cpp
class Solution {
public:
    string countAndSay(int n) {
        if (n < 1) return "";
        
        string ans = "1";
        
        for (int i = 1; i < n; ++i) {
            int length = ans.size();           
            int count = 1;
                      
            for (int j = 1; j < length; ++j) {
                if (ans[j] == ans[j - 1]) ++count;
                else {
                    ans.append(to_string(count));
                    ans.push_back(ans[j - 1]);
                    count = 1;
                }
            }
            ans.append(to_string(count)); //单独处理最后一个元素
            ans.push_back(ans[length - 1]);
            ans.erase(0, length);
        }
        
        return ans;
    }
};
```
## Solution 2 - 递归 

本行字符串是有上一行生成的，所以不难写出 `string ans = countAndSay(n - 1);` ，其余核心部分和迭代是一样的，另外注意递归的终止条件。

这里的核心部分，换了一种写法，就不用最后单独处理最后一个元素了，上面的迭代亦可如此。

> 判断相邻字符是否相同时需要判断索引是否越界!!!

```cpp
class Solution {
public:
    string countAndSay(int n) {
        if (n <= 0) return "";
        if (n == 1) return "1";
        
        string ans = countAndSay(n - 1);
        int count = 1;
        int size = ans.size();
        
        for (int i = 0; i < size; ++i) {
            if (i + 1 < size && ans[i] == ans[i + 1]) ++count;
            else {
                ans.push_back(count + '0');
                ans.push_back(ans[i]);
                count = 1;
            }
        }
        
        ans.erase(0, size);
        return ans;
    }
};
```

