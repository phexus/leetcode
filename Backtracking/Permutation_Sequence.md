# Permutation Sequence

Tags: Math, Backtracking, Medium

## problem

The set `[1,2,3,…,*n*]` contains a total of *n*! unique permutations.

By listing and labeling all of the permutations in order,
We get the following sequence (ie, for *n* = 3):

1. `"123"`
2. `"132"`
3. `"213"`
4. `"231"`
5. `"312"`
6. `"321"`

Given *n* and *k*, return the *k*th permutation sequence.

**Note:** Given *n* will be between 1 and 9 inclusive.

## Solution

这道题还是不好理解的，[参考]() [参考](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutation_sequence.html)

和题 [Permutation Index](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutation_index.html) 正好相反，这里给定第几个排列的相对排名，输出排列值。和不同进制之间的转化类似，这里的『进制』为`1!, 2!...`, 以n=3, k=4为例，我们从高位到低位转化，直觉应该是用 `k/(n-1)!`, 但以 n=3,k=5 和 n=3,k=6 代入计算后发现边界处理起来不太方便，故我们可以尝试将 k 减1进行运算，后面的基准也随之变化。第一个数可以通过`(k-1)/(n-1)!`进行计算，那么第二个数呢？联想不同进制数之间的转化，我们可以通过求模运算求得下一个数的`k-1`, 那么下一个数可通过`(k2 - 1)/(n-2)!`求得，这里不理解的可以通过进制转换类比进行理解。和减掉相应的阶乘值是等价的。

下面以一个例子来做示范：

如何找出第16个（按字典序的）{1,2,3,4,5}的全排列？

1. 首先用16-1得到15

2. 用15去除4! 得到0余15

3. 用15去除3! 得到2余3

4. 用3去除2! 得到1余1

5. 用1去除1! 得到1余0

有0个数比它小的数是1，所以第一位是1

有2个数比它小的数是3，但1已经在之前出现过了所以是4

有1个数比它小的数是2，但1已经在之前出现过了所以是3

有1个数比它小的数是2，但1,3,4都出现过了所以是5

最后一个数只能是2

综上，由于 0 -9 不能用重，所以需要用一个数组来存放，每次算完一轮要更新， 即把空出来的部分，由后面的部分补齐，这样才能根据前面比他小的数有几个（即 rank）来拿到对应的值。

代码还有有个小细节，就是一开始把k--，目的是让下标从0开始，这样下标就是从0到n-1，不用考虑n时去取余，更好地跟数组下标匹配。

```cpp
class Solution {
public:
    string getPermutation(int n, int k) {
        int factor = 1;
        vector<int> nums;
        string ans;
        
        for (int i = 0; i < n; ++i) {
            nums.push_back(i + 1);
            factor *= i + 1;
        }
        
        --k;
        for (int i = 0; i < n; ++i) {
            factor /= n - i; // 更新阶乘因子
            int rank = k / factor;
            ans.push_back(nums[rank] + '0');
            k %= factor;
            for (int j = rank; j < n; ++j) nums[j] = nums[j + 1]; // 整个往前挪动
        }
        
        return ans;
    }
};
```

