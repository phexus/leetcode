# Reverse Nodes in k-Group

Tags: Linked_List, Hard

## Problem

Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.

You may not alter the values in the nodes, only nodes itself may be changed.

Only constant memory is allowed.

For example,
Given this linked list: 1->2->3->4->5

For k = 2, you should return: 2->1->4->3->5

For k = 3, you should return: 3->2->1->4->5

## 题解

本题在解决了前两道 rever node 的相关题目之后就显得相对简单了，但是要注意一些小的细节。[参考解法](https://siddontang.gitbooks.io/leetcode-solution/content/linked_list/reverse_linked_list.html) 中把 reverse 方法单独拎出来了，所以会清晰很多。我下面的解法是写在一起的，所以要注意一些迭代变量的环境保存。

另外，在做每隔 k 个结点找遍历开始结点时，for loop 里的语句写颠倒了，结果导致空指针，一定要注意先移动再判断。

这种 reverse 的方法，是一种通用的套路，一定是需要一个前驱结点的，但是由于是部分 reverse，所以还需要一个 end 结点，作为结束的标志。

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
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (! head || k <= 1) return head;
        
        ListNode dummy(0);
        dummy.next = head;
        
        ListNode* prev = &dummy;
        ListNode* node = &dummy;
        
        while (node) {
            prev = node;
            
            for (int i = 0; i < k; ++ i) {
                //下面两句的顺序不能颠倒，一定要在指针后移之后再判断，否则有可能为 NULL，后续会有空指针的问题
                node = node -> next;
                if (! node) return dummy.next;
            }
            
            ListNode* p = prev -> next;
            // 这里必须用 end 来保存，用作后续的判断条件，因为 node -> next 在循环过程中会变
            ListNode* end = node -> next;
            while (p -> next != end) {
                ListNode* tmp = p -> next;
                p -> next = tmp -> next;
                tmp -> next = prev -> next;
                prev -> next = tmp;
            }
            node = p;
        }
        return dummy.next;
    }
};
```

###　复杂度分析

遍历了一遍链表，时间复杂度　$$O(n)$$
空间复杂度　$$O(1)$$