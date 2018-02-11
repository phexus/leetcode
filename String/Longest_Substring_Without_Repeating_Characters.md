# Longest Substring Without Repeating Characters

Tags: String, Medium

## Problem

Given a string, find the length of the longest substring without repeating characters.

Examples:

Given "abcabcbb", the answer is "abc", which the length is 3.

Given "bbbbb", the answer is "b", with the length of 1.

Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

## 解法 - 滑动窗口

本题若是采用暴力遍历比对的解法，时间复杂度高达 $$O(n^3)$$，肯定是不能用了的。

[leetcode solution](https://leetcode.com/problems/longest-substring-without-repeating-characters/solution/) 里提供的解法是滑动窗口：
`A sliding window is an abstract concept commonly used in array/string problems. A window is a range of elements in the array/string which usually defined by the start and end indices, i.e. [i, j)[i,j) (left-closed, right-open). A sliding window is a window "slides" its two boundaries to the certain direction. For example, if we slide [i, j)[i,j) to the right by 11 element, then it becomes [i+1, j+1)[i+1,j+1) (left-closed, right-open).`

上面提到的滑动窗口是左闭右开的，但是本题为了方便，窗口做成左右都闭合的。具体实现也有多种思路：

###　unordered_set

此法的时间复杂度为 $$O(2n)$$，最差情况下 每个元素要被访问 2 次（i，j各访问一次）

这里需要注意的是 `ans = max(ans, j - i)`， 因为 j 已经在上一步自增了。

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        unordered_set<char> set;
        int ans = 0, i = 0, j = 0;
        
        while (i < n && j < n) {
            if (set.count(s[j]) > 0) {
                set.erase(s[i++]);
            } else {
                set.insert(s[j++]);
                ans = max(ans, j - i);
            }
        }
        
        return ans;
    }
};
```

### unordered_map

用 unordered_map 来存每个字符所对应的下标，出现重复元素后更新下标位置，也就是说 map 存的是遍历到当前为止，每个字符的最新下标。
这样以来，窗口的左边界就没必要一步一步增加了，可以直接增加到重复元素的先前位置 + 1 处。
看似比较简单，然而实现起来真是一把心酸泪，各种 bug。

1. 当有重复元素时，不能不管不顾的直接更新为 `i = map[s[j]] + 1;`，因为有可能 `map[s[j]] + 1` 比 `i` 本身还要小，之所以滑动窗口，都是往右滑动的，往左滑就搞笑了；

```cpp
            if (map.count(s[j]) > 0) {
                i = max(i, map[s[j]] + 1);
            }
```

2. ++ 运算符的问题。这点真是坑到家了，再次证明了 C++ 这门语言的灵活性，很多结果并不是想当然的，而是取决于编译器怎么理解。。下面的写法在 leetcode 和 nowcoder 上的解释完全不同。

```cpp
            map[s[j]] = j++;
            ans = max(ans, j - i);
```

上面面的语句本来我的意思是 `map[s[j]] = j; ++j; ans = max(ans, j - i);` leetcode 的编译器也是这么理解的。但是但是，nowcoder 会把上面的语句理解成 `int tmp = j; map[s[++j] = tmp; ans = max(ans, j - i);` 也就是说 = 右边先执行了，导致 s[j] 变成了 s[++j], 但赋值时又赋值的是原来的值。包括下面的例子 输出的结果也是 0 0 2 3 4，而不是 0 1 2 3 4
（实际测试，我的机器上的 gcc 7.2 和 nowcode 的 clang 都输出了和期望值不同的结果， leetcode 上的是 gcc 6.3）

```cpp
int main() {
    int a[] = {0,1,2,3,4};
    int i = 0;
    a[i] = i++;
    cout << a[0] << a[1] << a[2] << a[3] << a[4] << endl;
}
```

以后一定要警惕这种写法，一行语句中 自增自减 和 对该变量的访问必要出现在一起！！！大不了拆开写，多写两行。

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        unordered_map<char, int> map;
        int ans = 0, i = 0, j = 0;
        
        while (j < n) {
            if (map.count(s[j]) > 0) {
                i = max(i, map[s[j]] + 1);
            }
            
            map[s[j]] = j;
            ++j;
            ans = max(ans, j - i);
        }
        
        return ans;
    }
};
```

觉得 这种 while 写法不够优雅的话，完全可以用 for 来写，不过更新 ans 值是就得 + 1 了

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        unordered_map<char, int> map;
        int ans = 0;
        
        for (int j = 0, i = 0; j < n; ++j) {
            if (map.count(s[j]) > 0) {
                i = max(i, map[s[j]] + 1);
            }
            
            map[s[j]] = j;
            ans = max(ans, j - i + 1);
        }
        
        return ans;
    }
};
```

###　用数组代替　map

```
If we know that the charset is rather small, we can replace the Map with an integer array as direct access table.

Commonly used tables are:

int[26] for Letters 'a' - 'z' or 'A' - 'Z'
int[128] for ASCII
int[256] for Extended ASCII
```

假设字符串是由 ASCII 字符组成的，那么我们就可以用一个 128 维的一维数组来代替复杂的 map 结构，不过这里还是有坑。。

错误解法!!
下面的解法是有错的，因为要访问数组，所以在数组创建时就应该完成初始化操作，这里初始化为 0 (数组初始化列表中的元素个数小于指定的数组长度时，不足的元素补以默认值。容易出现的[误区](http://blog.csdn.net/u014417133/article/details/77185009)、[误区](https://www.zhihu.com/question/30438980?sort=created),所以初始化 0, 比较方便，对于基本类型int来说，默认值当然就是补int()即０了；如果要初始化为其他值还得循环。。)
初始化为０后，更新 i 时，就不用判断了，但是下面的写法会漏掉一些 corner case ，例如字符串只有 1 个元素 "c"，输出的结果是 0

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        int index[128] = {0};
        int ans = 0;
        
        for (int j = 0, i = 0; j < n; ++j) {
            i = max(i, index[s[j]] + 1);
            ans = max(ans, j - i + 1);
            index[s[j]] = j;
        }
        
        return ans;
    }
};
```

改写后的正确解法， `index[s[j]] = j + 1;` 即 index 存的是 遍历到目前为止 最新元素的下一个位置，也就意味着 i 还是左边界，不过把 +1 放在了更新 index 那一步。当不存在重复元素时，i 是不会变的，index[s[j]] 初始值为 0，不可能大于 i 的值的，只有当有重复元素时 i 才会更新。

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        int index[128] = {0};
        int ans = 0;
        
        for (int j = 0, i = 0; j < n; ++j) {
            i = max(i, index[s[j]]);
            ans = max(ans, j - i + 1);
            index[s[j]] = j + 1;
        }
        
        return ans;
    }
};
```

###　复杂度

以上时间复杂度均为 $$O(n)$$
空间复杂度 $$O(min(m, n))$$. Same as the previous approach. We need $$O(k)$$ space for the sliding window, where $$k$$ is the size of the Set. The size of the Set/Map is upper bounded by the size of the string $$n$$ and the size of the charset/alphabet $$m$$