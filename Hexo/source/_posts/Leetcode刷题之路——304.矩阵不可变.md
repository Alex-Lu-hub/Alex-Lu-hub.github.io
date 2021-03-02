---
title: Leetcode刷题之路——304.二维区域和检索 - 矩阵不可变
tags: [C++]
categories: [Leetcode]
date: 2021-3-2 10:11:00
updated: 2021-3-2 11:27:00
excerpt: Leetcode题库304.二维区域和检索 - 矩阵不可变

---

# 题目描述

给定一个二维矩阵，计算其子矩形范围内元素的总和，该子矩阵的左上角为 ``(row1, col1)`` ，右下角为 ``(row2, col2)`` 。

示例：

```
给定 matrix = [
  [3, 0, 1, 4, 2],
  [5, 6, 3, 2, 1],
  [1, 2, 0, 1, 5],
  [4, 1, 0, 1, 7],
  [1, 0, 3, 0, 5]
]

sumRegion(2, 1, 4, 3) -> 8
sumRegion(1, 1, 2, 2) -> 11
sumRegion(1, 2, 2, 4) -> 12
```


提示：

* 你可以假设矩阵不可变。
* 会多次调用 ``sumRegion`` 方法。
* 你可以假设 ``row1 ≤ row2`` 且 ``col1 ≤ col2`` 。

# 题目分析

## 个人初尝试

这个题目实际就是昨天的前缀和的二维形式，有了一个思路就是把它看作多个一维的前缀和，用一个二维的 ``sum`` 存储每一行的前缀和，按照输入把每行通过前缀和求出来再加到一起就得到了一个方形区域内的总和了。

具体代码如下

```c++
class NumMatrix {
public:
    vector<vector<int>> sum;
    NumMatrix(vector<vector<int>>& matrix) {
        if(matrix.size() != 0) {
            int rowlen = matrix[0].size();
            int columnlen = matrix.size();
            sum.resize(columnlen);
            for(int i = 0; i < columnlen; i++) {
                sum[i].resize(rowlen + 1);
                for(int j = 0; j < rowlen; j++) {
                    sum[i][j + 1] = sum[i][j] + matrix[i][j];
                }
            } 
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        int total = 0;
        for(int i = row1; i <= row2; i++)　{
            total += sum[i][col2 + 1] - sum[i][col1];
        }
        return total;
    }
};

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix* obj = new NumMatrix(matrix);
 * int param_1 = obj->sumRegion(row1,col1,row2,col2);
 */
```

测试用例第一个就是输入为空，结果第一次就挂了。。。。。。所以后来加了一个判断输入不为空。

第二次通过了，但是时间上不是特别理想。于是进一步思考怎么调优。

## 进一步的优化

先前的思路是直接转化为多个一维的前缀和解决，那能不能直接用二维的前缀和呢？用一个二维的 ``sum`` 存储从 ``(0, 0)`` 到 ``(i, j)`` 的大小，这样在最后求和时又可以得到像一维前缀和一样 ``O(1)`` 的复杂度的查询算法。

具体代码如下

```c++
class NumMatrix {
public:
    vector<vector<int>> sum;
    NumMatrix(vector<vector<int>>& matrix) {
        if(matrix.size() != 0) {
            int rowlen = matrix[0].size();
            int columnlen = matrix.size();
            sum.resize(columnlen + 1);
            sum[0].resize(rowlen + 1);
            for(int i = 0; i < columnlen; i++) {
                sum[i + 1].resize(rowlen + 1);
                for(int j = 0; j < rowlen; j++) {
                    sum[i + 1][j + 1] = sum[i][j + 1] + sum[i + 1][j] - sum[i][j] + matrix[i][j];
                }
            } 
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        int total = sum[row2 + 1][col2 + 1] - sum[row1][col2 + 1] - sum[row2 + 1][col1] + sum[row1][col1];
        return total;
    }
};

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix* obj = new NumMatrix(matrix);
 * int param_1 = obj->sumRegion(row1,col1,row2,col2);
 */
```

这个二维的前缀和就要注意在计算时的范围， ``+1`` 、 ``-1`` 已经要仔细分析。

# 一些细节的总结

* 边界条件注意判断是否为空
* 二位前缀和的计算范围要仔细判断

