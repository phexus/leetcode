# Implement strStr()

Tags: String, Medium

## Problem

Implement strStr().

Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

Example 1:

Input: haystack = "hello", needle = "ll"
Output: 2
Example 2:

Input: haystack = "aaaaa", needle = "bba"
Output: -1

## 解法1 - 双重 for 循环遍历

用双重 for 循环来做，效率不高，感觉比较简单，但实现起来坑还是比较多：

1. 边界问题：
题目也没说清楚，毕竟不熟悉 strStr()，下面是试出来的。。
当 haystack 为空， needle 也为空时 返回 0；
当 haystack 不为空， needle 为空时 返回 0；
当 haystack 为空， needle 不为空时 返回 -1；
当 haystack 长度 < needle 长度时 返回 -1；

前三条总结为，当 needle 为空时，直接返回 0，后面两条符合后续逻辑，输出 -1

2. haystack.size() 返回类型 为 `string::size_type`，是 `unsigned integral type`
在处理 haystack 长度 < needle 长度时，没有单独写逻辑来判断，而是通过设置外层 loop 的终止条件来实现，为仅遍历源字符串中有可能和目标字符串匹配的部分索引。
但是，初次写的代码是这样的：

```cpp
for (int i = 0; i < haystack.size() - needle.size() + 1; ++i) {
```

有符号型的 int 和 无符号型的结果 相比，结果可想而知，当相减为负时在无符号型里表示的是 `18446744073709551615` 这样大的一个数。。

后面经过查询[资料](http://www.cplusplus.com/reference/string/string/size/)， 调用 string::size(),返回类型是 string::size_type。
无符号数做减法时一定要小心！！！（vector 的 size 也返回的是无符号数）

所以可以用 int 强制类型转换，或者把迭代的游标就定义为 string::size_type 类型

```cpp
for (string::size_type i = 0; i < haystack.size() - needle.size() + 1; i++) {
```



```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.empty()) return 0;
        
        int haystackSize = haystack.size();
        int needleSize = needle.size();
        
        for (int i = 0; i < haystackSize - needleSize + 1; ++i) {
            for (int j = 0; j < needleSize; ++j) {
                if (haystack[i + j] != needle[j]) break;
                else if (j == needle.size() - 1) return i;
            }
        }
        
        return -1;
    }
};
```

可以把 j 定义在外层 loop，`return i` 的分支判断可以少做几次

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.empty()) return 0;
        
        int haystackSize = haystack.size();
        int needleSize = needle.size();
        
        for (int i = 0; i < haystackSize - needleSize + 1; ++i) {
            int j = 0;
            for ( ; j < needleSize; ++j) {
                if (haystack[i + j] != needle[j]) break;
            }
            if (j == needleSize) return i;
        }
        
        return -1;
    }
};
```

## 解法2 - KMP
