# Simplify Path

Tags: String, Medium

## Problem

Given an absolute path for a file (Unix-style), simplify it.

For example,
**path** = `"/home/"`, => `"/home"`
**path** = `"/a/./b/../../c/"`, => `"/c"`

[click to show corner cases.](https://leetcode.com/problems/simplify-path/description/#)

Corner Cases:

- Did you consider the case where **path** = `"/../"`?
  In this case, you should return `"/"`.
- Another corner case is the path might contain multiple slashes `'/'` together, such as `"/home//foo/"`.
  In this case, you should ignore redundant slashes and return `"/home/foo"`.

## Solution1 - getline STL

本来认为是一道挺简单的题，结果各种 corner case 让人防不胜防，结果代码被改得千疮百孔，不忍直视。。

... 这样的路径名是有意义的，如 [解释](https://leetcode.com/problems/simplify-path/discuss/25841/Don't-understand-the-testcase-%22abc...%22)

```
Input:
"/..."
Expected:
"/..."
```

下面的是隐藏文件，也是有意义的。

```
Input:
"/..hidden"
Expected:
"/..hidden"
```

之所以会出上面的问题，是因为最初我的思路是一个字符一个字符往里送，操作的应该是连续两个 / 分割的字符串！

参考 [leetcode discuss](https://leetcode.com/problems/simplify-path/discuss/25680/C++-10-lines-solution)

用到了STL 的 getline 和 stringstream 以流的方式来分割字符串

这里有个需要注意的地方就是 C++ 的字符串拼接。以这道题为例，拼接那步可以有一下 3 种方式来表达：

1. `ans = ans + "/" + str;` 14ms
2. `ans += "/" + str;` 8ms
3. `ans.append("/" + str);` 7ms

2 和 3 的时间接近，1 的时间就很夸张了。是因为 1 的 + 运算产生的是一个新的对象，再把结果返回，而 2 和 3 涉及到的应该是对象的引用，操作之后直接返回引用，避免了产生新的对象。因此，两者的性能有一定的差距。[字符串拼接的几种方式](http://blog.csdn.net/ljp1919/article/details/48134335)

另外，为了正序输出结果，没有用 stack，而是用的 vector 模拟的栈

```cpp
class Solution {
public:
    string simplifyPath(string path) {
        string ans;
        stringstream ss(path);
        string sub;
        vector<string> st;
        
        while (getline(ss, sub, '/')) {
            if (sub == "." || sub =="") continue;
            if (sub == ".." && !st.empty()) st.pop_back();
            else if (sub != "..") st.push_back(sub); // 判断不可少，排除 .. 的情况
        }
        
        for (string& str : st) ans.append("/" + str);
        return ans.empty() ? "/" : ans;
    }
};
```

## Solution 2 - 逐个单词处理

处理好 判断的条件，每次处理 / 之前的累积存储的字符子串，注意压栈时的多个条件 `else if (!elem.empty() && elem != "." && elem != "..") `

```cpp
class Solution {
public:
    string simplifyPath(string& path) {
        vector<string> stack; // use a vector as a stack
        string elem;

        if (path.back() != '/') path.push_back('/'); // 末尾多添一个分割符， 保证 /home 这样的输入正常处理
        for (int i = 0; i < path.size(); ++i) {
            char ch = path[i];
            if (ch == '/') { // 处理 / 之前的子串
                if (elem == ".." && !stack.empty()) {
                    stack.pop_back(); 
                } else if (!elem.empty() && elem != "." && elem != "..") {
                    stack.push_back(elem);                    
                }
                elem.clear();
                continue;
            }
            elem.push_back(ch);
        }

        string ans;
        for (string& str : stack) ans.append("/" + str);
        return ans.empty() ? "/" : ans;
    }
};
```

### 另一种更加简洁的写法，推荐

1. 遇到‘/’和'.'直接跳过
2. 遇到'..'弹出栈
3. 遇到一般的字符，压栈

另外优化了部分逻辑判断，更加清晰，要学会灵活使用 for while 搭配嵌套

```cpp
class Solution {
public:
    string simplifyPath(string path) {
        vector<string> st;
        int len = path.size();
        
        for (int i = 0; i < len; ++i) {
            while (i < len && path[i] == '/') ++i; //所有的 / 跳过
            
            if (i == len) break;
            
            string elem;
            while (i < len && path[i] != '/') elem.push_back(path[i++]);
            
            if (elem == ".") continue; // 上面的处理确保了 elem 不会是 ""
            if (elem == "..") { // 这样嵌套的写，逻辑更加清晰
                if (!st.empty()) st.pop_back();
            }
            else st.push_back(elem);
        }
        
        string ans;
        for (string& str : st) ans.append("/" + str);
        return ans.empty() ? "/" : ans;        
    }
};
```

