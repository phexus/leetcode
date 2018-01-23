# Remove Duplicates from Sorted List

Tags: Linked_List, Easy

## Problem

Given a sorted linked list, delete all duplicates such that each element appear only once.

For example,
Given 1->1->2, return 1->2.
Given 1->1->2->3->3, return 1->2->3.

## 解法

遍历，当前结点元素与下一个结点元素值相同时，删除下一节点，当前结点不变，结点的 next 指向下一个结点，直到下一个结点的值不同时，移动指针。

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
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* p = head;
        
        while (p) {
            while (p -> next && p -> next -> val == p -> val) {
                ListNode* tmp = p -> next;
                p -> next = tmp -> next;
                delete(tmp);
                tmp = NULL;
            }
            p = p -> next;
        }
        
        return head; 
    }
};
```

### 源码分析

由于最后返回的就是 head，所以可以不对 head 为空的情况进行单独处理。
释放内存，需要先用临时指针保存。

### 复杂度分析

时间复杂度 $$O(n)$$
空间复杂度 $$o(1)$$
