# Substring with Concatenation of All Words

Tags: String, Hard

## Problem

You are given a string, **s**, and a list of words, **words**, that are all of the same length. Find all starting indices of substring(s) in **s** that is a concatenation of each word in **words** exactly once and without any intervening characters.

For example, given:
**s**: `"barfoothefoobarman"`
**words**: `["foo", "bar"]`

You should return the indices: `[0,9]`.
(order does not matter).

## Solution 1

该题涉及到字符串的模式匹配，感觉还是挺难的，参考了 leetcode 上的相关[解答](https://leetcode.com/problems/substring-with-concatenation-of-all-words/discuss/13658/Easy-Two-Map-Solution-(C++Java))  以及 nowcoder 上的[解释](https://www.nowcoder.net/questionTerminal/69ad067708c14c248f9cb9b7b6553e8f)

需要考虑一下几点：

1. words 中可以存在重复的元素的
2. 子串不允许间断，即子串从开始到找全 words 中的所有元素之前，子串中不允许包含 words 以外的东西，而且，即使当前处理的子串是 words 中含有的，但是前面已经找够了，这个多余的也是不合法的，若此时还有L中的其他元素没找到，从这个起点开始也是不成功的。
3. words 在 S 中出现的顺序不同考虑，任意顺序，只要全部存在就可以

这里定义了两个 unordered_map, `wordsCount` 用来表示 words 中每个单词出现的次数（因为允许重复），`wordsSeen` 用来表示遍历当前子串时每个单词出现的次数。

因为子串不允许间断，所以 起点 i 的上界是 `sLen - wordNum * wordLen` 每个子串的长度固定是 `wordNum * wordLen` 

```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        if (s.empty(), words.empty()) return vector<int>();
        
        unordered_map<string, int> wordsCount;
        
        for (string& word : words) ++wordsCount[word]; // 当 word 不存在 map 中，默认是 0
        
        int sLen = s.size(), wordNum = words.size(), wordLen = words[0].size();
        vector<int> ans;
        
        for (int i = 0; i < sLen - wordNum * wordLen + 1; ++i) { // i 代表起点
            int j = 0; // 表示处理过的单词序号，只有当 j==wordNum 时，才找到了所有单词
            unordered_map<string, int> wordsSeen;
            
            for ( ; j < wordNum; ++j) {
                string sub = s.substr(i + j * wordLen, wordLen); //以i为起点，长度为len的第j个单词
                if (wordsCount.count(sub)) {
                    ++wordsSeen[sub];
                    if (wordsSeen[sub] > wordsCount[sub]) break; //单词出现次数超出，则i位置不合法
                } else {
                    break; // 此单词不存在于counts里，i位置不合法
                }
            }
            
            if (j == wordNum) ans.push_back(i); // 代表所有单词都处理完了，且没有异常，i是合法的
        }
        
        return ans;
    }
};
```

### 复杂度

For the time complexity, it depends on the two `for` loops. The outer loop will loop for $$O(n)$$ times and the inner loop will loop for $$O(wordNum)$$  times. So the time complexity is at most  $$O(n * wordNum)$$.

空间复杂度为 $$O(words.size())$$

## Solution 2 - O(n)

上面的解法在 leetcode 上需要 194 ms 才能跑完，[这个解法](https://leetcode.com/problems/substring-with-concatenation-of-all-words/discuss/13656/An-O(N)-solution-with-detailed-explanation) 设计的非常巧妙，可以做到 27 ms，[参考解释](https://www.cnblogs.com/grandyang/p/4521224.html) [参考](http://blog.csdn.net/u013291394/article/details/50559648)

不再是一个字符一个字符的遍历，而是一个词一个词的遍历。

最外层的 i 其实只需要遍历 wordLen 即可把所有的情况都遍历到了。

如”barfoothefoobarman”，因为目标单词的长度为3，所以只需遍历：

- ‘bar’ | ‘foo’ | ‘the’ | ‘foo’ | ‘bar’ | ‘man’
- ‘arf’ | ‘oot’ | ‘hef’ | ‘oob’ | ‘arm’
- ‘rfo’ | ‘oth’ | ‘efo’ | ‘oba’ | ‘rma’


还需要注意的是，`wordsSeen` `count` `left` 都要一起更新


```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> ans;
        if (s.empty(), words.empty()) return vector<int>();
        
        unordered_map<string, int> wordsCount;
        for (string& word : words) ++wordsCount[word]; // 当 word 不存在 map 中，默认是 0
        
        int sLen = s.size(), wordNum = words.size(), wordLen = words[0].size();
        vector<int> ans;
        
        // travel all sub string combinations
        for (int i = 0; i < wordLen; ++i) { // 这里的 i 上界是 wordLen ！！！
            int left = i, count = 0; // left 为子串起点，count 代表单词出现的次数
            unordered_map<string, int> wordsSeen;
            for (int j = i; j <= n - wordLen; j += wordLen) { // 这里的j表示子串的起始位置， j每轮加单词的长度
                string str = s.substr(j, wordLen); 
                // a valid word, accumulate results
                if (wordsCount.count(str)) {
                    wordsSeen[str]++;
                    if (wordsSeen[str] <= wordsCount[str]) 
                        count++;
                    else { // 多了一个单词的情况
                        // 多了一个单词，所以要不断调整左边界，直到吐出那个单词
                        while (wordsSeen[str] > wordsCount[str]) {
                            string str1 = s.substr(left, wordLen);
                            wordsSeen[str1]--;
                            if (wordsSeen[str1] < wordsCount[str1]) --count; //说明一个匹配没了，那么对应的count也要自减1，这个判断很关键，如果是多的那个单词，吐掉就不需要减 count
                            left += wordLen; // 左边界挪动一个单词的长度
                        }
                    }
                    // 每次比较完子串后，判断一下是否单词数目已经相等了
                    if (count == wordNum) {
                        ans.push_back(left);
                        // 成功匹配到了一个合法位置后，窗口左边界右移一个单词长度，作相应调整
                        --wordsSeen[s.substr(left, wordLen)];
                        --count;
                        left += wordLen;
                    }
                }
                // 没找到，把当前wordsSeen清空，count归零，left右移，重新开始
                else {
                    wordsSeen.clear();
                    count = 0;
                    left = j + wordLen;
                }
            }
        }
        
        return ans;
        
    }
};
```

 ### 复杂度

时间复杂度的解释：

```travel all the words combinations to maintain a window
there are wordLen(word len) times travel

each time, sLen/wordLen words, mostly 2 times travel for each word

one left side of the window, the other right side of the window

so, time complexity O(wl * 2 * N/wl) = O(2N)

```

