# Copy List with Random Pointer

Tags: Linked_List, Medium

## Problem

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

[参考题解](https://algorithm.yuanbin.me/zh-hans/linked_list/copy_list_with_random_pointer.html) 中有非常详细的解释

## 解法1 - unordered_map + 两次遍历

`所有类似的深度拷贝题目的传统做法，都是维护一个 hash table。即先按照复制一个正常链表的方式复制，复制的时候把复制的结点做一个 hash table，以旧结点为 key，新节点为 value。这么做的目的是为了第二遍扫描的时候我们按照这个哈希表把结点的 random 指针接上。`

参考题解里给出的步骤，深拷贝步骤如下；
1. 根据 next 指针新建链表
2. 维护新旧节点的映射关系
3. 拷贝旧链表中的 random 指针
4. 更新新链表中的 random 指针

这里的第三步其实感觉没必要，完全可以在第4步中用一个指向原链表的指针来找到 random 指针

```cpp
/**
 * Definition for singly-linked list with a random pointer.
 * struct RandomListNode {
 *     int label;
 *     RandomListNode *next, *random;
 *     RandomListNode(int x) : label(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    RandomListNode *copyRandomList(RandomListNode *head) {
        if (! head) return head;
        
        unordered_map<RandomListNode*, RandomListNode*> randomMap;
        RandomListNode dummy(0);
        RandomListNode* newCurr = &dummy;
        RandomListNode* curr = head;
        
        while (curr) {
            newCurr -> next = new RandomListNode(curr -> label);
            newCurr = newCurr -> next;
            randomMap[curr] = newCurr;
            curr = curr -> next;
        }
        
        newCurr = dummy.next;
        curr = head;
        while (curr) {
            newCurr -> random = randomMap[curr -> random];
            curr = curr -> next;
            newCurr = newCurr -> next;
        }
        
        return dummy.next;
    }
};
```

###　复杂度分析

总共要进行两次扫描，所以时间复杂度是 $$O(2n)=O(n)$$, 在链表较长时可能会 TLE(比如 Python). 空间上需要一个哈希表来做结点的映射，所以空间复杂度也是 $$O(n)$$.

## 解法２ - 哈希 + 一次遍历

对解法1 的优化，减少到一次遍历。在创建完每个 next node，建立好 map 联系后，直接去创建 random node，都在一次遍历里完成。不过要注意的是节点加入哈希表之前进行一次判断(是否已经生成，避免对同一个值产生两个不同的节点)

```cpp
/**
 * Definition for singly-linked list with a random pointer.
 * struct RandomListNode {
 *     int label;
 *     RandomListNode *next, *random;
 *     RandomListNode(int x) : label(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    RandomListNode *copyRandomList(RandomListNode *head) {
        if (! head) return NULL;
        
        unordered_map<RandomListNode*, RandomListNode*> randomMap;
        RandomListNode dummy(0);
        RandomListNode* curr = head;
        RandomListNode* newCurr = & dummy;
        
        while (curr) {
            if (randomMap.count(curr) > 0) newCurr -> next = randomMap[curr];
            else {
                newCurr -> next = new RandomListNode(curr -> label);
                randomMap[curr] = newCurr -> next;
            }
            newCurr = newCurr -> next;
            
            if (curr -> random) {
                if (randomMap.count(curr -> random) > 0) newCurr -> random = randomMap[curr -> random];
                else {
                    newCurr -> random = new RandomListNode(curr -> random -> label);
                    randomMap[curr -> random] = newCurr -> random;
                }
            }
            
            curr = curr -> next;
        }
        
        return dummy.next;
    }
};
```

## 解法３

通过链表原来结构中的 next 指针来替代 hash table 做哈希。

```
总结起来，实际我们对链表进行了三次扫描，第一次扫描对每个结点进行复制，然后把复制出来的新节点接在原结点的 next 指针上，也就是让链表变成一个重复链表，就是新旧更替；第二次扫描中我们把旧结点的随机指针赋给新节点的随机指针，因为新结点都跟在旧结点的下一个，所以赋值比较简单，就是 node->next->random = node->random->next，其中 node->next 就是新结点，因为第一次扫描我们就是把新结点接在旧结点后面。现在我们把结点的随机指针都接好了，最后一次扫描我们把链表拆成两个，第一个还原原链表，而第二个就是我们要求的复制链表。因为现在链表是旧新更替，只要把每隔两个结点分别相连，对链表进行分割即可。
```

特别要注意指针迭代时是否要进两步，以及使用指针前要注意 最后结点 的 corner case。`if (curr -> random) curr -> next -> random = curr -> random -> next;` `if (newCurr -> next) newCurr -> next = newCurr -> next ->next;`


```cpp
/**
 * Definition for singly-linked list with a random pointer.
 * struct RandomListNode {
 *     int label;
 *     RandomListNode *next, *random;
 *     RandomListNode(int x) : label(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    RandomListNode *copyRandomList(RandomListNode *head) {
        if (! head) return NULL;
        
        RandomListNode* curr = head;
        
        //遍历并插入新的节点
        while (curr) {
            RandomListNode* newNode = new RandomListNode(curr -> label);
            newNode -> next = curr -> next;
            curr -> next = newNode;
            curr = curr -> next -> next;
        }
        
        //设置新节点的random
        curr = head;
        while (curr) {
            if (curr -> random) curr -> next -> random = curr -> random -> next; //这里的 if 判断很重要
            curr = curr -> next -> next;
        }
        
        //断开链表
        RandomListNode* newHead = head -> next;
        curr = head;
        while (curr) { // 注意这里需要对新节点和老结点都处理
            RandomListNode* newCurr = curr -> next;
            curr -> next = curr -> next -> next;
            curr = curr -> next;
            if (newCurr -> next) newCurr -> next = newCurr -> next ->next;
        }
        
        return newHead;
    }
};
```

###　复杂度分析

总共进行三次线性扫描，所以时间复杂度是 $$O(n)$$。但不再需要额外空间的 hash table，所以空间复杂度是 $$O(1)$$。