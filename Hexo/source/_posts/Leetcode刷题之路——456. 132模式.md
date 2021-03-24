---
title: Leetcode刷题之路——456. 132模式
tags: [C++]
categories: [Leetcode]
date: 2021-3-24 10:15:00
updated: 2021-3-24 10:36:00
excerpt: Leetcode题库456. 132模式
---

# 题目描述

给定一个整数序列： ``a1, a2, ..., an`` ，一个132模式的子序列 ``ai, aj, ak`` 被定义为：当 ``i < j < k`` 时，``ai < ak < aj`` 。设计一个算法，当给定有 ``n`` 个数字的序列时，验证这个序列中是否含有132模式的子序列。

注意：n 的值小于15000。

示例1:

```
输入: [1, 2, 3, 4]

输出: False

解释: 序列中不存在132模式的子序列。
```


示例 2:

```
输入: [3, 1, 4, 2]

输出: True

解释: 序列中有 1 个132模式的子序列： [1, 4, 2].
```


示例 3:

```
输入: [-1, 3, 2, 0]

输出: True

解释: 序列中有 3 个132模式的的子序列: [-1, 3, 2], [-1, 3, 0] 和 [-1, 2, 0].
```

# 题目分析

## 单调栈

可以倒着遍历输入的序列，寻找符合要求 ``3`` 和 ``2`` 。

* 当寻找到新的数字比 ``3`` 要大时，则我们需要更新 ``3`` 的值，把原先的 ``3`` 作为 ``2`` 
* 当寻找到比 ``2`` 大比 ``3`` 小的数时，这个数可以作为新的 ``3`` 
* 当寻找到小于 ``2`` 的数时，则找到了 ``1`` ，符合要求，返回 ``true`` 
* 遍历完成没有返回，则返回 ``false`` 。

根据上面的分析，我们可以维护一个 ``3`` 的单调栈，因为当 ``3`` 和 ``2`` 的数值越大，我们找到 ``1`` 的可能性也越大。那么可以针对上述第一步利用单调栈的 ``push`` 、 ``pop`` 保证 ``3`` 和 ``2`` 找到的是理论上的最大数值。

具体代码如下：

```c++
class Solution {
public:
    bool find132pattern(vector<int>& nums) {
        int len = nums.size();
        if(len < 3) {
            return false;
        }
        
        stack<int> mystack;
        int two = INT_MIN;
        for(int i = len - 1; i >= 0; --i) {
            if(nums[i] >= two) {
                while(!mystack.empty() && nums[i] > mystack.top()) {
                    two = mystack.top();
                    mystack.pop();
                }
                mystack.push(nums[i]);
            }
            else {
                return true;
            }
        }
        return false;
    }
};
```
