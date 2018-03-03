# Implement Stack using Queues

tags: Stack, Design, Easy

## Problem

Implement the following operations of a queue using stacks.

- push(x) -- Push element x to the back of queue.
- pop() -- Removes the element from in front of queue.
- peek() -- Get the front element.
- empty() -- Return whether the queue is empty.

Notes:

- You must use *only* standard operations of a stack -- which means only `push to top`, `peek/pop from top`, `size`, and `is empty`operations are valid.
- Depending on your language, stack may not be supported natively. You may simulate a stack by using a list or deque (double-ended queue), as long as you use only standard operations of a stack.
- You may assume that all operations are valid (for example, no pop or peek operations will be called on an empty queue).

## Solution

本题与 `剑指 offer` 面试题 7 相同，需要对 Stack 和 queue 熟练掌握。

由于题目已经明确指出可以假设所有操作都是合法的，所以不用考虑当队列为空时的 pop 问题 。但实际上是要抛异常的。

注意这里的 front 用来表示队列头部，在 push 操作的时候赋值 `if (stack1.empty()) front = x;`

在 peak 操作的时候读取，` if (!stack2.empty()) return stack2.top();  return front;`，这样能够确保 peak 的时间复杂度也能控制在 $$O(1)$$

```cpp
class MyQueue {
public:
    /** Initialize your data structure here. */
    MyQueue() {
        
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        if (stack1.empty()) front = x;
        stack1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        if (stack2.empty()) {
            while (!stack1.empty()) {
                stack2.push(stack1.top());
                stack1.pop();
            }
        }
        int ans = stack2.top();
        stack2.pop();
        return ans;
    }
    
    /** Get the front element. */
    int peek() {
        if (!stack2.empty()) return stack2.top();
        return front;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return stack1.empty() && stack2.empty(); 
    }
    
private:
    stack<int> stack1;
    stack<int> stack2;
    int front;
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * bool param_4 = obj.empty();
 */
```

 ### 复杂度

push: $$O(1)$$

pop: Amortized $$O(1)$$, Worst-case $$O(n)$$. [leetcode solution](https://leetcode.com/problems/implement-queue-using-stacks/solution/) 对于 amortized 有着比较详细的解释

peak: $$O(1)$$

empty : $$O(1)$$

空间复杂度只有 push 是 $$O(n)$$, 其余 $$O(1)$$