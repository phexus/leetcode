# Sort List

Tags: Linked_List, Medium

## Problem

Sort a linked list in $$O(nlog(n))$$ time using constant space complexity.

## 解法1 - 归并排序

注意， `while (fast -> next && fast -> next -> next)` 的循环条件，确保只有两个元素时的 corner case（否则会死循环）
通过这样两个快慢指针来找到中间元素很常见，Convert Sorted List to Binary Search Tree 里也用到了，注意 循环条件的不同。

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
    ListNode* sortList(ListNode* head) {
        if (! head || ! head -> next) return head;
        
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast -> next && fast -> next -> next) {
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        
        fast = slow -> next;
        slow -> next = NULL;
        
        ListNode* left = sortList(head);
        ListNode* right = sortList(fast);
        
        return merge2Lists(left, right);
    }
    
private:
    ListNode* merge2Lists(ListNode* left, ListNode* right) {
        ListNode dummy(0);
        ListNode* curr = & dummy;
        
        while (left && right) {
            if (left -> val < right -> val) {
                curr -> next = left;
                left = left -> next;
            } else {
                curr -> next = right;
                right = right -> next;
            }
            curr = curr -> next;
        }
        
        curr -> next = left ? left : right;
        return dummy.next;
    }
};
```