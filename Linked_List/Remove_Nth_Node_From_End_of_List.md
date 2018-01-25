# Remove Nth Node From End of List

Tags: Linked_List, Medium

## Problem

Given a linked list, remove the nth node from the end of list and return its head.

For example,

   Given linked list: 1->2->3->4->5, and n = 2.

   After removing the second node from the end, the linked list becomes 1->2->3->5.
Note:
Given n will always be valid.
Try to do this in one pass.

## 解法

本题较为简单，由于有可能需要移除第一个元素，所以通常的做法是添加 dummy node。
为了只遍历一遍，通常做法是采用两个指针，对前指针，使其先走出N步，随后两个指针同时前进，当前指针到达链表尾部时，后指针到达倒数第N个节点的位置。

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
    ListNode *removeNthFromEnd(ListNode *head, int n) {
        if (! head) return NULL;
        
        ListNode dummy(0);
        dummy.next = head;
        
        ListNode* fast = &dummy;
        ListNode* slow = &dummy;
        
        for (int i = 0; i < n; ++ i) fast = fast -> next;
        
        while (fast -> next) {
            fast = fast -> next;
            slow = slow -> next;
        }
        
        ListNode* tmp = slow -> next;
        slow -> next = tmp -> next;
        delete tmp;
        tmp = NULL;
        
        return dummy.next;
    }
};
```

### 复杂度分析

遍历一遍，时间复杂度 $$O(n)$$
空间复杂度　$$O(1)$$