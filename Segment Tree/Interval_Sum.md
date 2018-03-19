# Interval Sum

Tags: Segment Tree, Binary Tree, Medium, Lintcode

## Problem

Given an integer array (index from 0 to n-1, where n is the size of this array), and an query list. Each query has two integers `[start, end]`. For each query, calculate the sum number between index start and end in the given array, return the result list.

##### ** Notice

We suggest you finish problem [Segment Tree Build](http://www.lintcode.com/problem/segment-tree-build/), [Segment Tree Query](http://www.lintcode.com/problem/segment-tree-query/) and [Segment Tree Modify](http://lintcode.com/en/problem/segment-tree-modify/) first.



**Example**

For array `[1,2,7,8,5]`, and queries `[(0,4),(1,2),(2,4)]`, return `[23,9,20]`

**Challenge** ****

O(logN) time for each query

## Solution 1 - Segment Tree

标准的线段树，有了 Segment Tree Build II 和 Segment Tree Query 的积累，一样的思路来做，只不过写起来比较累，得要建树，再查询，很容易出错。

query 的 `if (!root || root -> start > end || root -> end < start) return 0;` 这部分 我第一次写的时候就写成了 `if (!root || root -> start > start || root -> end < end) return 0;` 排查了好久才发现。。。

```cpp
/**
 * Definition of Interval:
 * classs Interval {
 *     int start, end;
 *     Interval(int start, int end) {
 *         this->start = start;
 *         this->end = end;
 *     }
 * }
 */

struct SegmentTree {
    int start, end;
    long long sum;
    SegmentTree* left;
    SegmentTree* right;

    SegmentTree(int start, int end, long long sum) : start(start), end(end), sum(sum) {}

};

class Solution {
public:
    /**
     * @param A: An integer list
     * @param queries: An query list
     * @return: The result list
     */
    vector<long long> intervalSum(vector<int> &A, vector<Interval> &queries) {
        if (A.empty() || queries.empty()) return vector<long long> ();
        SegmentTree* root = build(A, 0, A.size() - 1);
        vector<long long> ans;
        for (Interval& interval : queries) {
            ans.push_back(query(root, interval.start, interval.end));
        }
        return ans;
    }

private:
    SegmentTree* build(vector<int>& A, int start, int end) {
        if (start > end) return nullptr;

        SegmentTree* root = new SegmentTree(start, end, A[start]);

        if (start == end) return root;

        root -> left = build(A, start, (start + end) / 2);
        root -> right = build(A, (start + end) / 2 + 1, end);
        root -> sum = root -> left -> sum + root -> right -> sum;

        return root;
    }

    long long query(SegmentTree* root, int start, int end) {
        if (!root || root -> start > end || root -> end < start) return 0;

        if (root -> start >= start && root -> end <= end) return root -> sum;

        long long l = query(root -> left, start, end);
        long long r = query(root -> right, start, end);

        return l + r;
    }
};
```

### 九章算法的解法

其实是一样的思路，只不过把线段树定义成了类，用了 static 方法。

```cpp
/**
 * Definition of Interval:
 * classs Interval {
 *     int start, end;
 *     Interval(int start, int end) {
 *         this->start = start;
 *         this->end = end;
 *     }
 */
class SegmentTree {
public:
    SegmentTree *left, *right;
    int start, end;
    long long sum;
    SegmentTree(int start, int end, int sum=0): left(NULL), right(NULL), start(start), end(end), sum(sum) {};
    
    static SegmentTree *build(int start, int end, vector<int> &a) {
        if (start > end)
            return NULL;

        SegmentTree * node = new SegmentTree(start, end, a[start]);
        if (start == end) 
            return node;

        int mid = (start + end) / 2;
        node->left = build(start, mid, a);
        node->right = build(mid+1, end, a);
        node->sum = node->left->sum + node->right->sum;

        return node;
    }

    static long long query(SegmentTree *root, int start, int end) {
        if (start <= root->start &&  root->end <= end) {
            return root->sum;
        }
        
        if (root->left->end >= end) {
            return query(root->left, start, end);
        }
        if (root->right->start <= start) {
            return query(root->right, start, end);
        }
        
        long long leftsum = query(root->left, start, root->left->end);
        long long rightsum = query(root->right, root->right->start, end);
        return leftsum + rightsum;
    }
};


class Solution { 
public:
    /**
     *@param A, queries: Given an integer array and an query list
     *@return: The result list
     */
    vector<long long> intervalSum(vector<int> &A, vector<Interval> &queries) {
        // write your code here
        SegmentTree * root = SegmentTree::build(0, A.size()-1, A);
        vector<long long> result;

        int len = queries.size();
        for (int i = 0; i < len; ++i)
            result.push_back(SegmentTree::query(root, queries[i].start, queries[i].end));

        return result;
    }
};
```

