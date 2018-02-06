# Evaluate Reverse Polish Notation

Tags: Stack, Medium

## Problem

Evaluate the value of an arithmetic expression in Reverse Polish Notation.

Valid operators are +, -, *, /. Each operand may be an integer or another expression.

Some examples:
  ["2", "1", "+", "3", "*"] -> ((2 + 1) * 3) -> 9
  ["4", "13", "5", "/", "+"] -> (4 + (13 / 5)) -> 6

## 我的解法 - 比较笨， 各种 int str 互转。。

本题比较简单，典型的栈相关的题目，不过，传入参数是 vector<string>& tokens， 因为有两位数的存在，所以都是用 string 来存的，然而四则运算又必须是 int 型，所以就涉及到了格式转换。（在初次实现的时候，竟然忘了 switch case 是不能用 String 型的。。）

注意 atoi 要先把 string 转成 char* 型的 C 风格字符串。

```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<string> st;
        
        for (string& s : tokens) {
            if (s == "+" || s == "-" || s == "*" || s == "/") {
                int b = atoi(st.top().c_str());
                st.pop();
                int a = atoi(st.top().c_str());
                st.pop();
                
                int c = 0;
                if (s == "+") c = a + b;
                else if (s == "-") c= a -b;
                else if (s == "*") c= a * b;
                else if (s == "/") c = a / b;
                
                st.push(to_string(c));
            } else {
                st.push(s);
            }            
        }
        
        return atoi(st.top().c_str());
    }
};

```

分析一下，其实可以把　stack 存放　int 型就好

```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> st;
        
        for (string& s : tokens) {
            if (s == "+" || s == "-" || s == "*" || s == "/") {
                int b = st.top();
                st.pop();
                int a = st.top();
                st.pop();
                
                if (s == "+") st.push(a + b);
                else if (s == "-") st.push(a -b);
                else if (s == "*") st.push(a * b);
                else st.push(a / b);
    
            } else {
                st.push(atoi(s.c_str()));
            }
        }
        
        return st.top();
    }
};
```