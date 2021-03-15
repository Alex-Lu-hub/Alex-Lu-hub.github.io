---
title: Leetcode刷题之路——54. 螺旋矩阵
tags: [C++]
categories: [Leetcode]
date: 2021-3-15 13:46:00
updated: 2021-3-15 14:00:00
excerpt: Leetcode题库54. 螺旋矩阵

---

# 题目描述

给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。

 

示例 1：

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```


示例 2：

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```


提示：

*  ``m == matrix.length`` 
*  ``n == matrix[i].length`` 
*  ``1 <= m, n <= 10`` 
*  ``-100 <= matrix[i][j] <= 100`` 

# 题目分析

## 个人初尝试

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
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        if(matrix.empty()) {
            return ans;
        }
        int left = 0, right = matrix[0].size(), up = 0, down = matrix.size();
        while(true) {
            for(int i = left; i < right; ++i) {
                ans.push_back(matrix[up][i]);            
            }
            ++up;
            if(left >= right || up >= down) {
                return ans;
            }
            for(int i = up; i < down; ++i) {
                ans.push_back(matrix[i][right - 1]);
            }
            --right;
            if(left >= right || up >= down) {
                return ans;
            }
            for(int i = right - 1; i >= left; --i) {
                ans.push_back(matrix[down - 1][i]);
            }
            --down;
            if(left >= right || up >= down) {
                return ans;
            }
            for(int i = down - 1; i >= up; --i) {
                ans.push_back(matrix[i][left]);
            }
            ++left;
            if(left >= right || up >= down) {
                return ans;
            }
        }
    }
};
```

这次AC的时间和空间都花费的挺少的，说明是一个不错的解答思路。最开始出现了遍历的边界和退出判断不全面的问题，出现问题后也很快解决掉了。

# 一些细节的总结

* 这类题目不是特别难，关键是边界值的判断、退出条件的判断、以及赋值重复的判断。需要多想多刷。


