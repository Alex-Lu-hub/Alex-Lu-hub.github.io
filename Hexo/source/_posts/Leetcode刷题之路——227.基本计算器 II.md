---
title: Leetcode刷题之路——224. 基本计算器 II
tags: [C++]
categories: [Leetcode]
date: 2021-3-11 12:22:00
updated: 2021-3-11 12:30:00
excerpt: Leetcode题库224. 基本计算器 II
---

# 题目描述

给你一个字符串表达式 ``s`` ，请你实现一个基本计算器来计算并返回它的值。

整数除法仅保留整数部分。

 

示例 1：

```
输入：s = "3+2*2"
输出：7
```

示例 2：

```
输入：s = " 3/2 "
输出：1
```

示例 3：

```
输入：s = " 3+5 / 2 "
输出：5
```


提示：

*  ``1 <= s.length <= 3 * 105``
*  ``s`` 由整数和算符 ``('+', '-', '*', '/')`` 组成，中间由一些空格隔开
*  ``s`` 表示一个 **有效表达式** 
* 表达式中的所有整数都是非负整数，且在范围 ``[0, 231 - 1]`` 内
* 题目数据保证答案是一个 **32-bit** 整数

# 题目分析

这个题目有了 [基本计算器](https://alex-lu-hub.github.io/2021/03/10/Leetcode%E5%88%B7%E9%A2%98%E4%B9%8B%E8%B7%AF%E2%80%94%E2%80%94224.%E5%9F%BA%E6%9C%AC%E8%AE%A1%E7%AE%97%E5%99%A8/) 的基础，就不觉得难了。还是用栈来实现，加减直接放入对应的数到栈内，乘除则把数和栈顶的数得到积/商，pop再入栈。

具体代码如下：

```C++
class Solution {
public:
    int calculate(string s) {
        int ans = 0, sign = 0;
        int i = 0, len = s.size();
        stack<int> mystack;
        while(i < len) {
            if(s[i] == ' ') {
                ++i ;
            }
            else if(s[i] == '+') {
                sign = 0; 
                ++i;
            }
            else if(s[i] == '-') {
                sign = 1;
                ++i;
            }
            else if(s[i] == '*') {
                sign = 2;
                ++i;
            }
            else if(s[i] == '/') {
                sign = 3;
                ++i;
            }
            else {
                long num = 0;
                while(i < len && s[i] >= '0' && s[i] <= '9' ) {
                    num = num * 10 + s[i] - '0';
                    ++i;
                }
                if(sign == 1) {
                    num = -1 * num;
                }
                else if(sign == 2) {
                    num = num * mystack.top();
                    mystack.pop();
                }
                else if(sign == 3) {
                    num = mystack.top() / num;
                    mystack.pop();
                }
                mystack.push(num);
            }
        }
        while(!mystack.empty()) {
            ans +=mystack.top();
            mystack.pop();
        }
        return ans;
    }
};
```

就是在写的时候掉了一个 ``++i`` ，导致一直超时。。。。。。debug了好久才发现。还是要细心点。