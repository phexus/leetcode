# Single Number III

Tags: Bit Manipulation

## Problem

Given an array of numbers `nums`, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

For example:

Given `nums = [1, 2, 1, 3, 2, 5]`, return `[3, 5]`.

**Note**:

1. The order of the result is not important. So in the above example, `[5, 3]` is also correct.
2. Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?

**Credits:**
Special thanks to [@jianchao.li.fighter](https://leetcode.com/discuss/user/jianchao.li.fighter) for adding this problem and creating all test cases.

## Solution

由于这次有两个数要输出，依次异或最终得到的结果是 a ^ b，但也就到此为止了，尽管 `a ^ b ^ a =b` 有这样的性质，但这就是一个死循环，你不知道 a，就没法求 b，你不知道 b， 就没法求 a。。。

要进行分组，把 a 分到一组，b 分到另一组。由于 a 与 b 肯定不同，所以 `a ^ b != 0` 那么也就意味着 a 与 b 一定有不相同的位，这里非常巧妙的用了减法找到了最后一个不相同位 `lastDiffBit = aXORb - (aXORb & (aXORb - 1));` -1 操作非常精髓，直接把最后一个不同位标记出来了。

找到了这一位之后，剩下就是每次判断 i & lastDiffBit 的结果（因为 lastDiffBit 只有一位为1，其余位为 0）分别异或到不同结果里去。

```cpp
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        vector<int> ans;
        if (nums.empty()) return ans;
        
        int aXORb = 0;
        for (int& i : nums) aXORb ^= i;
        
        int lastDiffBit = aXORb - (aXORb & (aXORb - 1));
        
        int a = 0, b = 0;
        for (int&i : nums) {
            if (i & lastDiffBit) a ^= i;
            else b ^= i;
        }
        
        ans.push_back(a);
        ans.push_back(b);
        return ans;
    }
};
```

### 另一种找最后不同位的解法

最后不同位，也就是最后1的位置

`x &= (-x);` -x 操作 体现在位层面就是 取反 + 1，直接导致最后的 1 由于进位发生了变化。比上面的解法显得更加简洁

```cpp
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        vector<int> result(2, 0);
        if (nums.size() < 2) {
            return result;
        }
        int x = nums[0];
        for (int i = 1; i < nums.size(); ++i) {
            x ^= nums[i];    
        }
        x &= (-x);
        for (auto i : nums) {
            if (i & x) {
                result[0] ^= i;
            } else {
                result[1] ^= i;
            }
        }
        return result;
    }
};
```





### 复杂度

两次遍历数组，时间复杂度 $$O(n)$$

使用了部分额外空间，空间复杂度 $$O(1)$$.