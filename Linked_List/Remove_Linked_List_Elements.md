# Remove Linked List Elements

Tags: Linked_List, Easy

## Problem

Remove all elements from a linked list of integers that have value val.

Example
Given: 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6
Return: 1 --> 2 --> 3 --> 4 --> 5

## 解法1 - 遍历

由于头结点不确定，创建 dummy node

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
    ListNode* removeElements(ListNode* head, int val) {
        if (! head) return NULL;
        
        ListNode dummy(0);
        dummy.next = head;
        ListNode* curr = & dummy;
        
        while (curr -> next) {
            if (curr -> next -> val == val) {
                ListNode* tmp = curr -> next;
                curr -> next = tmp -> next;
                delete tmp;
                tmp = NULL;
            } else {
                curr = curr -> next;
            }
        }
        
        return dummy.next;
    }
};
```

## 解法2 - 递归

链表的题很多都可以用递归的思路来做，因为链表本身就可以看成是递归的

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
    ListNode* removeElements(ListNode* head, int val) {
        if (! head) return NULL;
        head -> next = removeElements(head -> next, val);
        
        return head -> val == val ? head -> next : head;
    }
};
```