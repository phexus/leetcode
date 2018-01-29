# Intersection of Two Linked Lists

Tags: Linked_List, Easy

## Problem

Write a program to find the node at which the intersection of two singly linked lists begins.

For example, the following two linked lists:

A:          a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3
begin to intersect at node c1.

Notes:

If the two linked lists have no intersection at all, return null.
The linked lists must retain their original structure after the function returns.
You may assume there are no cycles anywhere in the entire linked structure.
Your code should preferably run in O(n) time and use only O(1) memory.

## 解法1 - hashset - 空间复杂度不符合要求

遍历 list A，将 A 的 node 存储在 unordered_set 中，然后遍历 B，查 B 的元素是否出现在 unordered_set 中。

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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode*> st;
        ListNode* curr = headA;
        
        while (curr) {
            st.insert(curr);
            curr = curr -> next;
        }
        
        curr = headB;
        while(curr) {
            if (st.count(curr) > 0) return curr;
            curr = curr -> next;
        }
        
        return NULL;
    }
};
```

### 复杂度分析

时间复杂度 $$O(m + n)$$
空间复杂度$$O(m)$$

## 解法2 - 双指针遍历

[参考题解](https://leetcode.com/articles/intersection-two-linked-lists/) 有详细的解释。
本题利用双指针的遍历、重新指向的方法很巧妙。下面给出了两种写法，其中第一种写法分开写，一定要写跳出条件，防止遇到两个链表完全不相交导致的死循环；而第二种写法用三元表达式就很机智，当两个指针都指向了NULL之后，就进不了循环了

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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (! headA || ! headB) return NULL;
        
        ListNode* p1 = headA;
        ListNode* p2 = headB;
        
        while (p1 != p2) {
            p1 = p1 -> next;
            p2 = p2 -> next;
            // 下面这一行很关键，如果两个链表都指向了末尾NULL，也会跳出循环返回NULL
            if (p1 == p2) return p1;
            
            if (! p1) p1 = headB;
            if (! p2) p2 = headA;
        }
        
        return p1;
    }
};
```

更优雅的解法

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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (! headA || ! headB) return NULL;
        
        ListNode* p1 = headA;
        ListNode* p2 = headB;
        
        while (p1 != p2) {
            p1 = p1 ? p1 -> next : headB;
            p2 = p2 ? p2 -> next : headA;
        }
        
        return p1;
    }
};
```

### 复杂度分析

时间复杂度 $$O(m + n)$$
空间复杂度 $$O(1)$$