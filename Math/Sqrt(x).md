## Sqrt(x)

Tags: Math, Binary Search, Easy

## Problem

Implement `int sqrt(int x)`.

Compute and return the square root of *x*.

**x** is guaranteed to be a non-negative integer.

**Example 1:**

```
Input: 4
Output: 2

```

**Example 2:**

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since we want to return an integer, the decimal part will be truncated.
```

## Solution 1 - 二分搜索

这里的二分搜索是典型的需要返回 小于等于目标值的最大值(upper bound )，所以最后要返回 end。要特别小心边界问题，因为 mid * mid 的值会溢出，一旦溢出就会出现错误判断，所以保险起见，直接将 mid 设为 long long (注意，不是 long，long 有可能是 32 位，而 long long 至少 64 位)

1. 先要将 x < 2 的提前处理，这样区间就可以缩小到 [1, x/2] ，可由两个函数的斜率和前几个不满足的离散值推得。
2. mid 定义为 long long
3. 最后返回 end

如何确定二分搜索的边界？其实最关键的是最后的返回结果到底是哪个。先分析循环条件 `start <= end` ，最后两个指针肯定会相遇，不管是 start 增加碰到的，还是 end 减少碰到的，此时的 mid 就是这个数，如果该数还小，那么 start 会增加导致循环终止；如果该数变大了，那么 end 就会减少到前一个数。这样以来，可能会担心万一 两个指针是由 start 增加碰到的，那么 start 要是多加了，错过了目标值该如何？其实这个问题是不存在的，因为只有当类似于 1， 2， 3 这种情形 start 才会 跳到 3，但 2 其实就是上轮的 mid，已经判断过了，一定是小于目标值的。这样一来最后肯定是返回 end。

```cpp
class Solution {
public:
    int mySqrt(int x) {
        if (x < 2) return x;
        int start = 1, end = x / 2;
        
        while (start <= end) {
            long long mid = start + (end - start) / 2;
            if (mid * mid == x) return mid;
            if (mid * mid < x) start = mid + 1;
            else end = mid - 1;
        }
        
        return end;
    }
};
```

正所谓一千个人就能写出一千种二分搜索来，下面罗列了一下其他表达，个人感觉还是上面的好。

```cpp
class Solution {
public:
    int sqrt(int x) {
        if (0 == x) return 0;
        int left = 1, right = x, ans;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (mid <= x / mid) {
                left = mid + 1;
                ans = mid;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
};
```

### 复杂度

时间复杂度为 $$O(logn)$$

空间复杂度 $$O(1)$$

## Solution 2 - Newton

牛顿法 “牛顿迭代法快速寻找平方根”， 具体步骤如下：

求出根号a的近似值：首先随便猜一个近似值x，然后不断令x等于x和a/x的平均数，迭代个六七次后x的值就已经相当精确了。例如，我想求根号2等于多少。假如我猜测的结果为4，虽然错的离谱，但你可以看到使用牛顿迭代法后这个值很快就趋近于根号2了：

(       4  + 2/4        ) / 2 = 2.25

(     2.25 + 2/2.25     ) / 2 = 1.56944..

( 1.56944..+ 2/1.56944..) / 2 = 1.42189..

( 1.42189..+ 2/1.42189..) / 2 = 1.41423..

....
这种算法的原理很简单，我们仅仅是不断用(x,f(x))的切线来逼近方程 $$x^2-a=0$$ 的根。根号a实际上就是 $$x^2-a=0$$ 的一个正实根，这个函数的导数是2x。也就是说，函数上任一点(x,f(x))处的切线斜率是2x。那么，$$x-f(x)/(2x)$$ 就是一个比x更接近的近似值。代入 $$f(x)=x^2-a$$得到 $$x-(x^2-a)/(2x)$$，也就是 $$(x+a/x)/2$$。

由于本题只要求给出整数部分，循环的条件就更加简单了。

不可思议的简单！还有更加精彩的 跟卡马克相关的 [平方根倒数速算法](https://algorithm.yuanbin.me/zh-hans/binary_search/sqrt_x.html)

```cpp
class Solution {
public:
    int mySqrt(int x) {
        long long ans = x;
        while (ans * ans > x) {
            ans = (ans + x / ans) / 2;
        }
        return ans;
    }
};
```



