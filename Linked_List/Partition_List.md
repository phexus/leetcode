# Partition List

Tags: Linked_List, Medium

## Problem

Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.

You should preserve the original relative order of the nodes in each of the two partitions.

For example,
Given 1->4->3->2->5->2 and x = 3,
return 1->2->2->4->3->5.

## 解法

这道题很简单，只要求将小于x的节点放到前面，而并不要求对元素进行排序。由于左右头节点不确定，我们可以使用两个dummy节点。
需要注意的是最后要对最右边结点的 next 指针做处理，`node2 -> next = NULL;`，否则有可能成环！！（第一次提交时就犯了这个错）
最后把右边结点挂载到左边结点末尾即可。

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
    ListNode *partition(ListNode *head, int x) {
        if (! head) return NULL;
        
        ListNode dummy1(0);
        ListNode dummy2(0);
        ListNode* node1 = &dummy1;
        ListNode* node2 = &dummy2;
        
        while (head) {
            if (head -> val < x) {
                node1 -> next = head;
                node1 = node1 -> next;
            } else {
                node2 -> next = head;
                node2 = node2 -> next;
            }
            head = head -> next;
        }
        // 这步操作很关键，否则有可能成环！！！
        node2 -> next = NULL;
        node1 -> next = dummy2.next;
        return dummy1.next;
    }
};
```

### 复杂度分析

遍历链表一次，时间复杂度近似为 $$O(n)$$, 使用了两个 dummy 节点及中间变量，空间复杂度近似为 $$O(1)$$.