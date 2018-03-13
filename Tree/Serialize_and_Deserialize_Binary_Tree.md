# Serialize and Deserialize Binary Tree

Tags: Tree, Design, Hard

## Problem

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

For example, you may serialize the following tree

```
    1
   / \
  2   3
     / \
    4   5

```

as `"[1,2,3,null,null,4,5]"`, just the same as [how LeetCode OJ serializes a binary tree](https://leetcode.com/faq/#binary-tree). You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

**Note:** Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.

## Solution

本题与 `剑指 offer` 面试题 62 相同

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if (!root) return "";
        string ans;
        serialHelp(root, ans);
        return ans;
    }
    
    void serialHelp(TreeNode* root, string& str) {
        if (!root) {
            str.push_back('$');
            str.push_back(',');
            return;
        }
        
        str.append(to_string(root -> val));
        str.push_back(',');
        serialHelp(root -> left, str);
        serialHelp(root -> right, str);
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        if (data.empty()) return NULL;
        
        TreeNode* root;
        int idx = 0;
        deserialHelp(data, idx, root);
        return root;
    }

    void deserialHelp(string& data, int& idx, TreeNode*& root) {
        if (idx >= data.size()) return;

        if (data[idx] == '$') {
            ++idx;
            ++idx;
            return;
        }

        int startIdx = idx;
        while (idx < data.size() && data[idx] != ',') ++idx;

        string val = data.substr(startIdx, idx - startIdx);
        root = new TreeNode(stoi(val));
        ++idx;

        deserialHelp(data, idx, root -> left);
        deserialHelp(data, idx, root -> right);

    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```

C 语言版本

最终还是用到了 string，因为不知道树的规模，用 string 更方便点。

```C
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};
*/
class Solution {
public:
    char* Serialize(TreeNode *root) {    
        if (!root) return NULL;
        string str;
        serial(root, str);
        char* ch = new char[str.size() + 1];
        for (int i = 0; i < str.size(); ++i) ch[i] = str[i];
        ch[str.size()] = '\0';
        return ch;
    }
    
    void serial(TreeNode* root, string& str) {
        if (!root) {
            str.append("$,");
            return;
        }
        str.append(to_string(root -> val));
        str.push_back(',');
        
        serial(root -> left, str);
        serial(root -> right, str);
    }
    
    TreeNode* Deserialize(char *str) {
        if (!str) return NULL;
        return deserial(str);
    }
    
    TreeNode* deserial(char*& str) {
        if (*str == '\0') return NULL;
        if (*str == '$') {
            str += 2;
            return NULL;
        }
        
        int num = 0;
        while (*str != '\0' && *str != ',') {
            num = num * 10 + (*str - '0');
            ++str;
        }
        TreeNode* root = new TreeNode(num);
        if (*str != '\0') ++str;
        root -> left = deserial(str);
        root -> right = deserial(str);
        return root;
    }
};
```

