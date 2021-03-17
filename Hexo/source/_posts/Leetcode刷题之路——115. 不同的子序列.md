---
title: Leetcode刷题之路——115. 不同的子序列
tags: [C++]
categories: [Leetcode]
date: 2021-3-17 15:10:00
updated: 2021-3-17 15:24:00
excerpt: Leetcode题库59. 螺旋矩阵 II
---

# 题目描述

给定一个字符串 ``s`` 和一个字符串 ``t`` ，计算在 ``s`` 的子序列中 ``t`` 出现的个数。

字符串的一个 **子序列** 是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如， ``"ACE"`` 是 ``"ABCDE"`` 的一个子序列，而 ``"AEC"`` 不是）

题目数据保证答案符合 32 位带符号整数范围。

 

示例 1：

```
输入：s = "rabbbit", t = "rabbit"
输出：3
解释：
如下图所示, 有 3 种可以从 s 中得到 "rabbit" 的方案。
(上箭头符号 ^ 表示选取的字母)
rabbbit
^^^^ ^^
rabbbit
^^ ^^^^
rabbbit
^^^ ^^^
```

示例 2：

```
输入：s = "babgbag", t = "bag"
输出：5
解释：
如下图所示, 有 5 种可以从 s 中得到 "bag" 的方案。 
(上箭头符号 ^ 表示选取的字母)
babgbag
^^ ^
babgbag
^^    ^
babgbag
^    ^^
babgbag
  ^  ^^
babgbag
    ^^^
```


提示：

*  ``0 <= s.length, t.length <= 1000`` 
*  ``s`` 和 ``t`` 由英文字母组成

# 题目分析

## 动态规划

这个题目直接求助于题解了，动态规划的思路，关键就在于状态转移方程的构建。具体可参考 [官方题解](https://leetcode-cn.com/problems/distinct-subsequences/solution/bu-tong-de-zi-xu-lie-by-leetcode-solutio-urw3/) 。

利用 ``dp[i][j]`` 记录 ``s`` 中从 ``i`` 到结尾的子字符串的子序列在 ``t`` 中从 ``j`` 到结尾的子字符串中出现的个数。

边界情况：

 ``j`` 为 ``n`` 时，表示的是空字符串，空字符串是所有字符串的子串，赋值为 ``1`` ； ``i`` 为 ``m`` 时，同样的为空字符串，空字符串没有自己的子串，赋值为 ``0`` 

普遍情况：

当 ``s[i] ！= t[j]`` 时， ``dp[i][j]`` 只能考虑 ``s[i + 1]`` 对于 ``t[j]`` 的子串个数；

当 ``s[i] == t[j]`` 时， ``dp[i][j]`` 除上面的以外还可以考虑 ``s[i + 1]`` 对于 ``t[j + 1]`` 的子串个数，既将 ``i + 1`` 对应的字符删去后找子串。

具体代码如下：

```c++
class Solution {
public:
    int numDistinct(string s, string t) {
        int m = s.size(), n = t.size();
        if(m < n) {
            return 0;
        }
        vector<vector<long>> dp (m + 1, vector<long>(n + 1));
        for(int i = 0; i <= m; ++i) {
            dp[i][n] = 1;
        }
        for(int i = m - 1; i >= 0; --i) {
            char schar = s[i];
            for(int j = n - 1; j >= 0; --j) {
                char tchar = t[j];
                if(schar == tchar) {
                    dp[i][j] = dp[i + 1][j + 1] + dp[i + 1][j];
                }
                else {
                    dp[i][j] = dp[i + 1][j];
                }
            }
        }
        return dp[0][0];
    }
};
```

