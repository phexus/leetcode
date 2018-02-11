# Integer to Roman

Tags: String, Medium

## Problem

Given an integer, convert it to a roman numeral.

Input is guaranteed to be within the range from 1 to 3999.

## 解法

感觉这个题最大的问题在于不熟悉罗马数字怎么表示。。。

```cpp
class Solution {
public:
    string intToRoman(int num) {
        string thous[] = {"","M","MM","MMM"};
        string hunds[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};
        string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};
        string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};
        
        return thous[num / 1000] + hunds[(num % 1000) / 100] + tens[(num % 100) / 10] + ones[(num % 10)];
    }
};
```

### 另一种解法

```cpp
class Solution {
public:
    string intToRoman(int num) {
        string res="";
        int val[] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        string str[] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        for (int i = 0;i < 13; ++i){
            while (num >= val[i]){
                res += str[i];
                num -= val[i];
            }
        }
        return res;
    }
};
```