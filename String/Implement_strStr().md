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

## 解法1 - 暴力匹配 - 双重 for 循环遍历

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

另一种写法

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.empty()) return 0;
        
        for (int i = 0; ; ++i) {
            for (int j = 0; ; ++j) {
                if(j == needle.size()) return i;
                if (i + j == haystack.size()) return -1;
                if (haystack[i + j] != needle[j]) break;
            }
        }
    }
};
```

### 复杂度

双重 for 循环，时间复杂度最坏情况下为 $$O((n-m)*m)$$. 即 $$O(n * m)$$

## 解法2 - KMP

参考了 《编程之法》一书的 4.4 节，详细的讲解了 KMP 算法。

KMP 在实现的时候有以下几点需要注意：

1. 实现 next 数组的时候，pattern[k]  代表前缀，pattern[j] 代表后缀，由于在循环体里是先++，再赋值，所以 k ，j 的初始值设为 `int k = -1, int j = 0;` 而且要提前把 next[0] = -1 设好。循环体的判断条件为 `j < n - 1` 
2. `k == -1 || pattern[j] == pattern[k]` 若前缀下标为 -1 也要囊括进去
3. 主函数 i, j 分别代表 haystack 和 needle 的下标，这里的判断条件 `j == -1 || haystack[i] == needle[j]` 是 j ，因为 i 永远之后递增，只有 j 才会回溯。虽然 j 的初始值为 0， 但是有可能会变为 -1 的。
4. 最后的判断是 `if (j == n) return i - j;` 因为 i 一定会加到 m，但 j 不匹配的话一定不会递增到末尾，所以 j 到末尾是可以判断的。

### C++

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.empty()) return 0;
        int m = haystack.size();
        int n = needle.size();
        vector<int> next(n);
        getNext(needle, next);
        
        int i = 0, j = 0;
        while (i < m && j < n) {
            if (j == -1 || haystack[i] == needle[j]) {
                ++i;
                ++j;
            } else {
                j = next[j];
            }
        }
        
        if (j == n) return i - j;
        else return -1;
    }
    
    void getNext(string& pattern, vector<int>& next) {
        int n = next.size();
        next[0] = -1;
        int k = -1, j = 0;
        
        while (j < n - 1) {
            if (k == -1 || pattern[j] == pattern[k]) {
                ++j, ++k;
                if (pattern[j] != pattern[k]) next[j] = k;
                else next[j] = next[k];
            } else {
                k = next[k];
            }
        }
        
    }
};
```

### C

C 的形参传递数组的话，应该写作 `int next[]` 而不能写成 `int[] next`

```cpp
class Solution {
public:
    char *strStr(char *haystack, char *needle) {
        int m = strlen(haystack);
        int n = strlen(needle);
        int next[n];
        getNext(needle, next);
        
        int i = 0, j = 0;
        while (i < m && j < n) {
            if (j == -1 || haystack[i] == needle[j]) ++i, ++j;
            else j = next[j];
        }
        
        if (j == n) return haystack + i - j;
        else return NULL;
    }
    
    void getNext(char* needle, int next[]) {
        int n = strlen(needle);
        next[0] = -1;
        int k = -1, j = 0;
        while (j < n - 1) {
            if (k == -1 || needle[k] == needle[j]) {
                ++k, ++j;
                if (needle[k] != needle[j]) next[j] = k;
                else next[j] = next[k];
            } else {
                k = next[k];
            }
        }
    }
};
```

### 复杂度

文本串长度为 m, 匹配字符串长度为 n，那么匹配过程的时间复杂度为 `O(m)` 计算 next 的时间为 `O(n)`，故 KMP 算法的整体时间复杂度为 $$O(m + n)$$ 