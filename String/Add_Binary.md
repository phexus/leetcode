# Add Binary

Tags: String, Easy

## Problem

Given two binary strings, return their sum (also a binary string).

For example,
a = "11"
b = "1"
Return "100".

## Solution

本题与 linked_list 的 Add_Two_Numbers 一题类似，只是换成了二进制加法。充分利用 carry 的存储，有效的简化了两个数长短不一带来的边界处理。
另外需要注意的是， `carry = carry >> 1;` 我经常写成 `carry >> 1;` ，不赋值的话，carry 是不会变的！！！

```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        if (a.empty() || b.empty()) return "";
        
        int lenA = a.size();
        int lenB = b.size();
        
        int i = lenA -1, j = lenB - 1;
        int carry = 0;
        string ans;
        
        while (i >= 0 || j >= 0 || carry > 0) {
            if (i >= 0) carry += a[i--] - '0';
            if (j >= 0) carry += b[j--] - '0';

            ans.push_back(carry % 2 + '0');
            carry = carry >> 1;
        }
        
        reverse(ans.begin(), ans.end());
        
        return ans;
    }
};
```

### 复杂度

时间和空间复杂度均为 $$O(max(La, Lb))$$.