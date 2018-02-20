# Divide Two Integers

Tags: Math, Binary Search

## Problem

Divide two integers without using multiplication, division and mod operator.

If it is overflow, return MAX_INT.

## Solution

本题的边界条件比较坑，做的时候坑了很长时间。

本题有两个 corner case：

1. 除数不可为 0 
2. 首先由于 INT_MIN 取负后 int 是无法表示的，所以 INT_MIN / -1 也会溢出，这点一定要提前考虑好

另外就是不断将 除数 移位来乘以 2，知道大于 被除数，此时可以得到 将divisor乘以了多少倍

之后再把商减去，被除数更新为余数，接着循环。

具体实现时，可以直接用 long long multiple 来记录商，但是一定一定要小心，这里的 multiple 也必须用 long long，否则`2147483647 1` 的例子过不去会溢出，是因为我这里的 multiple 在循环完成后会比实际大 1 倍，后续还要回退一格才行。要么就改变循环的判读条件。

也可以用 int cont 来记录移位的次数，内层循环做完一轮后统一移位。

```cpp
class Solution {
public:
    int divide(int dividend, int divisor) {
        if (divisor == 0 || dividend == INT_MIN && divisor == -1) return INT_MAX;
        if (dividend == 0) return 0;
      
        long long divid = dividend;
        long long divis = divisor;
        bool sign = (dividend < 0) ^ (divisor < 0);
        if (divid < 0) divid = -divid;
        if (divis < 0) divis = -divis;
        
        int ans = 0;
        
        while (divid >= divis) {
            long long tmp = divis;
            long long multiple = 1;
            while (divid >= tmp) {
                tmp <<= 1;
                multiple <<= 1;
            }
            ans += multiple >> 1;
            divid -= tmp >> 1;
        }
        
        return sign ? -ans : ans;
    }
};
```

另一种写法，改变循环判断的条件，就可以用 int 了

```cpp
class Solution {
public:
    int divide(int dividend, int divisor) {
        if (!divisor || (dividend == INT_MIN && divisor == -1))
            return INT_MAX;
        int sign = ((dividend < 0) ^ (divisor < 0)) ? -1 : 1;
        long long dvd = labs(dividend);
        long long dvs = labs(divisor);
        int res = 0;
        while (dvd >= dvs) { 
            long long temp = dvs;
            int multiple = 1;
            while (dvd >= (temp << 1)) {
                temp <<= 1;
                multiple <<= 1;
            }
            dvd -= temp;
            res += multiple;
        }
        return sign == 1 ? res : -res; 
    }
};
```

另一种写法，改用 count

```cpp
class Solution {
public:
    int divide(int dividend, int divisor) {
        if (divisor == 0 || dividend == INT_MIN && divisor == -1) return INT_MAX;
        long long divid = dividend;
        long long divis = divisor;
        bool sign = (dividend < 0) ^ (divisor < 0);
        if (divid < 0) divid = -divid;
        if (divis < 0) divis = -divis;
        
        int ans = 0;
        
        while (divid >= divis) {
            long long tmp = divis;
            int count = 0;
            while (divid >= tmp) {
                tmp <<= 1;
                ++count;
            }
            ans += 1 << (count - 1) ;
            divid -= tmp >> 1;
        }
        
        return sign ? -ans : ans;
    }
};
```

## 拓展

[参考](https://www.nowcoder.com/questionTerminal/472c6e8772bd4f1299fbe4a60f3b54f7) 给出的不使用“+、-、*、%”来实现加减乘除

```cpp
//链接：https://www.nowcoder.com/questionTerminal/472c6e8772bd4f1299fbe4a60f3b54f7
//来源：牛客网

public int add(int a, int b) {
        int sum = a;
        int arr = b;// 表示进位
        while (arr != 0) {
            sum = a ^ b;
            arr = (a & b) << 1;
            a = sum;
            b = arr;
        }
        return sum;
    }
 
    public int minus(int a, int b) {
        // b为减数,先变成负数
        b = add(~b, 1);
        return add(a, b);
    }
 
    public int multi(int a, int b) {
        // b为乘数
        for (int i = 0; i < b - 1; i++) {
            a = add(a, a);
        }
        return a;
    }
 
    public int divide(int dividend, int divisor) {
        // a被除树，b为除数
        int a = dividend;
        int b = divisor;
        if(a==1)
            return 0;
        if(b==0)
            try {
                throw new Exception("被除数不能为0！");
            } catch (Exception e) {
                e.printStackTrace();
                return 0;
            }
        int result = 0;
        if (a > 0 && b > 0) {
            while (a > 0) {
                a = minus(a, b);
                if (a >= 0)
                    result++;
            }
            return result;
        } else if (a < 0 && b > 0) {
            a = add(~a, 1);
            return add(~divide(a, b), 1);// 结果变为负号
        } else if (a > 0 && b < 0) {
            b = add(~b, 1);
            return add(~divide(a, b), 1);
        } else if (a < 0 && b < 0) {
            a = add(~a, 1);
            b = add(~b, 1);
            return divide(a, b);
        }
        return result;
    }
```

