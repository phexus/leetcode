# Remove Duplicates from Sorted List II

Tags: Linked_List, Medium

## Problem

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

For example,
Given 1->2->3->3->4->4->5, return 1->2->5.
Given 1->1->1->2->3, return 2->3.

## 题解1 - dummy node

[参考题解](https://algorithm.yuanbin.me/zh-hans/linked_list/remove_duplicates_from_sorted_list_ii.html)

和前一题不同，因为要删除所有重复的结点，涉及到头结点的变动，所以想到遍历时要使用 `node -> next`，确保之前的结点都是不重复的。这样一来，头结点就会有问题，所有这类问题的通用解决方案就是设置一个 dummy node 伪头结点。dummy node 的设置方法有两种，一种就是定义一个 ListNode 型的变量，不用管理内存，另一种就是定义一个指向 ListNode 型的指针变量，但是要注意最后返回前回收内存，否则会有内存泄露的问题。下面先用 指针型来实现。

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
    ListNode *deleteDuplicates(ListNode *head) {
        if (head == NULL || head -> next == NULL) return head;
        
        ListNode* dummy = new ListNode(0);
        dummy -> next = head;
        
        ListNode* node = dummy;
        while (node -> next && node -> next -> next) {
            if (node -> next -> val == node -> next -> next -> val) {
                int tmpVal = node -> next -> val;
                
                while (node -> next && node -> next -> val == tmpVal) {
                    ListNode* tmp = node -> next;
                    node -> next = tmp -> next;
                    delete(tmp);
                    tmp = NULL;
                }
            } else {
                node = node -> next;
            }
        }
        // 回收 dummy 内存
        head = dummy -> next;
        delete(dummy);
        dummy == NULL;
        
        return head;
    }
};
```

不用指针
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
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == NULL || head -> next == NULL) return head;
        
        ListNode dummy(0);
        dummy.next = head;
        
        ListNode* node = &dummy;
        while (node -> next && node -> next -> next) {
            if (node -> next -> val == node -> next -> next -> val) {
                int tmpVal = node -> next -> val;
                while (node -> next && node -> next -> val == tmpVal) {
                    ListNode* tmp = node -> next;
                    node -> next = tmp -> next;
                    delete(tmp);
                    tmp = NULL;
                }
            } else {
                node = node -> next;
            }
        }
        
        return dummy.next;
    }
};
```

### 源码分析

处理异常情况，空链表或单结点链表
最外层的 while loop 的判断条件为 `node -> next && node -> next -> next`
else 分支为 `node = node -> next;`

### 复杂度分析

时间复杂度线性，$$O(n)$$
使用了一个 dummy 和中间缓存变量，空间复杂度近似为 $$O(1)$$

## 题解２ - 递归

[参考题解](https://discuss.leetcode.com/topic/12892/simple-and-clear-c-recursive-solution)

递归的写法非常简洁，但是边界条件一定要处理好。`else` 的分支里的 `return deleteDuplicates(p)` 就已经处理好了第一个 head 结点，此时的 p 一定是第一个不连续的结点。递归的效率虽然不高，但是还是要好好学习递归的思想。

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
    ListNode* deleteDuplicates(ListNode* head) {
        if (! head || ! head -> next) return head;
        
        int val = head -> val;
        ListNode* p = head -> next;
        
        if (p -> val != val) {
            head -> next = deleteDuplicates(p);
            return head;
        } else {
            while (p && p -> val == val) p = p -> next;
            return deleteDuplicates(p);
        }
        
    }
};

```

## 题解3 - 指向指针的指针

[九章题解](http://www.jiuzhang.com/solutions/remove-duplicates-from-sorted-list-ii/#tag-highlight-lang-cpp)

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
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == NULL) return NULL;
        
        // 第一个后继指针
        ListNode* root = NULL;
        // 指向后继指针的指针
        ListNode** ppnext = &root;
        
        while (head) {
            if (head -> next == NULL || head -> val != head -> next -> val) {
                *ppnext = head;
                ppnext = &(head -> next);
                head = head -> next;
            } else {
                int val = head -> val;
                while (head && head -> val == val) {
                    ListNode* tmp = head;
                    head = tmp -> next;
                    delete tmp;
                    tmp = NULL;
                }
            }
        }
        // 非常重要，若最后的几个元素全是重复时，全部删除后，后继指针指向的最后一个元素的 next，应该置为NULL
        *ppnext = NULL;
        return root;
    }
};
```

### 源码分析

上述解法，没有创建 dummy node，因为通过分析，之前的解法所创建的 dummy node，也仅仅是其后继指针 next 起了作用，所以完全没必要分配一个堆内存，仅仅创建一个指向指针的指针即可。为了更加清晰，这里还创建了第一个后继指针，用来指向第一个元素。

特别要注意的是最后的 `*ppnext = NULL;`，一定要进行处理，否则部分分支会有错误。

[更精简的写法](https://discuss.leetcode.com/topic/7224/is-this-the-best-c-solution)
这里也直接用该指针遍历了，减少了创建其他指针，很巧妙的做法。

```cpp
class Solution {
public:
    ListNode *deleteDuplicates(ListNode *head) {
        ListNode **runner = &head;
        
        if(!head || !head->next)return head;
        
        while(*runner)
        {
            if((*runner)->next && (*runner)->next->val == (*runner)->val)
            {
                ListNode *temp = *runner;
                while(temp && (*runner)->val == temp->val)
                    temp = temp->next;
                
                *runner = temp;
            }
            else
                runner = &((*runner)->next);
        }
        
        return head;
    }
};
```