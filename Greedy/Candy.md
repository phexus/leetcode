# Candy

Tags: Greedy, Hard

## Problem

There are *N* children standing in a line. Each child is assigned a rating value.

You are giving candies to these children subjected to the following requirements:

- Each child must have at least one candy.
- Children with a higher rating get more candies than their neighbors.

What is the minimum candies you must give?

## Solution 1 

一开始能想到的方法是找局部最小点然后不断左右延伸，但是具体实现起来又不止如何下手，参考了[解答](https://www.nowcoder.com/questionTerminal/74a62e876ec341de8ab5c8662e866aef)

需要明确的是所有的局部最小点都应该是 1 ，然后向两边延伸不断 + 1

有以下几点需要注意：

1. 记录局部最小点的时候的判断条件应当包含第一个点和最后一个点的判断，判断大小要 >=, <= 因为有可能有连续几个点都相同，那么它们也应该都算是局部最小点，都应该赋值 1 
2. 还要记录局部最大点，因为连续两个局部最小点在延伸的时候会小相交，这个时候这个点就是局部最大点了，它的值应该等于两边延伸过来的较大的那个。而在实现的时候这个值会被两次覆盖，所以下面的加法就取了局部最大点两边的较大的点 +１

```cpp
//链接：https://www.nowcoder.com/questionTerminal/74a62e876ec341de8ab5c8662e866aef
//来源：牛客网
class Solution {
public:
    int candy(vector<int> &ratings) {
        // 1 3 5 2 7
        vector<int> a = ratings;
        vector<int> top;
        vector<int> low;
 
        int size = a.size();
        vector<int> candy(size);
        for( int i=0 ; i<size ; i++ ) {
            candy[i] = 1;
        }
 
        for( int i=0 ; i<size ; i++ ) {
            if( (i==0 || (i!=0 && a[i-1]<a[i])) &&
                (i==size-1 || (i+1!=size && a[i]>a[i+1])) ) {    // 找到所有的局部最高点
                top.push_back( i );
            }
            if( (i==0 || (i!=0 && a[i-1]>=a[i])) &&
                (i==size-1 || (i+1!=size && a[i]<=a[i+1])) ) {// 找到所有的局部最低点，注意等号
                low.push_back( i );
            }
        }
 
        // 遍历每个局部最低点
        for( int i=0 ; i<low.size() ; i++ ) {    // 每个最低点向两边延伸，遇到大的就+1
            int k;
            // 向左边延伸
            k = low[i] -1;
            // tips : 处理边界问题的时候，只判断要处理的最靠近边界的下标 是否越界就行了.
            while( k>=0 && a[k]>a[k+1] ) {
                candy[k] = candy[k+1] +1;
                k--;
            }
            // 向右边延伸
            k = low[i] +1;
            while( k<size && a[k]>a[k-1] ) {
                candy[k] = candy[k-1] +1;
                k++;
            }  
        }
 
        // 遍历每个局部最高点
        for( int k=0 ; k<top.size() ; k++ ) {
            int i = top[k];
 
            // 局部最高点的值等于 他的左右两边的值中较大值 +1
            int t1 = 0, t2 = 0;
            if( i>0 ) t1 = candy[i-1];
            if( i<size-1 ) t2 = candy[i+1];　// 注意好两边的边界问题
            candy[i] = (t1 > t2 ? t1 : t2) +1;
        }
 
        // 统计candy
        int ans = 0;
        for( int i=0 ; i<size ; i++ ) {
            ans += candy[i];
        }
 
        return ans;
    }
};
```

### 复杂度

虽然多次遍历，但是时间复杂度依然是$$O(n)$$，空间复杂度 $$O(n)$$

## Solution 2 

根据上面的解法进行进一步优化，其实只要从左往右扫一次，再从右往左扫一次就可以了。

1. 先将每个人都置位1，因为最少每个人糖果为 1
2. 先从左往右扫，只要前一个元素比当前小，当前元素的值就为上一个元素的值 + 1，这样就保证了右边高级别的孩子一定比左边的孩子糖果数量多
3. 再从右往左扫，这里有点区别，因为有可能在上一个环节之后，左边的糖果已经比右边的糖果多了，所以只有右边的元素比当前小并且当前糖果比右边元素少（或相等）的时候才做处理

```cpp
class Solution {
public:
    int candy(vector<int>& ratings) {
        if (ratings.empty()) return 0;
        
        int n = ratings.size();
        vector<int> ans(n, 1);
        
        for (int i = 1; i < n; ++i) {
            if (ratings[i] > ratings[i - 1]) ans[i] = ans[i - 1] + 1;
        }
        
        for (int i = n - 2; i >=0; --i) {
            if (ratings[i] > ratings[i + 1] && ans[i] <= ans[i + 1]) ans[i] = ans[i + 1] + 1;
        }
        
        int sum = 0;
        for (int i : ans) sum += i;
        return sum;
    }
};
```

### 复杂度

同上

## Solution 3 - 

还可以做到 $$O(1)$$ 的空间复杂度，具体参考 [solution](https://leetcode.com/problems/candy/solution/)

之后再补充。