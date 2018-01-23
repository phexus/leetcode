# Swap Nodes in Pairs

Tags: Linked_List, Medium

## Problem

Given a linked list, swap every two adjacent nodes and return its head.

For example,
Given 1->2->3->4, you should return the list as 2->1->4->3.

Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

## 题解1 - 迭代

这道题简直就是 Reverse Nodes in k-Group 的简化版，只是 k = 2。一样的做法，只不过由于 k = 2，所以不需要内层循环，只要注意把循环的条件设置好就行。
注意 while 的判断条件为 `node -> next && node -> next -> next`，第一次提交时只想到了要关注 node -> next -> next 的状态，结果报了空指针的错，这是因为 node -> next 为 NULL 时，node -> next -> next 就没有意义了，会报错。操作指针时一定要小心再小心！！

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (! head) return NULL;
        
        ListNode dummy(0);
        dummy.next = head;
        
        ListNode* prev = &dummy;
        ListNode* node = &dummy;
        
        while (node -> next && node -> next -> next) {
            prev = node;
            node = node -> next;
            
            ListNode* tmp = node -> next;
            node -> next = tmp -> next;
            tmp -> next = prev -> next;
            prev -> next = tmp;
        }
        return dummy.next;
    }
};
```

###　复杂度分析

遍历链表一遍，时间复杂度 $$$O(n)$
空间复杂度　$$$O(1)$

## 题解2 - 递归

[参考解法](https://algorithm.yuanbin.me/zh-hans/linked_list/swap_nodes_in_pairs.html)
用递归的思路来解的话，可以将本题理解成奇偶结点互换，函数返回的是偶结点，奇结点链接的应该是下一个偶结点，这样一来明显看出是一个递归问题，要注意先链接奇结点，再把奇结点连接在偶结点后面，最后返回偶结点，非常优雅～

注意递归的退出条件　`if (! head || ! head -> next) return head;`

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (! head || ! head -> next) return head;
        
        ListNode* after = head -> next;
        head -> next = swapPairs(after -> next);
        after -> next = head;
        
        return after;
    }
};
```

###　复杂度分析

每个结点最多被遍历若干次，时间复杂度 $$$O(n)$
空间复杂度　$$$O(1)$