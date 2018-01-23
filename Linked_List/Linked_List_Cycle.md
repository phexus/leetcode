# Linked List Cycle

Tags: Linked_List, Easy

## Problem

Given a linked list, determine if it has a cycle in it.

Follow up:
Can you solve it without using extra space?

## 题解 -快慢指针

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
    bool hasCycle(ListNode *head) {
        if (head == NULL || head -> next == NULL) return false;
        
        ListNode* slow = head;
        ListNode* fast = head -> next;
        
        while (slow && fast && fast -> next) {
            if (slow == fast) return true;
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        
        return false;
    }
};
```

### 源码分析

1. head -> next　== NULL 的情况也要考虑进去，因为后续 fast 指针指向的就是 head -> next
2. while (slow && fast && fast -> next) 可以简化为 while (fast && fast -> next)，因为 fast 比 slow 快，fast 肯定先比 slow 遇到链表尾部，所以只用判断 fast 即可。

### 复杂度分析

无环时，快指针每次走两步到尾结点，遍历的时间复杂度为$$O(n/2)$$
有环时，最坏情况链表头尾相接，需 n 次才能相遇，时间复杂度 $$o(n)$$，最好情况尾节点出现环，与无环相同。

## - 题解2 - hashset

用了额外的空间，虽然不符合题意，但也是一种思路， 特别注意 unordered_set 存的是指针， ListNode*

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
    bool hasCycle(ListNode *head) {
        unordered_set<ListNode*> st;
        
        ListNode* p = head;
        while(p != NULL) {
            if (st.find(p) != st.end()) {
                return true;
            } else {
                st.insert(p);
            }
            p = p -> next;
        }
        return false;
    }
};
```

### 复杂度分析

时间复杂度 $$O(n)$$
空间复杂度 $$o(n)$$
