# Merge k Sorted Lists

Tags: Linked_List, Hard

## Problem

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

本题的详细三种由易到难的解法[参考资料](https://algorithm.yuanbin.me/zh-hans/linked_list/merge_k_sorted_lists.html)
[leetcode solution](https://leetcode.com/problems/merge-k-sorted-lists/solution/) 也给出了 5 种解法

## 解法1 - 选择归并(TLE) - 效率较低的解法

参考之前做过的 Merge Two Sorted Lists，很自然能推广到 k 个 lists。从 k curr->next(和选择排序思路有点类似)，同时该节点所在的链表表头节点往后递推一个。直至 curr 遍历完 k 个链表的所有节点，此时表头节点均为NULL, 返回dummy->next.

需要注意的是：
1. `int tmpVal = INT_MAX；` 是为了先定一个较大的值，才好去找最小的值 `int index = -1;` 是为了不干扰后续的记录，因为 0 是有意义的，和实际冲突。
2. 使用 curr 表示每次归并后的新链表末尾节点。
3. count用于累计链表表头节点为NULL的个数，若与 vector 大小相同则代表所有节点均已遍历完。
4. tempVal用于保存每次比较 vector 中各链表表头节点中的最小值，index保存本轮选择归并过程中最小值对应的链表索引，用于循环结束前递推该链表表头节点。

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
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if (lists.empty()) return NULL;
        
        ListNode dummy(0);
        ListNode* curr = & dummy;
        
        while (true) {
            int count = 0;
            int tmpVal = INT_MAX;
            int index = -1;
            
            for (int i = 0; i < lists.size(); ++ i) {
                if (! lists[i]) ++ count;
                
                if (count == lists.size()) return dummy.next;
                
                if (lists[i] && lists[i] -> val < tmpVal) {
                    tmpVal = lists[i] -> val;
                    index = i;
                }
            }
            
            curr -> next = lists[index];
            lists[index] = lists[index] -> next;
            curr = curr -> next;
        }
    }
};
```

### 复杂度分析

由于每次for循环只能选择出一个最小值，总的时间复杂度最坏情况下为 $$O(k \cdot \sum ^{k}_{i=1}l_i)$$
空间复杂度近似为 $$O(1)$$

###　解法２ - 迭代调用 Merge Two Sorted Lists

反复调用　Merge Two Sorted Lists，直到 vector 内的所有链表都合并完。

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
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if (lists.empty()) return NULL;
        
        ListNode* newHead = lists[0];
        for (int i = 1; i < lists.size(); ++ i) {
            newHead = merge2Lists(newHead, lists[i]);
        }
        return newHead;
    }
    
    ListNode *merge2Lists(ListNode *left, ListNode *right) {
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

### 复杂度分析

合并两个链表时最差时间复杂度为 $$O(l_1+l_2)$$， 第 1 个链表要遍历 k-1 次，第 2 个链表遍历 k-2 次，...，第 k 个链表遍历 1 次，所以总的时间复杂度为 $$O(\sum _{i=1} ^{k} (k-i) \cdot l_i)$$

## 解法3 - 二分调用 Merge Two Sorted Lists

第i个链表 $$l_i$$ 被遍历了 $$k-i$$ 次。
从中间索引处进行二分归并后，每个链表参与合并的次数变为 $$\log k$$, 故总的时间复杂度可降至 $$\log k \cdot \sum _{i=1} ^{k} l_i$$. 优化幅度较大。

1. 需要建立二分递归模型，新建一个函数引入起止位置会比较方便。
2. `(start + 1 == end)` 这一边界条件其实是可以去掉的，但是加上会节省递归调用的栈空间
3. merge2Lists 中传入的参数，为 lists[start] 而不是 start
4. 在 mergeKLists 中调用 mergeKList 时传入的 end 参数为 lists.size() - 1，而不是 lists.size()
> 感觉这里的边界是不是也可以填 lists.size()，只要处理递归退出条件就行？不过我初步尝试报错，之后再研究下，可以参考 array 里的相关题目

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
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if (lists.empty()) return NULL;
        
        return mergeKList(lists, 0, lists.size() - 1);
    }
    
    ListNode* mergeKList(vector<ListNode *> &lists, int start, int end) {
        if (start == end) return lists[start];
        //if (start + 1 == end) return merge2Lists(lists[start], lists[end]);
        
        int mid = (start + end) >> 1;
        ListNode* left = mergeKList(lists, start, mid);
        ListNode* right = mergeKList(lists, mid + 1, end);
        return merge2Lists(left, right);
    }
    
    ListNode *merge2Lists(ListNode *left, ListNode *right) {
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

## 解法4 - divide and conquer

详细图解见 [Approach #5 Merge with Divide And Conquer](https://leetcode.com/problems/merge-k-sorted-lists/solution/) 解法5

比之解法 3 更加清晰明了，但是边界条件的处理非常巧妙，我也是参考了 python 的解法才写出来的。
由于有可能是奇数个链表，这样就没办法完成配对了，所以要把最后一个元素跳过本次的 merge， for 循环的判断条件为 `i < length - interval`， 另外由于每次 merge 的第一个参数实际上是 跳着取的，所以 i 的自增为 `i = i + interval * 2`，最后别忘了更新 interval

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
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty()) return NULL;
        
        int length = lists.size();
        int interval = 1;
        
        while (interval < length) {
            for (int i = 0; i < length - interval; i = i + interval * 2) {
                lists[i] = merge2Lists(lists[i], lists[i + interval]);
            }
            interval *= 2;
        }
             
        return lists[0];
    }
    ListNode* merge2Lists(ListNode* left, ListNode* right) {
        ListNode dummy(0);a
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
        
        curr -> next = (left) ? left : right;
        
        return dummy.next;
    }
};
```

###　复杂度分析

与解法３相同　时间复杂度　 $$\log k \cdot \sum _{i=1} ^{k} l_i$$

##　解法４ - 优先队列

还可以用优先队列去做，以后做到队列那章再来补充。

[Approach #3 Optimize Approach 2 by Priority Queue](https://leetcode.com/articles/merge-k-sorted-list/)
[解题思路](https://www.nowcoder.com/questionTerminal/65cfde9e5b9b4cf2b6bafa5f3ef33fa6)
`解题思路很简单，使用优先队列，每次从队列中找到最小的数取出来，然后把该数的下一个结点添加到优先队列中（如果没有后继结点则不添加），直到队列为空，这个时候所有的链表都合并结束了。`
