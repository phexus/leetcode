# Roman to Integer

Tags: String, Easy

## Problem

Given a roman numeral, convert it to an integer.

Input is guaranteed to be within the range from 1 to 3999.

## 解法

[参考解法](https://leetcode.com/problems/roman-to-integer/discuss/6547/Clean-O(n)-c++-solution)

[基础知识](https://www.nowcoder.net/questionTerminal/817fdd12bd3341349a0079f74e692ebf)

```
基本字符 对应的数字
I 1
V 5
X 10
L 50
C 100
D 500
M 1000
相同的数字连写、所表示的数等于这些数字相加得到的数、如：Ⅲ=3；
小的数字在大的数字的右边、所表示的数等于这些数字相加得到的数、 如：Ⅷ=8、Ⅻ=12；
小的数字、（限于 Ⅰ、X 和 C）在大的数字的左边、所表示的数等于大数减小数得到的数、如：Ⅳ=4、Ⅸ=9；
正常使用时、连写的数字重复不得超过三次；
在一个数的上面画一条横线、表示这个数扩大 1000 倍。
```

由上面的知识可知，从字符串倒数第二位开始往前迭代即可，当前符号代表的数比后面的大，就增加累积和，小就减少累积和

```cpp
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> T = { { 'I' , 1 },
                                       { 'V' , 5 },
                                       { 'X' , 10 },
                                       { 'L' , 50 },
                                       { 'C' , 100 },
                                       { 'D' , 500 },
                                       { 'M' , 1000 } };
        
        int sum = T[s.back()];
        
        for (int i = s.length() - 2; i >= 0; --i) {
            if (T[s[i]] < T[s[i + 1]]) sum -= T[s[i]];
            else sum += T[s[i]];
        }
        
    return sum;
    }
};
```

[假的优化解法](https://leetcode.com/problems/roman-to-integer/discuss/6811/My-easy-to-understand-C++-solutions)

但经过实际测试发现，过多的判断反而拖慢了速度，所以这是一个负优化。。

```cpp
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> T = { { 'I' , 1 },
                                       { 'V' , 5 },
                                       { 'X' , 10 },
                                       { 'L' , 50 },
                                       { 'C' , 100 },
                                       { 'D' , 500 },
                                       { 'M' , 1000 } };
        
        int sum = T[s.back()];
        
        for (int i = s.length() - 2; i >= 0; --i) {
            if ((s[i] == 'I' && (s[i + 1] == 'V' || s[i + 1] == 'X')) ||
                (s[i] == 'X' && (s[i + 1] == 'L' || s[i + 1] == 'C')) ||
                (s[i] == 'C' && (s[i + 1] == 'D' || s[i + 1] == 'M')))
                
            sum -= T[s[i]];
            else sum += T[s[i]];
        }
        
    return sum;
    }
};
```

其他的解法
```cpp
enum WORD {NONE, MARK_I, MARK_X, MARK_C};

class Solution {
public:
    int romanToInt(string s) {
        int i=0;
        int ans  = 0; 
        int mark = NONE;
        
        
        int len = s.length();
        
        for(i=0;i<len;i++) {
            switch(s[i]) {
                case 'M': {
                    ans +=1000;
                    if(mark==MARK_C) {ans -= 200; }
                    mark = NONE; 
                    
                    break;
                }
                case 'D': {
                    ans +=500;
                    if(mark==MARK_C) {ans -= 200;}
                    mark = NONE;                     
                    break;
                }
                case 'C': {                    
                    ans += 100;
                    if(mark==MARK_X) {mark = NONE  ; ans -= 20;}
                    else             {mark = MARK_C;}                   
                    
                    
                    break;
                }
                case 'L': {                    
                    ans +=50;
                    if(mark==MARK_X) { ans -= 20;}
                    mark = NONE;
                    
                    break;
                }
                case 'X': {                     
                    ans +=10;
                    if(mark==MARK_I) {mark = NONE ; ans -= 2;}
                    else             {mark = MARK_X;}                    
                    
                    break;
                }
                case 'V': {                    
                    ans +=5;
                    if(mark==MARK_I) { ans -= 2;}        
                    mark = NONE;

                    break;
                }
                case 'I': {                      
                    ans +=1;
                    mark = MARK_I;
                    
                    break;
                }                     
            }
              //cout << ans << endl;
        }     
        return ans;
    }
};
```

```cpp
static string s=[](){
    std::ios::sync_with_stdio(false);
    cin.tie(NULL);
    return "";
}();

class Solution {
public:
    int romanToInt(string s) {
        std::unordered_map<char, int> numerals;
        numerals['I'] = 1, numerals['V'] = 5,
        numerals['X'] = 10, numerals['L'] = 50,
        numerals['C'] = 100, numerals['D'] = 500,
        numerals['M'] = 1000;
        
        int sum = 0;
        int previousValue = 0;
        int currentValue = 0;
        
        for(int i = 0; i < s.size(); ++i)
        {
            currentValue = numerals[s[i]];           
            sum += (currentValue > previousValue) ? (currentValue - 2*previousValue) : currentValue;
            previousValue = currentValue;
        }
        
        return sum;
    }
};
```