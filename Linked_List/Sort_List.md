# Sort List

Tags: Linked_List, Medium

## Problem

Sort a linked list in $$O(nlog(n))$$ time using constant space complexity.

## 思路

[参考解法](https://algorithm.yuanbin.me/zh-hans/linked_list/sort_list.html) 详细介绍了关于链表排序的思路

`链表的排序操作，对于常用的排序算法，能达到 O(n \log n)O(nlogn)的复杂度有快速排序(平均情况)，归并排序，堆排序。快速排序不一定能保证其时间复杂度一定满足要求，归并排序和堆排序都能满足复杂度的要求。在数组排序中，归并排序通常需要使用 O(n)O(n) 的额外空间，也有原地归并的实现，代码写起来略微麻烦一点。但是对于链表这种非随机访问数据结构，所谓的「排序」不过是指针next值的变化而已，主要通过指针操作，故仅需要常数级别的额外空间，满足题意。堆排序通常需要构建二叉树，在这道题中不太适合。`

后序归并，先求得左半部分链表的表头，再求得右半部分链表的表头，最后进行归并操作。

归并需要按长度等分链表，等分能保证线性对数的时间复杂度。参考解法提供了两种思路，一种是对链表进行遍历求得长度，另一种就是通常用的快慢指针法获取中间结点。这里我用了快慢指针法，感觉遍历求长度的做法笨了点。（注意，快满指针法，中间指针会划分到左半部分链表）

## 解法1 - 归并排序 - 递归

注意， `while (fast -> next && fast -> next -> next)` 的循环条件，确保只有两个元素时的 corner case（否则会死循环）
通过这样两个快慢指针来找到中间元素很常见，Convert Sorted List to Binary Search Tree 里也用到了，注意 循环条件的不同。

快慢指针的另一种写法，将 fast 初始化为 head -> next
```cpp
        ListNode *slow = head, *fast = head->next;
        while(NULL != fast && NULL != fast->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
```
` 在递归和迭代程序中，需要尤其注意终止条件的确定，以及循环语句中变量的自增，以防出现死循环或访问空指针。`

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
    ListNode* sortList(ListNode* head) {
        if (! head || ! head -> next) return head;
        
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast -> next && fast -> next -> next) {
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        
        fast = slow -> next;
        slow -> next = NULL;
        
        ListNode* left = sortList(head);
        ListNode* right = sortList(fast);
        
        return merge2Lists(left, right);
    }
    
private:
    ListNode* merge2Lists(ListNode* left, ListNode* right) {
        ListNode dummy(0);
        ListNode* curr = & dummy;
        
        while (left && right) {
            if (left -> val < right -> val) {
                curr -> next = left;
                left = left -> next;
            } else {
                curr -> next = right;
                right = right -> next;
            }
            curr = curr -> next;
        }
        
        curr -> next = left ? left : right;
        return dummy.next;
    }
};
```

## 解法2 - 归并排序 - 自底向上

`归并排序，总的时间复杂度是（nlogn),但是递归的空间复杂度并不是常数（和递归的层数有着关；递归的过程是自顶向下，好理解；这里提供自底向上的非递归方法；`

该实现较为复杂，涉及到 for loop 嵌套，边界问题很是头疼，之后有空再补充。。。