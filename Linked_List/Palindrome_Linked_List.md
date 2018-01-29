# Palindrome Linked List

Tags: Linked_List, Easy

## Problem

Given a singly linked list, determine if it is a palindrome.

Follow up:
Could you do it in O(n) time and O(1) space?

## 解法

这个题要求 $$O(1)$$ 的空间复杂度，链表的翻转可以 in-place，所以认为 $$O(1)$$，不过这一点也有[争议](https://leetcode.com/problems/palindrome-linked-list/discuss/64493)。不过我认为争论这一点没有意义，默认 $$O(1)$$ 就好。

几乎与 Reorder List 一样的套路，先找到链表中间节点，分开后，翻转右半部分，再对比
注意这样分开的链表，最中间结点在左半部分的最后，所以 最后对比时要以有半部分链表为准

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
    bool isPalindrome(ListNode* head) {
        // 很关键，既要考虑返回 true，又要考虑 一个元素 的corner case，否则 后面的 reverse会有问题，fast->next空指针
        if (! head || ! head -> next) return true;
        
        ListNode* slow = head;
        ListNode* fast = head;
        
        while (fast -> next && fast -> next -> next) {
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        
        fast = slow -> next;
        slow -> next = NULL;
        
        //reverse in-place
        ListNode dummy(0);
        dummy.next = fast;
        ListNode* prev = & dummy;
        
        while (fast -> next) {
            ListNode* tmp = fast -> next;
            fast -> next = tmp -> next;
            tmp -> next = prev -> next;
            prev -> next = tmp;
        }
        
        fast = dummy.next;
        slow = head;
        
        while (fast) {
            if (fast -> val != slow -> val) return false;
            fast = fast -> next;
            slow = slow -> next;
        }
        
        return true;
        
    }
};
```

###　复杂度分析

时间复杂度　$$O(n)$$
空间复杂度　$$O(1)$$