# Group Anagrams

Tags: Medium, Easy

## Problem

Given an array of strings, group anagrams together.

For example, given: ["eat", "tea", "tan", "ate", "nat", "bat"], 
Return:

[
  ["ate", "eat","tea"],
  ["nat","tan"],
  ["bat"]
]

## Solution 1 - map - sort

本题为 Valid Anagram 的升级版，需要的是


```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> ans;
        
        unordered_map<string, vector<string>> map;
        
        for (string& str : strs) {
            string sortedStr = str;
            sort(sortedStr.begin(), sortedStr.end());
            
            if (map.count(sortedStr) > 0) map[sortedStr].push_back(str);
            else map[sortedStr] = vector<string> {str};
        }
        
        for (string& str : strs) {
            string sortedStr = str;
            sort(sortedStr.begin(), sortedStr.end());
            
            if (! map[sortedStr].empty()) {
                ans.push_back(map[sortedStr]);
                map[sortedStr].clear();
            }
        }
        
        return ans;
    }
};
```