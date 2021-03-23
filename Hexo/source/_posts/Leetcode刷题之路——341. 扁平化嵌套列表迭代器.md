---
title: Leetcode刷题之路——341. 扁平化嵌套列表迭代器
tags: [C++]
categories: [Leetcode]
date: 2021-3-23 13:56:00
updated: 2021-3-23 14:58:00
excerpt: Leetcode题库341. 扁平化嵌套列表迭代器
---

# 题目描述

给你一个嵌套的整型列表。请你设计一个迭代器，使其能够遍历这个整型列表中的所有整数。

列表中的每一项或者为一个整数，或者是另一个列表。其中列表的元素也可能是整数或是其他列表。

 

示例 1:

```
输入: [[1,1],2,[1,1]]
输出: [1,1,2,1,1]
解释: 通过重复调用 next 直到 hasNext 返回 false，next 返回的元素的顺序应该是: [1,1,2,1,1]。
```


示例 2:

```
输入: [1,[4,[6]]]
输出: [1,4,6]
解释: 通过重复调用 next 直到 hasNext 返回 false，next 返回的元素的顺序应该是: [1,4,6]。 
```

# 题目分析

## 深度优先搜索递归

一个比较直接的想法，在初始化迭代器的时候就顺便把整个列表全部处理完成，形成一个符合顺序的整数列表。采用深度优先搜索的方法，碰到整数就送入列表中，碰到列表就进入列表进行同样的操作搜索。最终可以处理完成。那么最终这个迭代器就可以直接用整数列表的迭代器代替了。

具体代码如下：

```c++
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * class NestedInteger {
 *   public:
 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
 *     bool isInteger() const;
 *
 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
 *     // The result is undefined if this NestedInteger holds a nested list
 *     int getInteger() const;
 *
 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
 *     // The result is undefined if this NestedInteger holds a single integer
 *     const vector<NestedInteger> &getList() const;
 * };
 */

class NestedIterator {
private:
    vector<int> val;
    vector<int>::iterator cur;

    void dfs(const vector<NestedInteger> &nestedList) {
        for(auto info: nestedList) {
            if(info.isInteger()) {
                val.push_back(info.getInteger());
            }
            else {
                dfs(info.getList());
            }
        }
    }

public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        dfs(nestedList);
        cur = val.begin();
    }
    
    int next() {
        return *cur++;
    }
    
    bool hasNext() {
        return cur != val.end();
    }
};

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i(nestedList);
 * while (i.hasNext()) cout << i.next();
 */
```

## 使用栈来进行边遍历边展开

在初始化的时候，不全部展开，而是将元素都放入栈中。因为要按顺序遍历的迭代器，所以在放入栈时应该倒序放入。迭代器遍历时，碰到列表则展开，放入栈中，直到碰到了整数才停止展开和放入栈的操作。每从栈中取出一个要注意栈的pop和push。

具体的代码如下：

```c++
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * class NestedInteger {
 *   public:
 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
 *     bool isInteger() const;
 *
 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
 *     // The result is undefined if this NestedInteger holds a nested list
 *     int getInteger() const;
 *
 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
 *     // The result is undefined if this NestedInteger holds a single integer
 *     const vector<NestedInteger> &getList() const;
 * };
 */

class NestedIterator {
private:
    stack<NestedInteger> mystack;

public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        for(int i = nestedList.size() - 1; i >= 0; --i) {
            mystack.push(nestedList[i]);
        }
    }
    
    int next() {
        NestedInteger cur = mystack.top();
        mystack.pop();
        return cur.getInteger();
    }
    
    bool hasNext() {
        while(!mystack.empty()) {
            NestedInteger cur = mystack.top();
            if(cur.isInteger()) {
                return true;
            }
            mystack.pop();
            for(int i = cur.getList().size() - 1; i >=0; --i) {
                mystack.push(cur.getList()[i]);
            }
        }
        return false;
    }
};

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i(nestedList);
 * while (i.hasNext()) cout << i.next();
 */
```

# 一些细节的总结

*  ``++`` 、 ``--`` 的使用还有 ``bool`` 类型的返回内容有很多可以简化的写法，可以多考虑一下。