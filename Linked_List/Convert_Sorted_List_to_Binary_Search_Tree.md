# Convert Sorted List to Binary Search Tree

Tags: Linked_List, Medium

## Problem

Given a singly linked list where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

Example:

Given the sorted linked list: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5

## 解法

[参考思路](https://www.nowcoder.com/questionTerminal/86343165c18a4069ab0ab30c32b1afd0)
`这道题是要求把有序链表转为二叉搜索树，和之前那道Convert Sorted Array to Binary Search Tree思路完全一样，只不过是操作的数据类型有所差别，一个是数组，一个是链表。数组方便就方便在可以通过index直接访问任意一个元素，而链表不行。由于二分查找法每次需要找到中点，而链表的查找中间点可以通过快慢指针来操作。找到中点后，要以中点的值建立一个数的根节点，然后需要把原链表断开，分为前后两个链表，都不能包含原中节点，然后再分别对这两个链表递归调用原函数，分别连上左右子节点即可`

本题需要注意的是，tail 是不包含的那个元素，或者说是排除在外的那个元素，所以 corner case 只用处理`if (head == tail) return NULL;`即可。另外注意通过快满指针找中间结点的判断条件不能漏掉任何一个`while (fast != tail && fast -> next != tail)`

这种解法通过定义了一个新的函数，有了 tail 指针处理起来更加简洁方便，当然也可以直接用原函数递归 `https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/discuss/35555`

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
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
    TreeNode* sortedListToBST(ListNode* head) {
        return generateBST(head, NULL);
    }
    
    TreeNode* generateBST(ListNode* head, ListNode* tail) {
        if (head == tail) return NULL;
        
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast != tail && fast -> next != tail) {
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        
        TreeNode* root = new TreeNode(slow -> val);
        root -> left = generateBST(head, slow);
        root -> right = generateBST(slow -> next, tail);
        
        return root;
    }
};
```