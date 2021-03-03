---
title: Leetcode刷题之路——338. 比特位计数
tags: [C++]
categories: [Leetcode]
date: 2021-3-3 15:03:00
updated: 2021-3-3 17:01:00
excerpt: Leetcode题库338. 比特位计数
---

# 题目描述

给定一个非负整数 ``num`` 。对于 ``0 ≤ i ≤ num`` 范围中的每个数字 ``i`` ，计算其二进制数中的 ``1`` 的数目并将它们作为数组返回。

示例 1:

```
输入: 2
输出: [0,1,1]
```

示例 2:

```
输入: 5
输出: [0,1,1,2,1,2]
```

进阶:

* 给出时间复杂度为 ``O(n*sizeof(integer))`` 的解答非常容易。但你可以在线性时间 ``O(n)`` 内用一趟扫描做到吗？
* 要求算法的空间复杂度为 ``O(n)`` 。
* 你能进一步完善解法吗？要求在C++或任何其他语言中不使用任何内置函数（如 C++ 中的 ``__builtin_popcount`` ）来执行此操作。

# 题目分析

## 个人初尝试

比较直接的思路，把每一个数都计算一遍，然后存下来输出。每一个数用辗转相除法算出1的个数。

具体代码如下：

```c++
class Solution {
public:
    int countone(int num) {
        int count = 0;
        while(num >= 2) {
            if(num%2 == 1) {
                ++count;
            }
            num = num/2;
        }
        if(num == 1) {
            ++count;
        }
        return count;
    }
    
    vector<int> countBits(int num) {
        vector<int> result(num + 1);
        for(int i=0; i<=num; ++i) {
            result[i] = countone(i);
        }
        return result;
    }
};
```

在第一次时出现了问题，得到的答案不对。仔细逐行分析之后发现是 ``while`` 中的 ``if`` 放到了除法后面，这就导致了取余的时候实际已经不是最开始的那个数了。调整顺序后问题解决。

虽然达成了题目的要求，但是对于进阶的要求其实并不满足，时间上仍然比较长。由于自己没有啥更好的思路了，看题解！

## 动态规划

### 最低有效位

对于任意一个正整数 ``x``  ，将其右移一位，相当于将该数的最低位去掉。如果去掉最低位的数的比特数已经知道，则原数的比特数也可以推理得到。如果原数为偶数，则比特数相同；为奇数则比特数 ``+1`` 。即
$$
bit[x] = 
	\begin{cases}
    	bit[{x \over 2}],  & \text{如果 $x$ 是偶数} \\
        bit[{x \over 2}] + 1, & \text{如果 $x$ 是奇数} \\
    \end{cases}
$$
按照这个思路，从0开始遍历即可得到所需的bit值。其中奇数偶数的判断可以化简为对2取模。

具体代码如下

```c++
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int> result(num + 1);
        for(int i=0; i<=num; ++i) {
            result[i] = result[i >> 1] + i%2;
        }
        return result;
    }
};
```

# 一些细节的总结

* 注意代码运行的逻辑顺序！

