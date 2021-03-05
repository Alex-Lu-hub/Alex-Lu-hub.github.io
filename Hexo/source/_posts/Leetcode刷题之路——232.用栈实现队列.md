---
title: Leetcode刷题之路——232. 用栈实现队列
tags: [C++]
categories: [Leetcode]
date: 2021-3-5 14:48:00
updated: 2021-3-5 15:58:00
excerpt: Leetcode题库232. 用栈实现队列
---

# 题目描述

请你仅使用两个栈实现先入先出队列。队列应当支持一般队列的支持的所有操作（ ``push`` 、 ``pop`` 、 ``peek`` 、 ``empty`` ）：

实现 ``MyQueue`` 类：

*  ``void push(int x)`` 将元素 x 推到队列的末尾
*  ``int pop()`` 从队列的开头移除并返回元素
*  ``int peek()`` 返回队列开头的元素
*  ``bool empty()`` 如果队列为空，返回 ``true`` ；否则，返回 ``false`` 


说明：

* 你只能使用标准的栈操作 —— 也就是只有 ``push to top`` , ``peek/pop from top`` , ``size`` , 和 ``is empty`` 操作是合法的。
* 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。


进阶：

* 你能否实现每个操作均摊时间复杂度为 ``O(1)`` 的队列？换句话说，执行 ``n`` 个操作的总时间复杂度为 ``O(n)`` ，即使其中一个操作可能花费较长时间。

示例：

```
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```


提示：

*  ``1 <= x <= 9`` 
* 最多调用 ``100`` 次 ``push`` 、 ``pop`` 、 ``peek`` 和 ``empty`` 
* 假设所有操作都是有效的 （例如，一个空的队列不会调用 ``pop`` 或者 ``peek`` 操作）

# 题目分析

没啥好分析的，就是C++的栈 ``stack`` 还没有用过，需要学习一下。

具体代码如下：

```c++
class MyQueue {
private:
    stack <int> instack, outstack;

    void in2out() {
        while(!instack.empty()) {
            outstack.push(instack.top());
            instack.pop();
        }
    }

public:
    /** Initialize your data structure here. */
    MyQueue() {

    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        instack.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        if(outstack.empty()) {
            in2out();
        }
        int number = outstack.top();
        outstack.pop();
        return number;
    }
    
    /** Get the front element. */
    int peek() {
        if(outstack.empty()) {
            in2out();
        }
        int number = outstack.top();
        return number;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return instack.empty() && outstack.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```

# 一些细节的总结

* C++的STL还是需要都看一下，免得要用的时候不知道
* 当只有一个线程对栈进行读写操作的时候，总有一个栈是空的。在多线程应用中，如果我们只有一个队列，为了线程安全，我们在读或者写队列的时候都需要锁住整个队列。而在两个栈的实现中，只要写入栈不为空，那么push操作的锁就不会影响到pop。

