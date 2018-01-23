# Add Two Numbers

Tags: Linked_List, Medium

## Problem

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.

## 题解1 - 我的解法 - ugly。。

原本认为这个题只要处理好加法进位就没问题了，结果自己实现时错误频出，各种边界问题没有考虑好。。。提交了 n 次后才 ac，代码也被改成了下面的这个样子，很是 ugly。
首先，引入 dummy node 的目的是为了把处理头结点也涵盖到循环里，这样返回是返回 dummy.next 就可以了，不用单独处理头结点指针。
用 carry 表示进位。
之所以这这道题容易出错，是因为两个链表的长度是有可能不等长的，完全不可控，于此同时进位信息却会影响后面的值，处理不好代码就会显得很啰嗦。另外最后还要处理最高位的进位，即时 l1 和 l2 的元素都遍历完了。

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
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        ListNode dummy(0);
        ListNode* node = &dummy;
        int carry = 0;
        
        while (l1 || l2) {
            if (l1 && l2) {
                int val = l1 -> val + l2 -> val + carry;
                node -> next = new ListNode(val % 10);
                carry = val / 10;
                node = node -> next;
                l1 = l1 -> next;
                l2 = l2 -> next;
            } else if (l1 && ! l2) {
                int val = l1 -> val + carry;
                l1 -> val = val % 10;
                carry = val / 10;
                node -> next = l1;
                node = node -> next;
                l1 = l1 -> next;
            } else if (l2 && ! l1){
                int val = l2 -> val + carry;
                l2 -> val = val % 10;
                carry = val / 10;
                node -> next = l2;
                node = node -> next;
                l2 = l2 -> next;
            }
        }
        
        if (carry && ! l1 && ! l2) node -> next = new ListNode(carry);
        
        return dummy.next;
    }
};
```

##　解法２ - carry 代替 sum 累加

[参考解法 - 韩迅](https://www.nowcoder.com/questionTerminal/56f8d422eae04f129c8e5a05299ae275)

这里的 carry 其实已经不完全起了进位的作用，还起到了存储了，大大减少了临时变量的数量。
注意这里的 while 的条件 `l1 || l2 || carry`，因为要考虑到最高位进位的情况

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        if (! l1) return l2;
        if (! l2) return l1;
        
        ListNode dummy(0);
        ListNode* node = &dummy;
        int carry = 0;
        
        while (l1 || l2 || carry) {
            if (l1) {
                carry += l1 -> val;
                l1 = l1 -> next;
            }
            
            if (l2) {
                carry += l2 -> val;
                l2 = l2 -> next;
            }
            
            node -> next = new ListNode(carry % 10);
            node = node -> next;
            carry /= 10;
        }
        
        return dummy.next;
    }
};
```

##　解法３　

[参考解法](https://algorithm.yuanbin.me/zh-hans/linked_list/add_two_numbers.html)

其实和 解法2 差不多，只是用了两个三元表达式，多了几个临时变量，感觉还是解法2更优雅点。
另外`if (l1) l1 = l1 -> next;` 也可以用三元表达式来做 `l1 = l1?l1 -> next:NULL;`

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
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        if (! l1) return l2;
        if (! l2) return l1;
        
        ListNode dummy(0);
        ListNode* node = &dummy;
        int carry = 0;
        
        while (l1 || l2 || carry) {
            int l1Val = l1 ? l1 -> val : 0;
            int l2Val = l2 ? l2 -> val : 0;
            
            int sum = l1Val + l2Val + carry;
            node -> next = new ListNode(sum % 10);
            carry = sum / 10;
            node = node -> next;
            
            if (l1) l1 = l1 -> next;
            if (l2) l2 = l2 -> next;
        }
        return dummy.next;
    }
};
```

###　复杂度分析

时间和空间复杂度均为 $$O(max(L1, L2))$$.