# Gray Code

Tags: Backtracking

## Problem

The gray code is a binary numeral system where two successive values differ in only one bit.

Given a non-negative integer *n* representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

For example, given *n* = 2, return `[0,1,3,2]`. Its gray code sequence is:

```
00 - 0
01 - 1
11 - 3
10 - 2

```

**Note:**
For a given *n*, a gray code sequence is not uniquely defined.

For example, `[0,2,3,1]` is also a valid gray code sequence according to the above definition.

For now, the judge is able to judge based on one instance of gray code sequence. Sorry about that.

## Solution 1 - 镜射排列

![Gray Code](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Binary-reflected_Gray_code_construction.svg/330px-Binary-reflected_Gray_code_construction.svg.png)

关于格雷码参考 [wiki](https://zh.wikipedia.org/wiki/%E6%A0%BC%E9%9B%B7%E7%A0%81) 

生成格雷码的一个方法就是镜像排列，n 位的格雷码可由 n-1位的格雷码递推，在最高位前顺序加0，逆序加1即可。实际实现时我们可以省掉在最高位加0的过程，因为其在数值上和前 n-1位格雷码相同。更通俗的讲，就是随着n变大，前面的数不用动，后面的数倒着拿出来再在首部加1即可。

下面的实现是从 0 开始的，因为从测试用例来看，n = 0 时应该输出 0，从 0 开始处理，high 的移位 i 就要从 0 开始算。

内层 for loop 的 ans.size() 不是常量，只是用来初始化。ans[j] + high 的操作用位或运算代替了，效率更高。

```cpp
class Solution {
public:
    vector<int> grayCode(int n) {
        if (n < 0) return vector<int>();
        vector<int> ans;
        ans.push_back(0);
        
        for (int i = 0; i < n; ++i) {
            int high = 1 << i;
            
            for (int j = ans.size() - 1; j >= 0; --j) {
                ans.push_back(ans[j] | high);
            }
        }
        
        return ans;
    }
};
```

### 复杂度

生成n 位的二进制码，时间复杂度 $$O(2^n)$$

 使用了`high`代表最高位的值便于后续相加，空间复杂度 $$O(1)$$

## Solution 2 - backtracking

第一种解法从定义出发更加简明，下面的这种解法是从 backtracking 的思路来做的。[参考](https://leetcode.com/problems/gray-code/discuss/29880/Backtracking-C++-solution) 其实也是从定义出发直接生成，不断翻转临近的二进制位。

画出树形图就很好理解了，典型的 backtracking 问题。（也是 DFS）

用到了 bitset 容器，方便做位运算的翻转。

```cpp
class Solution {
    void utils(bitset<32>& bits, vector<int>& result, int k){
        if (k==0) {
            result.push_back(bits.to_ulong());
        }
        else {
            utils(bits, result, k-1);
            bits.flip(k-1); // 翻转第 k - 1 位
            utils(bits, result, k-1);
        }
    }
public:
    vector<int> grayCode(int n) {
        bitset<32> bits;
        vector<int> result;
        utils(bits, result, n);
        return result;
    }
};
```

下面是不用 bitset 这种 stl 容器，通过 异或 移位来实现 第 k - 1 位的翻转

```cpp
class Solution {
public:
    vector<int> grayCode(int n) {
        if (n < 0) return vector<int>();
        int num = 0;
        vector<int> ans;
        backtracking(num, n, ans);
        return ans;
    }
    
    void backtracking(int& num, int n, vector<int>& ans) {
        if (n == 0) ans.push_back(num); // 退出情况已经涵盖了 n = 0 的输出
        else {
            backtracking(num, n - 1, ans);
            num ^= (1 << (n - 1));
            backtracking(num, n - 1, ans);
        }
    }
};
```

### 复杂度

递归最后一层有 $$2^n$$ 个状态，所以复杂度同上

空间复杂度递归了 n 层，所以有 $$O(n)$$ 的栈消耗。