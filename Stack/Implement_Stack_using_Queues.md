# Implement Stack using Queues

tags: Stack, Design, Easy

## Problem

Implement the following operations of a stack using queues.

- push(x) -- Push element x onto stack.
- pop() -- Removes the element on top of the stack.
- top() -- Get the top element.
- empty() -- Return whether the stack is empty.

Notes:

- You must use *only* standard operations of a queue -- which means only `push to back`, `peek/pop from front`, `size`, and `is empty` operations are valid.
- Depending on your language, queue may not be supported natively. You may simulate a queue by using a list or deque (double-ended queue), as long as you use only standard operations of a queue.
- You may assume that all operations are valid (for example, no pop or top operations will be called on an empty stack).

## Solution

### 错误实现

先看我的错误实现，想套用之前的 Implement Stack using Queues，结果发现 2 个 queue 倒来倒去，顺序是不变的。。这也就是为什么倒序要用 stack 而不用 queue 了。想要改正，参考 `剑指 offer` 的思路，从一个队列导向另一个队列的时候不要导完，留最后一个元素删除，所以就要判读 size 了。

```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        !q1.empty() ? q1.push(x) : q2.push(x);
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int ans;
        if (q2.empty()) {
            while (!q1.empty()) {
                q2.push(q1.front());
                q1.pop();
            }
            ans = q2.front();
            q2.pop();
        } else {
            while (!q2.empty()) {
                q1.push(q2.front());
                q2.pop();
            }
            ans = q1.front();
            q1.pop();
        }
        
        return ans;
    }
    
    /** Get the top element. */
    int top() {
        int ans;
        if (q2.empty()) {
            while (!q1.empty()) {
                q2.push(q1.front());
                q1.pop();
            }
            ans = q2.front();
        } else {
            while (!q2.empty()) {
                q1.push(q2.front());
                q2.pop();
            }
            ans = q1.front();
        }
        
        return ans;
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q1.empty() && q2.empty();
    }
private:
    queue<int> q1;
    queue<int> q2;

};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * bool param_4 = obj.empty();
 */
```

### 修改后的实现

由于数据是在 q1 和 q2 之间震荡的，所以代码有很多重复的部分，但是也不好优化了。

```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        !q1.empty() ? q1.push(x) : q2.push(x);
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int ans;
        if (q2.empty()) {
            while (q1.size() > 1) {
                q2.push(q1.front());
                q1.pop();
            }
            ans = q1.front();
            q1.pop();
        } else {
            while (q2.size() > 1) {
                q1.push(q2.front());
                q2.pop();
            }
            ans = q2.front();
            q2.pop();
        }
        
        return ans;
    }
    
    /** Get the top element. */
    int top() {
        int ans;
        if (q2.empty()) {
            while (q1.size() > 1) {
                q2.push(q1.front());
                q1.pop();
            }
            ans = q1.front();
            q2.push(q1.front());
            q1.pop();
        } else {
            while (q2.size() > 1) {
                q1.push(q2.front());
                q2.pop();
            }
            ans = q2.front();
            q1.push(q2.front());
            q2.pop();
        }
        
        return ans;
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q1.empty() && q2.empty();
    }
private:
    queue<int> q1;
    queue<int> q2;

};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * bool param_4 = obj.empty();
 */
```

## Solution 2 

[leetcode solution](https://leetcode.com/problems/implement-stack-using-queues/solution/) 给出的方案是把数据固定在 q1，q2只是作为临时交换的队列，确保 q1 里存的就是 栈的顺序

push 里的 `swap(q1, q2);` 也可以自己替换成 `q1.swap(q2);`，还可以想下面这样自己写，但是感觉这种拷贝构造效率有点低

> 另外，注意 C++ 不允许函数名和成员函数同名，只能叫 front

```cpp
        queue<int> tmp;
        tmp = q2;
        q2 = q1;
        q1 = tmp;
```



```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        q2.push(x);
        while (!q1.empty()) {
            q2.push(q1.front());
            q1.pop();
        }
        
        swap(q1, q2);
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int ans = q1.front();
        q1.pop();
        return ans;
    }
    
    /** Get the top element. */
    int top() {
        return q1.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q1.empty();
    }
private:
    queue<int> q1;
    queue<int> q2;
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * bool param_4 = obj.empty();
 */
```

## Solution 3 - 1 queue

只用一个队列的方法，非常机智，for loop 控制好循环次数。

```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        q1.push(x);
        for (int i = 0; i < q1.size() - 1; ++i) {
            q1.push(q1.front());
            q1.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int ans = q1.front();
        q1.pop();
        return ans;
    }
    
    /** Get the top element. */
    int top() {
        return q1.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q1.empty();
    }
private:
    queue<int> q1;
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * bool param_4 = obj.empty();
 */
```

### 复杂度

只有 push 的时间复杂度为 $$O(n)$$ ，$$2n + 1$$ 个操作， 其余为 $$O(1)$$ 

