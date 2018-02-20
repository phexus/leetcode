# Max Points on a Line

Tags: Hash Table, Math

## Problem

Given *n* points on a 2D plane, find the maximum number of points that lie on the same straight line.

## Solution

感觉本题的主要难点在于处理各种边界问题。

如果既要算斜率又算截取来确定的话，时间复杂度高达 $$O(n^3)$$

下面的解法用 hash 表来记录当前点出现过的斜率的个数，因为固定了该点，所以斜率相同的点一定在同一条直线上。

需要注意的几点：

1. j 可以从 i + 1开始，因为若是之前也有相同的斜率，那么在这之前肯定已经遍历过了，不影响最终结果。

2. 要考虑特殊情况，斜率不存在的点，重合的点，都会影响这一轮的结果。所以需要在这一轮比较后取斜率最多的数目再加上重复的点来更新最终结果；斜率不存在的点在循环内部更新就行。

3. 下面的做法选用了 long double，而不是 double。是因为浮点数精度的问题，这个例子`[[0,0],[94911151,94911150],[94911152,94911151]]` 就会输出错误结果 3，原因是 double 精度不够，使得 `94911150/94911151 == 94911151/94911152` 具体[参考评论](https://leetcode.com/problems/max-points-on-a-line/discuss/47102/20-line-C++-O(n2)-Hashing-Solution) 

   You’re comparing x/(x+1) with (x-1)/x. The difference is:

   $$x/(x+1) - (x-1)/x$$
   $$= (x^2 - (x-1)(x+1)) / ((x+1)x)$$
   $$≈ 1 / x^2.$$

   double 有效位 52 位，上面的结果对 x 进行了平方，当 x 超过 26 位 二级制位后，就超出了 double 的表示范围。

   为了解决这个问题，用了 long double，但感觉这并不是一个好方案。不同硬件对 long double 支持不一样，我的 64 位机器就是 16 字节的，但是好像 32 位下 long double 和 double 都是 8 字节。

### long double

```cpp
/**
 * Definition for a point.
 * struct Point {
 *     int x;
 *     int y;
 *     Point() : x(0), y(0) {}
 *     Point(int a, int b) : x(a), y(b) {}
 * };
 */
class Solution {
public:
    int maxPoints(vector<Point>& points) {
        if (points.empty()) return 0;
        int ans = 1;
        unordered_map<long double, int> map;
        
        for (int i = 0; i < points.size(); ++i) {
            map.clear();
            int repeat = 1;
            int vertical = 1;
            int maxSlope = 0;
            
            for (int j = i + 1; j < points.size(); ++j) {
                if (points[i].x == points[j].x && points[i].y == points[j].y) {
                    ++repeat;
                    
                } else if (points[i].x == points[j].x) {
                    ++vertical;
                    ans = max(ans, vertical);
                    
                } else {
                    long double slope = getSlope(points[i], points[j]);
                    ++map[slope];
                    maxSlope = max(maxSlope, map[slope]);
                }
            }
            ans = max(ans, maxSlope + repeat);
        }
        return ans;
    }
    
    long double getSlope(Point& p, Point& q) {
        long double slope = (long double) (p.y - q.y) / (p.x - q.x); 
        return slope;
    }
};
```

### 以分数形式来存

generateGcd 是用来算最大公约数的，这样 delt_x / delt_y 就能约分至唯一的表达方式，而且也不用单独起处理斜率不存在的情况了。

 delt_x / delt_y 可以用这样的字符串作为 key，也可以用 pair。

string 形式大概在 18 ms，pair 能到 10 ms，更快。

#### string

```cpp
/**
 * Definition for a point.
 * struct Point {
 *     int x;
 *     int y;
 *     Point() : x(0), y(0) {}
 *     Point(int a, int b) : x(a), y(b) {}
 * };
 */
class Solution {
public:
    int maxPoints(vector<Point>& points) {
        if (points.empty()) return 0;
        int ans = 1;
        unordered_map<string, int> map;
        
        for (int i = 0; i < points.size(); ++i) {
            map.clear();
            int repeat = 1;
            int maxSlope = 0;
            
            for (int j = i + 1; j < points.size(); ++j) {
                int delt_x = points[i].x - points[j].x;
                int delt_y = points[i].y - points[j].y;
                
                if (delt_x == 0 && delt_y == 0) {
                    ++repeat;
                    
                } else {
                    int gcd = generateGcd(delt_x, delt_y);
                    delt_x /= gcd;
                    delt_y /= gcd;
                    string slope = to_string(delt_x) + "/" + to_string(delt_y) ;
                    ++map[slope];
                    maxSlope = max(maxSlope, map[slope]);
                }
            }
            ans = max(ans, maxSlope + repeat);
        }
        return ans;
    }
    
    int generateGcd(int x, int y) {
        if (y == 0) return x;
        return generateGcd(y, x % y);
    }
};
```

#### pair

用 pair 作 key 的话，由于 pair 没有默认的 hash 函数，所以得自己传一个进去，比较麻烦，改用 map了。

> 注意map和unordered_map之间的区别，前者key值需要的是比较函数，后者需要hash函数

```cpp
/**
 * Definition for a point.
 * struct Point {
 *     int x;
 *     int y;
 *     Point() : x(0), y(0) {}
 *     Point(int a, int b) : x(a), y(b) {}
 * };
 */
class Solution {
public:
    int maxPoints(vector<Point>& points) {
        if (points.empty()) return 0;
        int ans = 1;
        map<pair<int, int>, int> map;
        
        for (int i = 0; i < points.size(); ++i) {
            map.clear();
            int repeat = 1;
            int maxSlope = 0;
            
            for (int j = i + 1; j < points.size(); ++j) {
                int delt_x = points[i].x - points[j].x;
                int delt_y = points[i].y - points[j].y;
                
                if (delt_x == 0 && delt_y == 0) {
                    ++repeat;
                    
                } else {
                    int gcd = generateGcd(delt_x, delt_y);
                    delt_x /= gcd;
                    delt_y /= gcd;
                    ++map[make_pair(delt_x, delt_y)];
                    maxSlope = max(maxSlope, map[make_pair(delt_x, delt_y)]);
                }
            }
            ans = max(ans, maxSlope + repeat);
        }
        return ans;
    }
    
    int generateGcd(int x, int y) {
        if (y == 0) return x;
        return generateGcd(y, x % y);
    }
};
```

