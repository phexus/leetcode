# Delete Node in a Linked List

Tags: Linked_List, Easy

## Problem

Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.

Supposed the linked list is 1 -> 2 -> 3 -> 4 and you are given the third node with value 3, the linked list should become 1 -> 2 -> 4 after calling your function.

## 解法

由于不知道前驱结点，所以只能通过改变 node 的值的方式来变相的删除结点，把下一个结点的值覆盖到本结点，再删除下一个结点。（题目中已经提到了最后一个结点除外，所以不会有 corner case）

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
    void deleteNode(ListNode* node) {
        node -> val = node -> next -> val;
        ListNode* tmp = node -> next;
        node -> next = tmp -> next;
        delete tmp;
        tmp = NULL;
    }
};
```
## Reference

`剑指 offer` 一书 面试题 13 在 $$O(1)$$ 时间删除链表结点 是该题的完整版，需要考虑删除的结点是尾结点的情况，此时就得从链表的头结点开始遍历到尾结点的前序结点，并完成删除操作；还有一点容易遗漏的是加入 链表只有一个结点，此时在删除完该结点后，还需要把头结点设为 NULL，一般为了满足这一点，函数参数得是指向头结点指针的指针，这样才能修改头结点。