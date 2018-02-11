# Single Number II

Tags: Bit Manipulation

## Problem

Given an array of integers, every element appears *three* times except for one, which appears exactly once. Find that single one.

**Note:**
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

## Solution 1 - 逐位处理

一开始我把问题想简单了，想着上一道题是异或，那这一道题应该就是同或吧，然而太天真，完全不对。。

这种问题的本质其实是用一个数来记录每个 bit 出现的次数，上一道题是每出现 2 次就归零，这一道题是每出现 3 次就归零，所以上一道题用逐位异或（模 2 加法）就很完美。这道题要用一样的思路的话，就得有一种 模 3 加法。所以就想到了求和取 模 3 运算，每次获取某一位 bit 时要用移位运算符和 & 1 的操作。累积结果用的是 |= 运算，需要把对应位的结果左移回之前的位置。

或者换一种思路来理解，三个相同的数相加，不仅其和能被3整除，其二进制位上的每一位也能被3整除！

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (int i = 0; i < 32; ++i) {
            int bits = 0;
            for (int& num : nums) {
                bits += (num >> i) & 1;
            }
            ans |= bits % 3 << i;
        }
        return ans;
    }
};
```

上面的解法有点 hard code，C++ 没有规定 int 的长度，所以还是用 sizeof 来取吧。

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (int i = 0; i < 8 * sizeof(int); ++i) {
            int bits = 0;
            for (int& num : nums) {
                bits += (num >> i) & 1;
            }
            ans |= bits % 3 << i;
        }
        return ans;
    }
};
```

另外由于，单独那个数的每一位只能取 0 或 1，所以求余结果只能是 0， 1，也可以直接移动 1

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (int i = 0; i < 8 * sizeof(int); ++i) {
            int bits = 0;
            for (int& num : nums) {
                bits += (num >> i) & 1;
            }
            if (bits % 3) ans |= 1 << i;
        }
        return ans;
    }
};
```

### 复杂度

时间复杂度 $$O(sizeof(int) * n)$$ 因为 $$sizeof(int)$$ 是常数，所以，复杂度 $$O(n)$$

空间复杂度 $$O(1)$$ bits 来存中间累加结果，常数级别的空间复杂度。

## Solution 2 - 位运算

 非常灵性的做法，一个数的每一位只能表示 0,1，也就是说只能记录两种状态，所以显然是不够的，那么一个不够，咱就再来一个数。用 ones 来记录只出现过一次的bits（这个数的某一位为 1 ，就代表到目前为止这个位的数只出现过一次），用 twos 来记录只出现过两次的bits（这个数的某一位为1，就代表目前为止该数已经出现过 2 次了，为 0就代表没有出现过 2 次，可能 0 次，可能 1 次），ones&twos 实际上就记录了出现过 3 次的 bits，然后再把出现 3 次的位从 ones 和 twos 里剔除就好。

要先更新 twos，`ones & i` 就是加上本次遍历的数后，出现 2 次的位数，再用 |= 来叠加以前出现过的；

`ones ^= i` 就是此时只出现了一次的位数，用异或 把 00 和 11 都抛掉

`ones & twos` 就是出现了 3 次的位数

分别用 `ones &= ~threes` `twos &= ~threes;` 剔除掉出现了 3 次的位数。

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ones = 0;
        int twos = 0;
        
        for (int& i : nums) {
            twos |= ones & i;
            ones ^= i;
            int threes = ones & twos;
            ones &= ~threes;
            twos &= ~threes;
        }
        
        return ones;
    }
};
```

更精简的写法

更新 ones 的同时剔除上次的 twos 结果，因为，这次的 ones 结果不变的位是不会受影响的，而发生变化的位 要么是 0 -> 1，要么是 1 -> 0，0 -> 1 是比较关键的，剔除掉 twos 的也就意味着剔除掉了会变成 3 的位（只有此时 ones 为1，twos 为 1，才会剔除）

更新 twos 的同时剔除上次的 ones 结果也是一样的道理， 只有此时 twos 为 1，ones 也为 1，才会剔除，因为只有这样才以为出现了 3 次。

很巧妙，不过感觉上面的解法更加符合正向思维，这种解法只是一个 trick

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ones = 0;
        int twos = 0;
        
        for (int& i : nums) {
            ones = (ones ^ i) & ~twos;
            twos = (twos ^ i) & ~ones;
        }
        
        return ones;
    }
};
```

## Solution 3 - 数学规律 - 卡诺图

[参考解法](https://www.nowcoder.net/questionTerminal/1097ca585245418ea2efd0e8b4d9eb7a)

感觉比较麻烦，以后有空再看吧。



## 拓展 ***

[Single Number II - Leetcode Discuss](https://leetcode.com/discuss/857/constant-space-solution?show=2542) 中抛出了这么一道扩展题：

```
Given an array of integers, every element appears k times except for one. Find that single one which appears l times.

```

@ranmocy 给出了如下经典解：

We need a array `x[i]` with size `k` for saving the bits appears `i` times. For every input number a, generate the new counter by `x[j] = (x[j-1] & a) | (x[j] & ~a)`. Except `x[0] = (x[k - 1] & a) | (x[0] & ~a)`.

In the equation, the first part indicates the the carries from previous one. The second part indicates the bits not carried to next one.

Then the algorithms run in `O(kn)` and the extra space `O(k)`.

### Java 解法

```java
public class Solution {
    public int singleNumber(int[] A, int k, int l) {
        if (A == null) return 0;
        int t;
        int[] x = new int[k];
        x[0] = ~0;
        for (int i = 0; i < A.length; i++) {
            t = x[k-1];
            for (int j = k-1; j > 0; j--) {
                x[j] = (x[j-1] & A[i]) | (x[j] & ~A[i]);
            }
            x[0] = (t & A[i]) | (x[0] & ~A[i]);
        }
        return x[l];
    }
}
```

设计的很巧妙，当 k = 2就等退化成上面的解。

```java
public class Solution {
    public int singleNumber(int[] A) {
        if (A == null) return 0;
        int x0 = ~0, x1 = 0, x2 = 0, t;
        for (int i = 0; i < A.length; i++) {
            t = x2;
            x2 = (x1 & A[i]) | (x2 & ~A[i]);
            x1 = (x0 & A[i]) | (x1 & ~A[i]);
            x0 = (t & A[i]) | (x0 & ~A[i]);
        }
        return x1;
    }
}
```

`x[j] = (x[j-1] & a) | (x[j] & ~a)` 其实我理解的是左半部分是 j - 1 产生的新的结果，而有半部分只有 1 0 才满足要求，也就是原本的不变的结果，这两部分一定不是相互干扰的（某一位的个数只会出现在一个结果里）

注意，这里的 x[0] 要初始化为 ~0,这样保证了原来的记过就是A[i]， ~0 & A[i] = A[i]