# Unique Binary Search Trees II

Tags: Tree, Medium, Exhaustive Search

## Problem

Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1...n.

For example,
Given n = 3, your program should return all 5 unique BST's shown below.

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

## 解法 - divide and conquer - DFS

[参考解法](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/unique_binary_search_trees_ii.html) 有详细的思路，这里摘抄部分关键内容如下：
与前一题类似，围绕 二叉搜索树 的定义，如果以 i 为根节点，那么其左子树由[1, i - 1]构成，右子树由[i + 1, n] 构成。要构建包含1到n的二叉搜索树，只需遍历1到n中的数作为根节点，以i为界将数列分为左右两部分，小于i的数作为左子树，大于i的数作为右子树，使用两重循环将左右子树所有可能的组合链接到以i为根节点的节点上。
> 特别注意的是，这里提到左子树由[1, i - 1]构成，而前一题说的是[0, i - 1]，并不是 bug，而是因为本题需要递归的构造左右子树，当不满足条件时自然而然就返回了空子树的情况，所以就没必要在定义里体现了。

设计递归的终止步、输入参数和返回结果。由于根据i拆分左右子树的过程中，递归调用的方法中入口参数会缩小，且存在start <= i <= end, 故终止步为start > end. 那要至于start == end，只要简单的思考或画出递归调用退出的过程就会发现：
因为start == end时其左右子树均返回空，故在for循环中返回根节点。当然单独考虑可减少递归栈的层数，但实际测下来后发现运行时间反而变长了不少 （个人觉得之所以变成，可能是因为每次都要加一次判断，而相等的情况必定是少数 case，所以下面的解法并没有单独处理相等的情况）

另外需要注意的是，leetcode 的测试用例比较严格，当 n = 0时，应该返回 [], 但按照上面的思路，回去调用 generateTrees(1, 0),返回的是 [[]],是因为后续的遍历需要把这种情况考虑成空子树，所以 辅助函数 和 主函数 在这一异常输入不能用同一部分代码处理，需要在主函数中单独处理。
```cpp
    vector<TreeNode*> generateTrees(int n) {
        if (n) return generateTrees(1, n);
        else return vector<TreeNode*>();
        
    }
```

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        if (n) return generateTrees(1, n);
        else return vector<TreeNode*>();
        
    }
    
    vector<TreeNode*> generateTrees(int start, int end) {
        vector<TreeNode*> result;
        
        if (start > end) {
            result.push_back(NULL);
            return result;
        }
        
        for (int i = start; i <= end; ++ i) {
            vector<TreeNode*> leftTrees = generateTrees(start, i - 1);
            vector<TreeNode*> rightTrees = generateTrees(i + 1, end);
            
            for (int j = 0; j < leftTrees.size(); ++ j) {
                for (int k = 0; k < rightTrees.size(); ++ k) {
                    TreeNode* root = new TreeNode(i);
                    root -> left = leftTrees[j];
                    root -> right = rightTrees[k];
                    result.push_back(root);
                }
            }
        }
        
        return result;
    }
};
```

###　复杂度

递归调用，一个合理的数组区间将生成新的左右子树，时间复杂度为指数级别，使用的临时空间最后都被加入到最终结果，空间复杂度(堆)近似为 $$O(1)$$, 而由于递归，栈上的空间较大


[另一种解法](https://leetcode.com/problems/unique-binary-search-trees-ii/discuss/31516/Share-a-C++-DP-solution-with-O(1)-space)　有空再看
