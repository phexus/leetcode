# Linked List Cycle II

Tags: Linked_List, Medium

## Problem

Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

Note: Do not modify the linked list.

Follow up:
Can you solve it without using extra space?

## 解法 - 快慢指针相遇

具体分析可参考
[分析1](https://siddontang.gitbooks.io/leetcode-solution/content/linked_list/linked_list_cycle.html)
[分析2](https://algorithm.yuanbin.me/zh-hans/linked_list/linked_list_cycle_ii.html)

综上，即使用头节点对快慢指针进行初始化，第一次相遇后，慢指针指向头结点，快慢指针每次走一步，再一次相遇的结点就是环开始的地方。

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
    ListNode *detectCycle(ListNode *head) {
        if (head == NULL || head -> next == NULL) return NULL;
        
        ListNode* slow = head;
        ListNode* fast = head;
        
        while (fast && fast -> next) {
            slow = slow -> next;
            fast = fast -> next -> next;
            if (slow == fast) {
                slow = head;
                while (slow != fast) {
                    slow = slow -> next;
                    fast = fast -> next;
                }
                return slow;
            }
        }
        return NULL;
    }
};
```

### 源码分析

这里快慢指针都初始化为头节点会方便很多，而前一题快结点是慢结点的下一个。需要灵活处理。

### 复杂度分析

时间复杂度 $$O(n)$$
空间复杂度 $$o(1)$$

## 题解2 - hashset

用 hashset 的做法与前一题相同，只是返回 node 即可。

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
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> st;
        
        ListNode* p = head;
        while(p != NULL) {
            if (st.find(p) != st.end()) {
                return p;
            } else {
                st.insert(p);
            }
            p = p -> next;
        }
        return NULL;
    }
};
```

### 复杂度分析

时间复杂度 $$O(n)$$
空间复杂度 $$o(n)$$
