# Permutation Index

Tags: Lintcode, Math, Easy

## Problem

Given a permutation which contains no repeated number, find its index in all the permutations of these numbers, which are ordered in lexicographical order. The index begins at 1.

Example

Given [1,2,4], return 1.

## Solution

[参考1](https://www.cnblogs.com/kittyamin/p/5014947.html) [参考2](https://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutation_index.html)

这道题有点难理解，求数列在其全排列组合位于第几位。

思路参考别人的，并不需要求出所有的排列组合。

对于某一个给定的位数A[i],需要判断在它后面有几个小于它的数，记下这个数字和A[i]所在的位置。

比如对于一个四位数，5316  ， 第一位后面有2小于它的数，如果这两个数排在第一位，那么（1和3）各有3！的排列组合数小于（5316）.

同理，对于第二位，其后有1个小于它的数，如果它放在第二位，那么有2！种排列。

因此判断一个给定数位于排列组合的第几位，则可以按照以下公式进行

count1*(A.length-1)!+count2*(A.length-2)!+......+countn*(0)!

下面的 factor 每次更新的是下一轮，低位到高位进行计算，我们可通过两重循环得出到某个索引处值的相对大小，而且顺便一起把 factor 更新了。

注意 index 和 factor 的初始值，smallerCount 的值每次计算时都需要重新置零，index 先自增，factorial 后自乘求阶乘

```cpp
class Solution {
public:
    /*
     * @param A: An array of integers
     * @return: A long integer
     */
    long long permutationIndex(vector<int> &A) {
        // write your code here
        
        long long index = 1; // initial 1
        long long factor = 1;
        
        for (int i = A.size() - 1; i >= 0; --i) {
            int smallerCount = 0;
            for (int j = i + 1; j < A.size(); ++j) {
                if (A[j] < A[i]) ++smallerCount; // 统计后面有几个比该数大的
            }
            
            index += smallerCount * factor; // update index
            factor *= A.size() - i; // update next factor
        }
        
        return index;
    }
};
```

### 复杂度

双重 for 循环，时间复杂度为 $$O(n^2)$$. 使用了部分额外空间，空间复杂度 $$O(1)$$