---
title: Leetcode刷题之路——503. 下一个更大元素 II
tags: [C++]
categories: [Leetcode]
date: 2021-3-6 12:00:00
updated: 2021-3-6 13:46:00
excerpt: Leetcode题库503. 下一个更大元素 II
---

# 题目描述

给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

示例 1:

```c++
输入: [1,2,1]
输出: [2,-1,2]
解释: 第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```

注意: 输入数组的长度不会超过 10000。

# 题目分析

## 个人初尝试

有一个很直接的思路，按照题目说的遍历比较就行了。

具体代码如下：

```c++
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int len = nums.size();
        vector<int> maxnums;
        maxnums.resize(len);
        for (int i = 0; i < len; i++) {
            int ret = -1;
            for (int j = i + 1; j < i + len; j++) {
                if(nums[j % len] > nums[i]) {
                    ret = nums[j % len];
                    break;
                }
            }
            maxnums[i] = ret;
        }
        return maxnums;
    }
};
```

第一次提交的时候出现了无法通过的问题，后来发现题目要求是第一个，而我是直接遍历了一遍找到最大的了。在第二层循环中加一个找到后break，问题解决。

AC是AC了，但是时间特别拉跨，毕竟是一个n2复杂度的方法。自己没有好的想法，看题解！

## 单调栈

而如果使用的是单调栈的话，可以做到 O(n) 的复杂度，我们将当前还没得到答案的下标暂存于栈内，从而实现「被动」更新答案。

也就是说，栈内存放的永远是还没更新答案的下标。

具体的做法是：

每次将当前遍历到的下标存入栈内，将当前下标存入栈内前，检查一下当前值是否能够作为栈内位置的答案（即成为栈内位置的「下一个更大的元素」），如果可以，则将栈内下标弹出。

如此一来，我们便实现了「被动」更新答案，同时由于我们的弹栈和出栈逻辑，决定了我们整个过程中栈内元素单调。

还有一些编码细节，由于我们要找每一个元素的下一个更大的值，因此我们需要对原数组遍历两次，对遍历下标进行取余转换。

以及因为栈内存放的是还没更新答案的下标，可能会有位置会一直留在栈内（最大值的位置），因此我们要在处理前预设答案为 -1。而从实现那些没有下一个更大元素（不出栈）的位置的答案是 -1。

```c++
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        vector<int> ret(n, -1);
        stack<int> stk;
        for (int i = 0; i < n * 2 - 1; i++) {
            while (!stk.empty() && nums[stk.top()] < nums[i % n]) {
                ret[stk.top()] = nums[i % n];
                stk.pop();
            }
            stk.push(i % n);
        }
        return ret;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/next-greater-element-ii/solution/xia-yi-ge-geng-da-yuan-su-ii-by-leetcode-bwam/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 一些细节的总结

* 单调栈的思路还需要多思考一下，理解这种算法的逻辑
