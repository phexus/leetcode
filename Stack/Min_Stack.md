# Min Stack

Tags: Stack, Design, Easy

## Problem

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

- push(x) -- Push element x onto stack.
- pop() -- Removes the element on top of the stack.
- top() -- Get the top element.
- getMin() -- Retrieve the minimum element in the stack.

**Example:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> Returns -3.
minStack.pop();
minStack.top();      --> Returns 0.
minStack.getMin();   --> Returns -2.
```

## Solution

总的来说，就是借助辅助栈，需要存储每次 push 新元素之后的最小值

pop 和 top 都应该做异常处理的，由于 leetcode 不好测试，所以就默认不会异常了。

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    MinStack() {
        
    }
    
    void push(int x) {
        dataSt.push(x);
        if (!minSt.empty()) {
            if (x < minSt.top()) minSt.push(x);
            else minSt.push(minSt.top());
        } else {
            minSt.push(x);
        }
    }
    
    void pop() {
        dataSt.pop();
        minSt.pop();
    }
    
    int top() {
        if (!dataSt.empty()) return dataSt.top();
        else return 0;
    }
    
    int getMin() {
        if (!minSt.empty()) return minSt.top();
        else return 0;
    }
    
private:
    stack<int> dataSt;
    stack<int> minSt;
    
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

上面的解法的  minSt 的大小和 dataSt 大小相同，比较清晰易懂，还可以参考这个 [解法](https://leetcode.com/problems/min-stack/discuss/49016/C++-using-two-stacks-quite-short-and-easy-to-understand) ，进一步减少 minSt 的大小，只有 <= 的时候才存储 x，只有 s1.top() == getMin() 的时候才 pop

```cpp
class MinStack {
private:
    stack<int> s1;
    stack<int> s2;
public:
    void push(int x) {
	    s1.push(x);
	    if (s2.empty() || x <= getMin())  s2.push(x);	    
    }
    void pop() {
	    if (s1.top() == getMin())  s2.pop();
	    s1.pop();
    }
    int top() {
	    return s1.top();
    }
    int getMin() {
	    return s2.top();
    }
};
```

