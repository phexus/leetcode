# Minimum Window Substring

Tags: Hash Table, Two Pointers, String

## Problem

Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).

For example,
**S** = `"ADOBECODEBANC"`
**T** = `"ABC"`

Minimum window is `"BANC"`.

**Note:**
If there is no such window in S that covers all characters in T, return the empty string `""`.

If there are multiple such windows, you are guaranteed that there will always be only one unique minimum window in S.

## Solution

[参考题解](https://leetcode.com/problems/minimum-window-substring/discuss/26808/Here-is-a-10-line-template-that-can-solve-most-'substring'-problems) 有非常详细的解释，感觉这种求 subString 的都会用到 滑动窗口的思想，和之前的 longest substring without repeating characters 一样的思路。

1) begin开始指向0， end一直后移，直到begin - end区间包含 t 中所有字符。
记录窗口长度 minLen
2) 然后 begin 开始后移移除元素，直到移除的字符是T中的字符则停止，此时T中有一个字符没被
包含在窗口，
3) 继续后移 end，直到 T 中的所有字符被包含在窗口，重新记录最小的窗口 minLen。
4) 如此循环直到 end 到 S 中的最后一个字符。

这种解法的精髓之处在于，用一个 map 和 counter 来配合标识窗口中是否包含 t 中所有的字符，同时也可以记录移除的字符是 t 中的字符。

在 1）的过程中，若不是在 t 中所包含的元素，那么 map 记录的值就一定是负数，而在 t 中包含的元素，有可能为 0 ，也有可能为负，因为 aabc abc 的例子，a出现了两次，那么 a 对应的次数肯定为 负。但是不要紧，因为在 2）过程中，判断的条件是 `counter == 0` 刚好可以把这种重复的情况给排除掉，会一直排掉第二个 a 为止。

由于 2）步骤会改变 start 的值，所以原先满足的 start 值要用 minStart 记录下来。`end - start` 为窗口长度是因为之前 ++end，所以窗口本身就是左闭右开的。

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        if (s.empty() || t.empty()) return "";
        int map[128] = {0};
        for (auto c : t) ++map[c];
        
        int start = 0, end = 0, minLen = 0x7fffffff, minStart = 0, counter = t.size();
        
        while (end < s.size()) {
            if (map[s[end]] > 0) --counter;
            --map[s[end]];
            ++end;
            
            while (counter == 0) {
                if (end - start < minLen) {
                    minLen = end - start;
                    minStart = start;
                }
                
                if (map[s[start]] == 0) ++counter;
                ++map[s[start]];
                ++start;
            }
        }
        
        return minLen == 0x7fffffff ? "" : s.substr(minStart, minLen);
    }
};
```

### 复杂度

每个元素最多遍历 2 次，所以时间复杂度 $$O(n)$$

### 模板

下面的 counter 可能随着问题的不同而又特殊的含义。

```cpp
int findSubstring(string s){
        vector<int> map(128,0);
        int counter; // check whether the substring is valid
        int begin=0, end=0; //two pointers, one point to tail and one  head
        int d; //the length of substring

        for() { /* initialize the hash map here */ }

        while(end<s.size()){

            if(map[s[end++]]-- ?){  /* modify counter here */ }

            while(/* counter condition */){ 
                 
                 /* update d here if finding minimum*/

                //increase begin to make it invalid/valid again
                
                if(map[s[begin++]]++ ?){ /*modify counter here*/ }
            }  

            /* update d here if finding maximum*/
        }
        return d;
  }
```

 **Longest Substring Without Repeating Characters** 的套用上面模板的解法，这里的 counter 就指的是重复的元素 **多出来的个数**

```cpp
int lengthOfLongestSubstring(string s) {
        vector<int> map(128,0);
        int counter=0, begin=0, end=0, d=0; 
        while(end<s.size()){
            if(map[s[end++]]++>0) counter++; 
            while(counter>0) if(map[s[begin++]]-->1) counter--;
            d=max(d, end-begin); //while valid, update d
        }
        return d;
    }
```

