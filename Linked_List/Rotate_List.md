# Rotate List

Tags: Linked_List, Medium

## Problem

Given a list, rotate the list to the right by k places, where k is non-negative.

Example:

Given 1->2->3->4->5->NULL and k = 2,

return 4->5->1->2->3->NULL.

## 我的解法 - 快慢指针 + 对 k 取余

虽然是旋转链表，但我的理解是将右边的 k 个元素截取到前面来，在拼接起来。但是在初次提交的时候并没有全部通过case，原因是 k 有可能大于链表的长度，我本来认为这是非法输入直接返回 NULL 就好了，结果是要对 k 取模接着搞，这个题目描述真是坑。。
由于不是数组，所以并不知道链表的长度，所以我先遍历了一遍获得了链表的长度，直接对 k 取模，再按照 Remove Nth Node From End of List 的做法来找到倒数第 k 个元素，做截取。

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
    ListNode *rotateRight(ListNode *head, int k) {
        if (! head) return NULL;
        
        ListNode dummy(0);
        dummy.next = head;
        
        ListNode* slow = &dummy;
        ListNode* fast = &dummy;
        
        int count = 0;
        while (fast -> next) {
            ++ count;
            fast = fast -> next;
        }
        
        fast = &dummy;
        k %= count;
        
        for (int i = 0; i < k; ++ i) {
            fast = fast -> next;
        }
        
        while (fast -> next) {
            fast = fast -> next;
            slow = slow -> next;
        }
        
        fast -> next = dummy.next;
        dummy.next = slow -> next;
        slow -> next = NULL;
        
        return dummy.next;
    }
};
```

##　解法２ - 链接链表成环

[参考解法](https://www.nowcoder.com/questionTerminal/afbec6b9d4564c63b2c149ccc01c5678)

先遍历一遍，得出链表长度len，注意k可能会大于len，因此k%=len。
将尾结点next指针指向首节点，形成一个环，接着往后跑len-k步，从这里断开，就是结果


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
    ListNode* rotateRight(ListNode* head, int k) {
        if (! head || k == 0) return head;
        
        int len = 1;
        ListNode* p = head;
        while (p -> next) {
            p = p -> next;
            ++ len;
        }
        
        p -> next = head;
        int count = len - k % len;
        
        for (int i = 0; i < count; ++ i) p = p -> next;
        
        head = p -> next;
        p -> next = NULL;
        return head;
    }
};
```