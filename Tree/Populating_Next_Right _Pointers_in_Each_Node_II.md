# Populating Next Right Pointers in Each Node II

Tags: Tags: Tree, Medium

## Problem

Follow up for problem "Populating Next Right Pointers in Each Node".

What if the given tree could be any binary tree? Would your previous solution still work?

Note:

You may only use constant extra space.
For example,
Given the following binary tree,
         1
       /  \
      2    3
     / \    \
    4   5    7
After calling your function, the tree should look like:
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \    \
    4-> 5 -> 7 -> NULL

##　解法1 - 三指针 - 遍历

需要三个指针，current 代表当前层遍历的元素，head 代表下一层（next 链表）的头元素，tail 代表下一层已经设置好 next 指针的最右边元素（next 链表的末尾）所以设置 next 指针主要遍历 tail，head 只是为了当本层 next 指针已设置完毕后方便 current 移到下一层设置的。

```cpp
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (! root) return;
        
        TreeLinkNode* curr = root;
        TreeLinkNode *head = NULL, *tail = NULL; 
        
        while (curr) {
            if (curr -> left) {
                if (tail) {
                    tail -> next = curr -> left;
                    tail = tail -> next;
                } else {
                    head = tail = curr -> left;
                }
            }
            
            if (curr -> right) {
                if (tail) {
                    tail -> next = curr -> right;
                    tail = tail -> next;
                } else {
                    head = tail = curr -> right;
                }
            }
            
            if (curr -> next) {
                curr = curr -> next;
            } else {
                curr = head;
                head = tail = NULL;
            }
        }
    }
};
```

## 解法2 - dummy node

[参考解法](https://www.nowcoder.net/questionTerminal/f18bc13a954f4389900b56e545feca6e)非常机智地引入了 dummy node。
原因是在设置 next 指针时，完全可以把该层看成是一个链表，链表头结点无法确定，所以就引入了 dummy node，这和之前的链表相关题目有异曲同工之妙。引入 dummy node 后，好处多多，省了复杂的逻辑判断，不用去判断 tail 是否存在了，也减少了指针。另外，在做 tree 、linkedlist 相关题目的时候，对于循环大多用的是 while，这里用了 for，与上面的解法相比更加简洁，令人耳目一新～

```cpp
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        while (root) {
            TreeLinkNode dummy(0), *pre;
            pre = & dummy;
            
            for (TreeLinkNode* p = root; p; p = p -> next) {
                if (p -> left) {
                    pre -> next = p -> left;
                    pre = pre -> next;
                }
                
                if (p -> right) {
                    pre -> next = p -> right;
                    pre = pre -> next;
                }
            }
            
            root = dummy.next;
        }
    }
};
```