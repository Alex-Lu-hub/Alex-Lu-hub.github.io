---
title: Leetcode刷题之路——73. 矩阵置零
tags: [C++]
categories: [Leetcode]
date: 2021-3-21 10:48:00
updated: 2021-3-21 11:10:00
excerpt: Leetcode题库73. 矩阵置零
---

# 题目描述

给定一个 ``m x n`` 的矩阵，如果一个元素为 **0** ，则将其所在行和列的所有元素都设为 **0** 。请使用 **原地** 算法。

进阶：

* 一个直观的解决方案是使用  ``O(mn)`` 的额外空间，但这并不是一个好的解决方案。
* 一个简单的改进方案是使用 ``O(m + n)`` 的额外空间，但这仍然不是最好的解决方案。
* 你能想出一个仅使用常量空间的解决方案吗？


示例 1：

```
输入：matrix = [[1,1,1],[1,0,1],[1,1,1]]
输出：[[1,0,1],[0,0,0],[1,0,1]]
```


示例 2：

```
输入：matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
输出：[[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```


提示：

*  ``m == matrix.length`` 
*  ``n == matrix[0].length`` 
*  ``1 <= m, n <= 200`` 
*  ``-231 <= matrix[i][j] <= 231 - 1`` 

# 题目分析

## 个人初尝试

一个直接可以想到的思路就是，先遍历一遍这个矩阵，把出现零的行列记录下来。再遍历一次，依据记录的行列更新矩阵的值。第二次可以不用遍历，而是直接根据记录的行列值针对性赋值。

具体代码如下：

```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        vector<pair<int, int>> flag;
        if(matrix.empty()) {
            return;
        }
        int rowlen = matrix[0].size(), columnlen = matrix.size(); 
        for(int i = 0; i < columnlen; ++i) {
            for(int j = 0; j < rowlen; ++j) {
                if(matrix[i][j] == 0) {
                    flag.push_back(pair<int, int>(i, j));
                }
            }
        }
        for(auto cp: flag) {
            for(int i = 0; i < columnlen; ++i) {
                matrix[i][cp.second] = 0;
            }
            for(int j = 0; j < rowlen; ++j) {
                matrix[cp.first][j] = 0;
            }
        }
    }
};
```

## 使用第一行和第一列来进行标记

在上述的方法中，使用了一个 ``vector<pair<int,int>>`` 来记录标志位，但实际上还可以直接用矩阵本身的第一行和第一列来进行标志处理。如果对应的行列出现了0，则将第一行第一列中对应的值变为0，记为标记。后面遍历其他行来更行其他行的数值。但是这样做会导致无法确定第一行第一列原始是否有0。所以还需先开始遍历第一行第一列来确定是否有0，设置额外的两个标志位。

具体的代码如下：

```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        if(matrix.empty()) {
            return;
        }
        int rowlen = matrix[0].size(), columnlen = matrix.size(); 
        bool rowflag = false, columnflag = false;
        for(int i = 0; i < columnlen; ++i) {
            if(matrix[i][0] == 0) {
                columnflag = true;
                break;
            }
        }
        for(int i = 0; i < rowlen; ++i) {
            if(matrix[0][i] == 0) {
                rowflag = true;
                break;
            }
        }
        for(int i = 0; i < columnlen; ++i) {
            for(int j = 0; j < rowlen; ++j) {
                if(matrix[i][j] == 0) {
                    matrix[0][j] = 0;
                    matrix[i][0] = 0;
                }
            }
        }
        for(int i = 1; i < columnlen; ++i) {
            for(int j = 1; j < rowlen; ++j) {
                if(matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        if(columnflag) {
            for(int i = 0; i < columnlen; ++i) {
                matrix[i][0] = 0;
            }
        }
        if(rowflag) {
            for(int i = 0; i < rowlen; ++i) {
                matrix[0][i] = 0; 
            }
        }
    }
};
```

# 一些细节的总结

* 循环的边界！数组的边界！这些地方你可长点心吧！