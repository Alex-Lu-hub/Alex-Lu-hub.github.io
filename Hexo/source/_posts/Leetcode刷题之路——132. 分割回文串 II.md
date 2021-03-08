---
title: Leetcode刷题之路——132. 分割回文串 II
tags: [C++]
categories: [Leetcode]
date: 2021-3-8 14:20:00
updated: 2021-3-8 14:32:00
excerpt: Leetcode题库132. 分割回文串 II
---

# 题目描述

给你一个字符串 ``s`` ，请你将 ``s`` 分割成一些子串，使每个子串都是回文。

返回符合要求的 **最少分割次数** 。

示例 1：

```
输入：s = "aab"
输出：1
解释：只需一次分割就可将 s 分割成 ["aa","b"] 这样两个回文子串。
```

示例 2：

```
输入：s = "a"
输出：0
```


示例 3：

```
输入：s = "ab"
输出：1
```


提示：

*  ``1 <= s.length <= 2000``
*  ``s`` 仅由小写英文字母组成

# 题目分析

## 动态规划

这道题目的回文判断可以按照 [我上一篇帖子](https://alex-lu-hub.github.io/2021/03/07/Leetcode%E5%88%B7%E9%A2%98%E4%B9%8B%E8%B7%AF%E2%80%94%E2%80%94131.%E5%88%86%E5%89%B2%E5%9B%9E%E6%96%87%E4%B8%B2/) 的判断方法，但是如果要统计最小的分割次数，用回溯法的方法很明显会十分耗时。一时间想不到好的办法了，就看了题解。没想到居然是和最长递增子串一个思路的动态规划。

状态转移方程很容易给出， ``f[i] = min{f[i], f[j] + 1}`` ，即在第二层遍历时，需要得到面所有的最小分割次数最小值赋给当前位置，在比较是注意是从 ``[j + 1]`` 到 ``[i]`` 是否为回文串。

具体代码如下：

```C++
class Solution {
public:
    int minCut(string s) {
        int len = s.size();
        vector<vector<int>> jugde (len, vector<int>(len, true));
        for(int i = len - 1; i >= 0; --i) {
            for(int j = i + 1; j < len; ++j) {
                jugde[i][j] = (s[i] == s[j]) && jugde[i + 1][j - 1];
            }
        }
        vector<int> count(len, INT_MAX);
        for(int i = 0; i < len; ++i) {
            if(jugde[0][i]) {
                count[i] = 0;
            }
            else {
                for(int j = 0; j < i; ++j) {
                    if(jugde[j + 1][i]) {
                        count[i] = min(count[i], count[j] + 1);
                    }
                }
            }
        }
        return count[len - 1];
    }
};
```

