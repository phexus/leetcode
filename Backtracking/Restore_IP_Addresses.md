# Restore IP Addresses

Tags: String, Backtracking, Medium

## Problem

Given a string containing only digits, restore it by returning all possible valid IP address combinations.

For example:
Given `"25525511135"`,

return `["255.255.11.135", "255.255.111.35"]`. (Order does not matter)

## Solution 1 - backtracking 

标准的回溯问题，不过本题难点在于剪枝。

总结一下回溯问题的模板，辅助函数里的 for 循环遍历的是每一层的元素个数，而递归退出条件控制着整体回溯树的层数，以本题为例，层数为 4，所以另加了 count 参数做判断，而每层的元素个数可以是 1,2,3 个字符组成数字，所以 for 循环的次数就是 1 - 3 依次推进截取字符串。这里的 i 代表截取的字符串长度。

关于剪枝问题：通常有两个地方进行剪枝处理，一个是递归退出时，判断生成的要往 ans 中添加的元素是否符合条件，若不符合就直接 return。这里除了要把段数 > 4 的丢弃外，还需要对 段数 = 4 的做处理，`count == 4 && begin == s.size()` 因为要把没有用完数字的分组也丢弃掉。

另一个地方就是 for loop 里试探生成元素的时候，可以对每个元素做一些处理。这里比较繁琐，先要把 `begin + i > s.size()` 直接 **break** 因为已经超过最大数字了，没必要再循环了；截取的字符串除了 0 以外不能由 0 开头，最大不能超过 255，都需要配合 字符串的长度做一些限制。

注意递归调用时，begin + i，atoi 用于 char*, stoi 可以用于 string

```cpp
class Solution {
public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> ans;
        
        string ip;
        backtracking(s, 0, ip, ans, 0);
        return ans;
    }
    
    void backtracking(string& s, int begin, string ip, vector<string>& ans, int count) {
        if (count > 4) return;
        if (count == 4 && begin == s.size()) {
            ans.push_back(ip);
            return;
            
        } else if (begin < s.size()) {
            for (int i = 1; i <= 3; ++i) {
                if (begin + i > s.size()) break;
                
                string sub = s.substr(begin, i);
                if ((i > 1 && sub[0] == '0') || (i == 3 && stoi(sub) > 255)) continue;
                
                backtracking(s, begin + i, ip + sub + (count == 3 ? "" : "."), ans, count + 1);
            }
        }
    }
};
```

## Solution 2 - iterative

[参考](https://leetcode.com/problems/restore-ip-addresses/discuss/30972/WHO-CAN-BEAT-THIS-CODE) 

非常机智的做法，通过 4 个 for loop，就成功的把字符串分割成了四个部分。最精彩的是 `if ( (ans=to_string(A)+"."+to_string(B)+"."+to_string(C)+"."+to_string(D)).length() == s.length()+3)` 这个判断，绕过了复杂的判断，直接把 0 开头的 corner case 给排除了，因为 to string 后这种情况下长度肯定会缩短。

```cpp
class Solution {
public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> ret;
        string ans;
        
        for (int a=1; a<=3; a++)
        for (int b=1; b<=3; b++)
        for (int c=1; c<=3; c++)
        for (int d=1; d<=3; d++)
            if (a+b+c+d == s.length()) {
                int A = stoi(s.substr(0, a));
                int B = stoi(s.substr(a, b));
                int C = stoi(s.substr(a+b, c));
                int D = stoi(s.substr(a+b+c, d));
                if (A<=255 && B<=255 && C<=255 && D<=255)
                    if ( (ans=to_string(A)+"."+to_string(B)+"."+to_string(C)+"."+to_string(D)).length() == s.length()+3)
                        ret.push_back(ans);
            }    
        
        return ret;
    }
};
```

另一种写法

```cpp
class Solution {
public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> ans;
        string ip;
        
        for (int a=1; a<=3; a++)
        for (int b=1; b<=3; b++)
        for (int c=1; c<=3; c++) {
            int d = s.size() - a -b -c;
            if (d >= 1 && d <= 3) {
                string A = s.substr(0, a);
                string B = s.substr(a, b);
                string C = s.substr(a+b, c);
                string D = s.substr(a+b+c, d);
                if (isValid(A) && isValid(B) && isValid(C) && isValid(D)) {
                    ip = A + "." + B + "." + C + "." + D;
                    ans.push_back(ip);
                }
            }
        }      
        return ans;
    }
    
    bool isValid(string& str) {
        if ((str.size() > 1 && str[0] == '0') || (str.size() == 3 && stoi(str) > 255)) return false;
        return true;
    }
};
```

