# Gas Station

Tags: Greedy

## Problem

There are *N* gas stations along a circular route, where the amount of gas at station *i* is `gas[i]`.

You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from station *i* to its next station (*i*+1). You begin the journey with an empty tank at one of the gas stations.

Return the starting gas station's index if you can travel around the circuit once, otherwise return -1.

**Note:**
The solution is guaranteed to be unique.

## Solution - 贪心

感觉这道题实现很简单，但是想明白其中的道理比较难。

下面的解法有一个数学命题在背后支撑：

**对于一个循环数组，如果这个数组整体和 SUM >= 0，那么必然可以在这个数组中找到这么一个元素，使得从这个元素出发，绕数组一周，能保证累加和一直处于非负的状态。**

仔细想想，的确是这么回事，要保证累加和非负，那么这里欠的必然在这里以外的广阔范围内给补回来，正所谓此消彼长就是这么个道理。

那么这道题的两个数组生成的新数组 `gas[i] - cost[i]` 的累加和若能非负，那么一定就能找到一个起点。另外，由上面的推论还可以得知，一旦到达了第 i 个油站，如果此时 `remain + gas[i]  - cost[i]` 已经为负，那么起点一定不在第 i 个及之前遍历过的位置，因为从贪心的角度来讲，i 肯定不行了（因为到不了 i + 1），但是 i 之前的最差情况是没有结余，都无法完成，那么减少结点的累积数量就更不可能完成了。所以此时起点一定在 i 后面的位置。

sum 变量是一定要有的，是一个全局的标识位，不管最终 start 指向了哪里，只要sum < 0，就一定返回 -1

remain 变量要和 start 一起更新，因为遍历到了所有的位置，所以不用担心 start 最后越界，因为一旦越界，sum 也必定为负。

```cpp
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int start = 0;
        int remain = 0;
        int sum = 0;
        
        for (int i = 0; i < gas.size(); ++i) {
            sum += gas[i] - cost[i];
            remain += gas[i] - cost[i];
            if (remain < 0) {
                start = i + 1;
                remain = 0;
            }
        }
        
        return sum >= 0 ? start : -1;
    }
};
```

