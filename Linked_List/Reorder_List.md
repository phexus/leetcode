# Reorder List

Tags: Linked_List, Medium

## Problem

Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…

You must do this in-place without altering the nodes' values.

For example,
Given {1,2,3,4}, reorder it to {1,4,2,3}.

## 解法

[参考解法](https://algorithm.yuanbin.me/zh-hans/linked_list/reorder_list.html) 里详细阐述了两种解法，但是我觉得这道题如果按第一种更加 length 双重 for 遍历来做，由于要考虑各种复杂的边界条件，反而复杂了。我在做此题时，没有想到第一种解法依据的规律，而是认为本题是将链表劈成两半，右半部分逆序穿插到左半部分来。

本题可以拆成以下几个步骤：
1. 找到中间节点，利用经典的快慢指针法
2. 对右半部分翻转链表，注意 dummy，prev
3. 穿插，注意 while loop 的循环条件 `slow && fast`，slow长度一定 >= fast，最后多出来的那个元素就是原来的最中间元素，也应该是调整后的最后一个元素。

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
    void reorderList(ListNode* head) {
        if (! head || ! head -> next) return;
        
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast -> next && fast -> next -> next) {
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        fast = slow -> next;
        slow -> next = NULL;
        
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
        
        while (slow && fast) {
            ListNode* tmp1 = slow -> next;
            slow -> next = fast;
            ListNode* tmp2 = fast -> next;
            fast -> next = tmp1;
            
            slow = tmp1;
            fast = tmp2;
        }
    }
};
```

###　复杂度分析

找中点一次，时间复杂度近似为 $$O(n)$$. 反转链表一次，时间复杂度近似为 $$O(n/2)$$. 合并左右链表一次，时间复杂度近似为 $$O(n/2)$$. 故总的时间复杂度为 $$O(n)$$.