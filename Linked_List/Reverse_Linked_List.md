# Reverse Linked List

Tags: Linked_List, Easy

## Problem

Reverse a singly linked list.

click to show more hints.

Hint:
A linked list can be reversed either iteratively or recursively. Could you implement both?

## 题解1 - 迭代

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
    ListNode* reverseList(ListNode* head) {
        if (! head) return NULL;
        ListNode* prev = NULL;
        
        while (head) {
            ListNode* tmp = head -> next;
            head -> next = prev;
            prev = head;
            head = tmp;
        }
        
        return prev;
    }
};
```

### 复杂度分析

时间复杂度 $$O(n)$$
空间复杂度 $$O(1)$$

## 题解2 - 递归

递归的解法很巧妙，层层递归到最后一个结点，再利用 `head -> next -> next = head;` 巧妙地转换了指针的指向。
这里递归返回的 p 不参与运算，只是递归到了最后，再层层传递出来，每次传递过程中的会伴随着 head 指针的指向调整。当递归站最终返回时，也就代表所有的链表结点已经调整完毕。

特别要注意的一点是， `head -> next = NULL;` 最后一个节点作为尾节点要把 next 指向 NULL。

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
    ListNode* reverseList(ListNode* head) {
        if (! head || ! head -> next) return head;
        
        ListNode* p = reverseList(head -> next);
        head -> next -> next = head;
        // 很关键，在只有连个元素的情况下，最后一个节点作为尾节点要把 next 指向NULL
        head -> next = NULL;
        return p;
    }
};
```

### 复杂度分析

时间复杂度 $$O(n)$$
要递归 n 次，考虑到栈的空间消耗，空间复杂度 $O(n)$