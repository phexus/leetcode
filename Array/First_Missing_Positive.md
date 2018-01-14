# First Missing Positive

Tags: Array, Hard

## Question

- leetcode: [First Missing Positive](https://leetcode.com/problems/first-missing-positive/)
- lintcode: [First Missing Positive](http://www.lintcode.com/en/problem/first-missing-positive/)
- nowcoder: [First Missing Positive](https://www.nowcoder.com/practice/ea7ca311264d4c579a1ef6c1f7f69138?tpId=46&tqId=29138&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given an unsorted integer array, find the first missing positive integer.

For example,  
Given `[1,2,0]` return `3`,  
and `[3,4,-1,1]` return `2`.

Your algorithm should run in _O_(_n_) time and uses constant space.


## 题解

[参考题解](https://algorithm.yuanbin.me/zh-hans/integer_array/first_missing_positive.html)

由于题目限定了是非排序的数组，若是先排序做处理，排序算法中常用的*基于比较的*方法时间复杂度的理论下界为 $$O(n \log n)$$, 不符题目要求。常见的能达到线性时间复杂度的排序算法有 [基数排序](http://zh.wikipedia.org/wiki/%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F)，[计数排序](http://algorithm.yuanbin.me/zh-hans/basics_sorting/counting_sort.html) 和 [桶排序](http://algorithm.yuanbin.me/zh-hans/basics_sorting/bucket_sort.html)。

计数排序对元素落在一定区间且重复值较多的情况十分有效，且需要额外的 $$O(n)$$ 空间。桶排序通常需要按照一定规则将值放入桶中，一般需要额外的 $$O(n)$$ 空间，但若是能设定一定的规则原地交换原数组的值就可以避免额外的 $$O(n)$$ 空间消耗。

```
对给定数组使用桶排序的思想排序，第一个桶放1，第二个桶放2，如果找不到相应的数，则相应的桶的值不变(可能为负值，也可能为其他值)。

那么怎么才能做到原地排序呢？即若 $$A[i] = x$$, 则将 x 放到它该去的地方 - $$A[x - 1] = x$$, 同时将原来 $$A[x - 1]$$ 地方的值交换给 $$A[i]$$.

排好序后遍历桶，如果不满足 $$f[i] = i + 1$$, 那么警察叔叔就是它了！如果都满足条件怎么办？那就返回给定数组大小再加1呗。
```

我的理解：因为本题要求是寻找第一个缺失的正整数，所以完全可以把数组的下标和本身的值关联起来，位置 0 就应该存第一个正整数 1，想明白这一点之后，无非就 for 里面嵌套 while 来换到不能再换为止。


class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        const int length = nums.size();
        
        for (int i = 0; i < length; ++ i) {
            while (nums[i] > 0 && nums[i] <= length && nums[i] != nums[nums[i] - 1]) {
                int tmp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = tmp;
            }
        }
        
        for (int i = 0; i < length; ++ i) {
            if (nums[i] != (i + 1)) return i + 1;
        }
        
        return length + 1;
    }
};

```

### 源码分析

边界条件的处理需要重点关注，这里直接参考上文链接的解答：

1. `nums[i]` 为正数，负数和零都无法在桶中找到生存空间...
2. `nums[i] <= length` 当前索引处的值不能比原数组容量大，大了的话也没用啊，肯定不是缺的第一个正数。
3. `nums[i] != i + 1`, 已满足条件了无需交换。
4. `nums[i] != nums[nums[i] - 1]`, 避免欲交换的值和自身相同，否则有重复值时会产生死循环。

这里要注意的是 3,4 两点，我在做本题的时候就造成了死循环。其实上面 4 个条件可以进一步精简，因为 3 的情况可以涵盖在 4 中，当满足条件了，也就以为着要与自身交换，自身和自身肯定相同，所以条件 3 可以干掉。最终条件为：
1. `nums[i] > 0`
2. `nums[i] <= length`
3. `nums[i] != nums[nums[i] - 1]`

使用`while`循环处理，此时`i`并不自增，直到将所有满足条件的索引处理完。

注意交换的写法，若写成

```c
int temp = A[i];
A[i] = A[A[i] - 1];
A[A[i] - 1] = temp;
```

因为在第三行中`A[i]`已不再是之前的值，第二行赋值时已经改变，故源码中的写法比较安全。（其实偷懒的话，可以直接用 swap()来做）

最后的返回结果为 `length + 1`，因为到这一步返回也就意味着前面从 1 到 length 都是完整的，自然缺的就是 `length + 1` 了

### 复杂度分析

这里其实是有疑惑的？「桶排序」需要遍历一次原数组，考虑到`while`循环也需要一定次数的遍历，故时间复杂度至少为 $$O(n)$$. 最后求索引值最多遍历一次排序后数组，时间复杂度最高为 $$O(n)$$，所以最终复杂度感觉要大于$$O(n)$$ 啊？
用到了`temp`作为中间交换变量，空间复杂度为 $$O(1)$$.

###　Reference
[First Missing Positive](https://algorithm.yuanbin.me/zh-hans/integer_array/first_missing_positive.html)
