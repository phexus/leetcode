# Combinations

Tags: Backtracking

## Problem

Given two integers *n* and *k*, return all possible combinations of *k* numbers out of 1 ... *n*.

For example,
If *n* = 4 and *k* = 2, a solution is:

```
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

## Solution 1 - 分治

Subsets 问题的简化版，即已经给定了 k，不用遍历。

其实从数学的角度讲，就是递推公式 $$C(n, k) = C(n - 1, k - 1) + C(n - 1, k)$$ 

处理好边界问题， `k == 0` 应该追加结果，`begin > n` 返回

```cpp
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        vector<int> combination;
        combine(n, 1, k, combination, ans);
        
        return ans;
    }
    
    void combine(int n, int begin, int k, vector<int>& combination, vector<vector<int>>& ans) {
        if (k == 0) {
            ans.push_back(combination);
            return;
        }
        if (begin > n) return;
        
        combination.push_back(begin);
        combine(n, begin + 1, k - 1, combination, ans);
        combination.pop_back();
        combine(n, begin + 1, k, combination, ans);
    }
};
```

## Solution 2 - 回溯法

和上面的解法对比一下就能发下，核心的辅助函数形参都是一样的，都需要一个 begin，只是回溯法的边界只用处理 k 就好了，因为 begin 已经在 for loop 里处理过了。

[该法的详细解释](http://blog.csdn.net/versencoder/article/details/52071930)

```cpp
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        vector<int> combination;
        backtracking(n, 1, k, combination, ans);
        
        return ans;
    }
    
    void backtracking(int n, int begin, int k, vector<int>& combination, vector<vector<int>>& ans) {
        if (k < 0) return;
        if (k == 0) {
            ans.push_back(combination);
        } else {
            for (int i = begin; i <= n; ++i) {
                combination.push_back(i);
                backtracking(n, i + 1, k - 1, combination, ans);
                combination.pop_back();
            }
        }
    }
};
```

## Solution 3 - DFS

比起第二种解法，感觉就是换了种写法，边界条件也做了调整，以 临时数组的长度作为判断依据，不断尝试添加后面的元素，其实也是回溯法。不过注意这里的 k 因为要作为判断依据，所以 递归中的 k 是不变的。

`dfs(n, i + 1, k, combination, ans);`

```cpp
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        vector<int> combination;
        dfs(n, 1, k, combination, ans);
        
        return ans;
    }
    
    void dfs(int n, int pos, int k, vector<int>& combination, vector<vector<int>>& ans) {
        if (combination.size() == k) {
            ans.push_back(combination);
        } else {
            for (int i = pos; i <= n; ++i) {
                combination.push_back(i);
                dfs(n, i + 1, k, combination, ans);
                combination.pop_back();
            }
        }
    }
};
```

### 复杂度

状态数 $$C_n^k$$, 每组解有 k 个元素，故时间复杂度应为 $$O(n^k)$$. 不考虑递归的消耗的话，list 只保留最多 k 个元素，空间复杂度 $$O(k)$$.

## Solution 4 - Iterative

[参考](https://leetcode.com/problems/combinations/discuss/26992/Short-Iterative-C++-Answer-8ms) 感觉不太好理解

``` cpp
class Solution {
public:
	vector<vector<int>> combine(int n, int k) {
		vector<vector<int>> result;
		int i = 0;
		vector<int> p(k, 0);
		while (i >= 0) {
			p[i]++;
			if (p[i] > n) --i;
			else if (i == k - 1) result.push_back(p);
			else {
			    ++i;
			    p[i] = p[i - 1];
			}
		}
		return result;
	}
};
```

