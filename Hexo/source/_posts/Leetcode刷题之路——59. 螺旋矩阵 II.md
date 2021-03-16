---
title: Leetcode刷题之路——59. 螺旋矩阵 II
tags: [C++]
categories: [Leetcode]
date: 2021-3-16 12:39:00
updated: 2021-3-16 12:42:00
excerpt: Leetcode题库59. 螺旋矩阵 II
---

# 题目描述

给你一个正整数 ``n`` ，生成一个包含 ``1`` 到 ``n^2`` 所有元素，且元素按顺时针顺序螺旋排列的 ``n x n`` 正方形矩阵 ``matrix`` 。

 

示例 1：

```
输入：n = 3
输出：[[1,2,3],[8,9,4],[7,6,5]]
```


示例 2：

```
输入：n = 1
输出：[[1]]
```


提示：

*  ``1 <= n <= 20`` 

# 题目分析

## 个人初尝试

这个题目和之前的 [螺旋矩阵](https://alex-lu-hub.github.io/2021/03/15/Leetcode%E5%88%B7%E9%A2%98%E4%B9%8B%E8%B7%AF%E2%80%94%E2%80%9454.%20%E8%9E%BA%E6%97%8B%E7%9F%A9%E9%98%B5/) 如出一辙，也比较顺畅，注意边界问题即可。

一个比较直观的思路，顺时针遍历其实可以分成四个遍历步骤：

* 从左边第一个往右边最后一个遍历
* 从上面第一个往下面最后一个遍历
* 从右边最后一个往左边遍历
* 从下面最后一个往上面遍历

所以遍历的关键就在于把四个点确定了：左边界、右边界、上边界、下边界。确定这四个边界后，就按上面的四个步骤遍历，每完成一个步骤就更新需要更新的边界，不断循环直至四个边界出现重叠现象，则遍历完成。

具体代码如下：

```c++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        if(n == 0) {
            return {};
        }
        vector<vector<int>> ans(n, vector<int>(n));
        int up = 0, down = n, left = 0, right = n, num = 1;
        while(num <= n*n) {
            for(int i = left; i < right; ++i) {
                ans[up][i] = num;
                ++num;
            }
            ++up;
            for(int i = up; i < down; ++i) {
                ans[i][right - 1] = num;
                ++num;
            }
            --right;
            for(int i = right - 1; i >= left; --i) {
                ans[down - 1][i] = num;
                ++num;
            }
            --down;
            for(int i = down - 1; i >= up; --i) {
                ans[i][left] = num;
                ++num;
            }
            ++left;
        }
        return ans;
    }
};
```

