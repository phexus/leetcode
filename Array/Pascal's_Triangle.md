# Pascal's Triangle

Tags: Array, Easy

## Question

- leetcode: [Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/description/)
- nowcoder: [Pascal's Triangle](https://www.nowcoder.com/practice/f3f8f0f058b347ba8245cc90d0049d92?tpId=46&tqId=29062&tPage=2&rp=1&ru=%2Fta%2Fleetcode&qru=%2Fta%2Fleetcode%2Fquestion-ranking)

### Problem Statement

Given numRows, generate the first numRows of Pascal's triangle.

For example, given numRows = 5,
Return

```
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

## 题解 - 数学规律，迭代遍历

帕斯卡三角的规律：

- 第k层有k个元素
- 每层第一个以及最后一个元素值为1
- 对于第k（k > 2）层第n（n > 1 && n < k）个元素A[k][n]，A[k][n] = A[k-1][n-1] + A[k-1][n]

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> triangle;
        
        triangle.resize(numRows);
        
        for (int row = 0; row < numRows; ++ row) {
            triangle[row].resize(row + 1);
            
            triangle[row][0] = 1;
            triangle[row][row] = 1;
            
            for (int col = 1; col < row; ++ col) {
                triangle[row][col] = triangle[row - 1][col - 1] + triangle[row - 1][col];
            }
        }
        
        return triangle;
    }
};
```

### 源码分析

1. vector 的 resize() 用法，`triangle.resize(numRows);` 其实也可以不写。

resize(n) 的用法：

- 如果n比当前的vector元素数目要小，vector的容量要缩减到resize的第一个参数大小，既n。并移除那些超出n的元素同时销毁他们。
- 如果n比当前vector元素数目要大，在vector的末尾扩展需要的元素数目，如果第二个参数val指定了，扩展的新元素初始化为val的副本，否则按类型默认初始化。
- 注意：如果n大于当前的vector的容量(是容量，并非vector的size)，将会引起自动内存分配。所以现有的pointer,references,iterators将会失效。 

```
与 reserver 的区别：
resize()，设置大小（size）;
reserve()，设置容量（capacity）;
size()是分配容器的内存大小，而capacity()只是设置容器容量大小，但并没有真正分配内存。
打个比方：正在建造的一辆公交车，车里面可以设置40个座椅（reserve(40);），这是它的容量，但并不是说它里面就有了40个座椅，只能说明这部车内部空间大小可以放得下40张座椅而已。而车里面安装了40个座椅(resize(40);)，这个时候车里面才真正有了40个座椅，这些座椅就可以使用了。

容器的capacity（容量）与size（长度）的区别：
size指容器当前拥有的元素个数；
而capacity则指容器在必须分配新存储空间之前可以存储的元素总数。也可以说是预分配存储空间的大小。

resize()函数和容器的size息息相关。调用resize(n)后，容器的size即为n。
至于是否影响capacity，取决于调整后的容器的size是否大于capacity。

reserve()函数和容器的capacity息息相关。
调用reserve(n)后，若容器的capacity<n，则重新分配内存空间，从而使得capacity等于n。
如果capacity>=n呢？capacity无变化。

从两个函数的用途可以发现，容器调用resize()函数后，所有的空间都已经初始化了，所以可以直接访问。
而reserve()函数预分配出的空间没有被初始化，所以不可访问。

```
### 复杂度分析

时间复杂度是 $$O(n^2)$$, 空间复杂度为 $$O(1)$$.