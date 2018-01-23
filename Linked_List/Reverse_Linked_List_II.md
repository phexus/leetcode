# Reverse Linked List II

Tags: Linked_List, Medium

## Problem

Reverse a linked list from position m to n. Do it in-place and in one-pass.

For example:
Given 1->2->3->4->5->NULL, m = 2 and n = 4,

return 1->4->3->2->5->NULL.

Note:
Given m, n satisfy the following condition:
1 ≤ m ≤ n ≤ length of list.

## 题解1 - 四个指针保存相关位置 + 拼接

与 Reverse Linked List 不同，该题指定了一定的区域。链表的头结点不定，所以要想到利用 dummy node。若是按照上一题的解题思路来翻转，要特别注意边界问题：
1. 翻转指定区域，会涉及到与原来链表连接的问题，所以要用指针保存第 m-1，n+1 的结点。
2. 用之前的翻转指针的思路，需要循环 n - m 次。
3. 连接 m-1 到 n， m 到 n + 1
4. 返回 dummy.next

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
    ListNode *reverseBetween(ListNode *head, int m, int n) {
        if (! head || m > n) return NULL;
         
        ListNode dummy(0);
        dummy.next = head;
        ListNode* node = &dummy;
        
        for (int i = 1; i < m; ++ i) {
            //if (node == NULL) {
            //    return NULL;
            //}
            node = node -> next;
        }
        
        ListNode* premNode = node;
        ListNode* mNode = node->next;
        ListNode* nNode = mNode;
        ListNode* postnNode = nNode->next;
        
        for (int i = m; i < n; ++ i) {
            //if (postnNode == NULL) {
            //    return NULL;
            //}
            ListNode* tmp = postnNode -> next;
            postnNode -> next = nNode;
            nNode = postnNode;
            postnNode = tmp;
        }
         
        premNode -> next = nNode;
        mNode -> next = postnNode;
        return dummy.next;
    }
};

```

### 源码分析

两个for 循环中，其实可以不用检查结点是否为空的情况，因为题目已经约定了`1 ≤ m ≤ n ≤ length of list`

## 题解２ - 逐个挂载

换一种翻转链表的思路，[参考解法](https://discuss.leetcode.com/topic/15034/12-lines-4ms-c/2)
这种思路有点类似沉底，把第 m 个结点拎起来，一点点往后挪。
1. 已知要翻转的区域的前一个结点，即第 m - 1 个结点
2. 先用 tmp 保存当前结点的下一个结点
3. 当前结点的 next 指向 tmp 的 下一个结点，即原本 node 的 next 的 next
4. tmp 的 next 指向 pre 的 next，即此时状态下的翻转链表头
5. pre 的 next 指向 tmp。

总结一下，即把当前节点的下一个节点插入到翻转链表头，这样相当于整个翻转链表结点往后错了一格，循环 n - m 次即可，这样都不用单独处理链表的链接问题了，因为这种思路，一次动了不止一个指针，而第一种解法一次只动一个指针。

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
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        if (! head || m > n) return NULL;
        
        ListNode dummy(0);
        dummy.next = head;
        ListNode* pre = &dummy;
        
        for (int i = 1; i < m; ++ i) {
            pre = pre -> next;
        }
        ListNode* node = pre -> next;
        
        for (int i = m; i < n; ++ i) {
            ListNode* tmp = node -> next;
            node -> next = tmp -> next;
            tmp -> next = pre -> next;
            pre -> next = tmp;
        }
        
        return dummy.next;
    }
};

```

###　源码分析

其实综合两种写法，可以看出调整指针的一定规律：
下一次要调整的就是上一行等式的等号右边部分，顺着这个规律就能很好的想到指针的移向了
 
```cpp
            ListNode* tmp = postnNode -> next;
            postnNode -> next = nNode;
            nNode = postnNode;
            postnNode = tmp;
```

```cpp
            ListNode* tmp = node -> next;
            node -> next = tmp -> next;
            tmp -> next = pre -> next;
            pre -> next = tmp;
```