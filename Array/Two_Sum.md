# Two Sum

Tags: Array, Hash Table, Easy

## Question

- leetcode: [Two Sum](https://leetcode.com/problems/two-sum/)
- lintcode: [Two Sum](http://www.lintcode.com/en/problem/two-sum/)
- nowcoder: [Two Sum](https://www.nowcoder.com/practice/20ef0972485e41019e39543e8e895b7f?tpId=46&tqId=29177&tPage=1&rp=1&ru=/ta/leetcode&qru=/ta/leetcode/question-ranking)

### Problem Statement

Given an array of integers, return **indices** of the two numbers such that
they add up to a specific target.

You may assume that each input would have **_exactly_** one solution, and you
may not use the _same_ element twice.

**Example:**  

    
    Given nums = [2, 7, 11, 15], target = 9,
    
    Because nums[**0**] + nums[**1**] = 2 + 7 = 9,
    return [**0**, **1**].

## 题解1 - 排序使用头尾指针遍历

### 我的解法

由于头、尾指针指向的是排过序的新数组，所以还需要用 find 去原来数组中查找原来的 index。另外特别注意的是，最后一定要处理相同元素，直接 find 会找到第一个符合条件的元素，但是题目不允许两次使用同一个元素，因此要减少查找范围，重新查找一次。（牛客上还要求返回大小顺序，还得 swap（））
这种解法最后的 find（） 处理太过麻烦，不推荐！

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int> &numbers, int target) {
        vector<int> result(2);
        vector<int> numbersSorted(numbers);
        sort(numbersSorted.begin(), numbersSorted.end());

        int idAhead = 0;
        int idBehind = numbersSorted.size() - 1;

        while (idAhead < idBehind) {
            int sum = numbersSorted[idAhead] + numbersSorted[idBehind];
            if (sum == target) {
                result[0] = idAhead;
                result[1] = idBehind;
                break;
            } else if (sum < target) {
                ++ idAhead;
            } else if (sum > target) {
                -- idBehind;
            }
        }

        result[0] = (find(numbers.begin(), numbers.end(), numbersSorted[result[0]])) - numbers.begin();
        result[1] = (find(numbers.begin(), numbers.end(), numbersSorted[result[1]])) - numbers.begin();
        if (result[1] == result[0]) {
            result[1] = (find(numbers.begin() + result[1] + 1, numbers.end(), numbers[result[1]])) - numbers.begin();
        }
        return result;
    }
};
```

### 推荐解法

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int> &numbers, int target) {
        const int length = numbers.size();
        vector<int> result;
        if (0 == numbers.size()) 
            return result;
        
        // first value, second index
        vector<pair<int, int>> numbersSortedPair(length);
        for (int i = 0; i < length; ++ i) {
            numbersSortedPair[i].first = numbers[i];
            numbersSortedPair[i].second = i;
        }
        
        sort(numbersSortedPair.begin(), numbersSortedPair.end());

        int idAhead = 0;
        int idBehind = length - 1;

        while (idAhead < idBehind) {
            int sum = numbersSortedPair[idAhead].first + numbersSortedPair[idBehind].first;
            if (sum == target) {
                result.push_back(numbersSortedPair[idAhead].second);
                result.push_back(numbersSortedPair[idBehind].second);
                
                break;
                    
            } else if (sum < target) {
                ++ idAhead;
            } else if (sum > target) {
                -- idBehind;
            }
        }

        return result;
    }
};
```

### 源码分析

1. 注意异常处理
2. 使用length保存数组的长度，避免反复调用nums.size()造成性能损失。
3. 使用pair组合排序前的值和索引，避免排序后找不到原有索引信息。注意这种 pair 和 vector 结合的用法！
4. 使用标准库函数 sort() 排序。
5. 两根指针指头尾，逐步靠拢，由于排序后的数字，所以不用单独处理相同元素的情况。

### 复杂度分析

与 sort 时间复杂度相同，是 $$O(nlog(n))$$, 空间复杂度为 $$O(1)$$.

## 题解2 - 哈希表

暴力求解，遍历 2 次，时间复杂度显然为 $$O(n^2)$$, 显然不符题目要求。两数之和为`target`所对应的判断条件—— $$x_i + x_j = target$$, 可进一步转化为 $$x_i = target - x_j$$, 其中 $$i$$ 和 $$j$$ 为数组中的下标。一段神奇的数学推理就将找两数之和转化为了找一个数是否在数组中了！
优化了时间复杂度，就要牺牲空间复杂度啦。在这里用什么呢？stack？queue？vector？还是hash_map?
对于stack和queue，除了pop外，查找的时间复杂度也是 $$O(n)$$。明显不是我们所需要的，那么什么数据结构的查找时间复杂度小呢？很显然是 hash_map, 查找的时间复杂度理想情况下是 $$O(1)$$。
我们需要额外的空间(也就是哈希表)来保存已经处理过的 $$x_j$$(**注意这里有两种思路，若是先初始化哈希表，就无法排除两个相同的元素相加为 target 的情况，得单独判断处理，这里采用先不初始化的思路，遍历过程中去添加哈希表的元素**), 如果不满足等式条件，那么我们就往后遍历，并把之前的元素加入到哈希表中（这样保证了下次遇到的元素肯定是新元素），如果`target`减去当前索引后的值在哈希表中找到了，那么就将哈希表中相应的索引返回，大功告成！

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        const int length = nums.size();
        vector<int> result;
        
        if (0 == length)
            return result;
        
        //value， index
        unordered_map<int, int> map(length);
        
        for (int i = 0; i < length; ++ i) {
            int restVal = target - nums[i];
            
            if (map.find(restVal) != map.end()) {
                result.push_back(map[restVal]);
                result.push_back(i);
                break;
            } else {
                map[nums[i]] = i; 
            }
        }
        
        return result;
    }
};
```

### 源码分析

1. 异常处理。
2. 使用 C++ 11 中的哈希表实现`unordered_map`映射值和索引。Python 中的`dict`就是天然的哈希表。
3. 找到满足条件的解就返回，找不到就加入哈希表中。注意题中要求返回索引值的含义。

### 复杂度分析

哈希表用了和数组等长的空间，空间复杂度为 $$O(n)$$, 遍历一次数组，时间复杂度为 $$O(n)$$.