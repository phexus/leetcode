# Merge Two Sorted Lists

Tags: Linked_List, Easy

## Problem

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Example:

Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4

## 题解1 - dummy + 遍历

一样的套路，两个链表的合并，合并后的表头节点不一定，故应联想到使用dummy节点。创建一个 node 指针指向遍历完成的最后一个结点。每次指针移动后，都要考虑到到 l1 与 l2 是否为空的情况，所以先统一处理两者都不为空的情况。之后势必只会剩下一个链表，只要把该链表接到之前遍历完成的结点后面即可。

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
    ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
        ListNode dummy(0);
        ListNode* node = &dummy;
        
        while (l1 && l2) {
            if (l1 -> val < l2 -> val) {
                node -> next = l1;
                l1 = l1 -> next;
            } else {
                node -> next = l2;
                l2 = l2 -> next;
            }
            node = node -> next;
        }
        
        if (l1) node -> next = l1;
        else node -> next = l2;
        
        return dummy.next;
    }
};
```

### 源码分析

一旦发现头结点无法确定时，就要联想到使用 dummy node。
最后的拼接可以用一个三元表达式完成 `node -> next = (l1) ? l1 : l2;`

### 复杂度分析

最好情况下，一个链表为空，时间复杂度为 $$O(1)$$. 最坏情况下，遍历两个链表中的每一个节点，时间复杂度为 $$O(l1+l2)$$. 
空间复杂度近似为 $$O(1)$$.

## 题解2 - 递归

递归的写法一般可以先写出递推公式，这道题的解法思路参考 [leetcode solution](https://leetcode.com/problems/merge-two-sorted-lists/solution/)

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
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (! l1) return l2;
        else if (! l2) return l1;
        
        else if (l1 -> val < l2 -> val) {
            l1 -> next = mergeTwoLists(l1 -> next, l2);
            return l1;
        } else {
            l2 -> next = mergeTwoLists(l1, l2 -> next);
            return l2;
        }
        
    }
};
```

### 复杂度分析

时间复杂度为 $$O(m+n)$$，每个 list 的每个元素只会被调用一次，因此时间复杂度为线性的。

`The first call to mergeTwoLists does not return until the ends of both l1 and l2 have been reached, so n + m stack frames consume O(n+m)O(n+m) space.`
递归调用的栈占用了 m + n，所以空间复杂度为 $$O(m + n)$$;
