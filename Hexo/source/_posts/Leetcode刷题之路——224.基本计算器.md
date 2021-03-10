---
title: Leetcode刷题之路——224. 基本计算器
tags: [C++]
categories: [Leetcode]
date: 2021-3-10 20:30:00
updated: 2021-3-10 20:49:00
excerpt: Leetcode题库224. 基本计算器

---

# 题目描述

实现一个基本的计算器来计算一个简单的字符串表达式 ``s`` 的值。



示例 1：

```
输入：s = "1 + 1"
输出：2
```

示例 2：

```
输入：s = " 2-1 + 2 "
输出：3
```

示例 3：

```
输入：s = "(1+(4+5+2)-3)+(6+8)"
输出：23
```


提示：

*  ``1 <= s.length <= 3 * 105``
*  ``s`` 由数字、 ``'+'`` 、 ``'-'`` 、 ``'('、')'`` 、和 ``' '`` 组成
*  ``s`` 表示一个有效的表达式

# 题目分析

对于具体的算法类不是特别熟悉，直接一个题解的看！

思路其实比较直接，就是把括号去掉，用一个数 ``{1, -1}`` 来表示要运算的数字前面的符号的正负。然后需要考虑括号对于符号的影响，利用一个栈来保存括号外符号对于括号内符号的影响。遇到 ``(`` 就入栈，遇到 ``)`` 就出栈。最后结合括号对符号的影响计算得数。

具体代码如下：

```C++
class Solution {
public:
    int calculate(string s) {
        stack<int> ops;
        ops.push(1);
        int sign = 1;

        int ret = 0;
        int n = s.length();
        int i = 0;
        while (i < n) {
            if (s[i] == ' ') {
                i++;
            } else if (s[i] == '+') {
                sign = ops.top();
                i++;
            } else if (s[i] == '-') {
                sign = -ops.top();
                i++;
            } else if (s[i] == '(') {
                ops.push(sign);
                i++;
            } else if (s[i] == ')') {
                ops.pop();
                i++;
            } else {
                long num = 0;
                while (i < n && s[i] >= '0' && s[i] <= '9') {
                    num = num * 10 + s[i] - '0';
                    i++;
                }
                ret += sign * num;
            }
        }
        return ret;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/basic-calculator/solution/ji-ben-ji-suan-qi-by-leetcode-solution-jvir/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

