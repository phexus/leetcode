# Insertion Sort List

Tags: Linked_List, Medium

## Problem

Sort a linked list using insertion sort.

## 解法

假设一个链表前n个节点是有序，第n + 1的节点需要遍历前n个，插入到合适位置就可以了

[参考解法](https://siddontang.gitbooks.io/leetcode-solution/content/linked_list/sort_list.html)

注意找到位置后如何将 curr 挂载到原链表后面的处理。

[补充资料](https://leetcode.com/problems/insertion-sort-list/discuss/46429) 这里面提到了 `So it might be better to actually copy the values into an array and sort them there.` 感觉这种时间复杂度的确有点高， 况且 sort list 一题中的解法更加优秀

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
    ListNode* insertionSortList(ListNode* head) {
        if (! head || ! head -> next) return head;
        
        ListNode dummy(0);
        ListNode* newCurr = & dummy;
        ListNode* curr = head;
        
        while (curr) {
            newCurr = & dummy;
            
            while (newCurr -> next && newCurr -> next -> val < curr -> val) newCurr = newCurr -> next;
            
            ListNode* tmp = newCurr -> next;
            newCurr -> next = curr;
            curr = curr -> next;
            newCurr -> next -> next = tmp;
        }
        
        return dummy.next;
    }
};
```

### 复杂度分析

时间复杂度 $$O(n^2)$$