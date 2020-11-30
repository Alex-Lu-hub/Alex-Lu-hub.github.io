---
title: Leetcode刷题之路——16.11.跳水板
tags: [C++]
categories: [Leetcode]
date: 2020-11-30 23:40:00
updated: 2020-12-1 00:42:00
excerpt: Leetcode题库16.11.跳水板
---

# 题目描述

你正在使用一堆木板建造跳水板。有两种类型的木板，其中长度较短的木板长度为 ``shorter`` ，长度较长的木板长度为 ``longer`` 。你必须正好使用 ``k`` 块木板。编写一个方法，生成跳水板所有可能的长度。

返回的长度需要从小到大排列。

**示例 1**

```
输入：
shorter = 1
longer = 2
k = 3
输出： [3,4,5,6]
解释：
可以使用 3 次 shorter，得到结果 3；使用 2 次 shorter 和 1 次 longer，得到结果 4 。以此类推，得到最终结果。
```

**提示：**

* 0 < shorter <= longer
* 0 <= k <= 100000

# 题目分析

## 个人初尝试

简单的在纸上画一画，可以得出如下的列表

| shorter个数 | longer个数 |
| :---------: | :--------: |
|      k      |     0      |
|     k-1     |     1      |
|     k-2     |     2      |
|     ...     |    ...     |
|      0      |     k      |

长度从小到大排列，就是从上表的第一行到最后一行算出来。计算公式为
$$
shorter \times 1 + longer \times 2
$$
接下来的算法实现就简单了，一个循环将所有的长度算出来即可，具体代码如下：

```C++
class Solution {
public:
    vector<int> divingBoard(int shorter, int longer, int k) {
        vector<int>lenthlist;
        for(int i = 0; i < k+1; ++i) {
            lenthlist.push_back((k-i)*shorter + i*longer);
        }
        return lenthlist;
    }
};
```

进行测试，发现无法通过用例，说明如下：

> 输入：
>
> 1 1 0
>
> 输出：
>
> [0]
>
> 预期结果：
>
> []

阿这，我输出0和输出空不是差不多吗，醉了。。。。。。加一层 ``k = 0`` 不执行试试，代码如下

```C++
class Solution {
public:
    vector<int> divingBoard(int shorter, int longer, int k) {
        vector<int>lenthlist;
        if(k != 0) {
            for(int i = 0; i < k+1; ++i) {
                lenthlist.push_back((k-i)*shorter + i*longer);
            }
        }
        return lenthlist;
    }
};
```

还是有一个用例过不了，说明如下：

> 输入：
>
> 1 1 100000
>
> 输出：
>
> [100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,100000,10000...
>
> 预期结果：
>
> [100000]

显然，这说明了上面的代码么有处理相同长度的情况，思考一下如何处理

仔细分析一下，什么时候会有相同长度呢？如果 ``shorter`` 和 ``longer`` 长度不一样的话，是绝对不会出现相同长度，要想出线相同的情况，必须是二者都相同，而且该情况下所有长度都一样。再加一层判断试试，代码如下

```C++
class Solution {
public:
    vector<int> divingBoard(int shorter, int longer, int k) {
        vector<int>lenthlist;
        if(k != 0) {
            if(shorter != longer) {
                for(int i = 0; i < k+1; ++i) {
                    lenthlist.push_back((k-i)*shorter + i*longer);
                }
            }
            else {
                lenthlist.push_back(k*shorter);
            }
        }
        return lenthlist;
    }
};
```

好的，这次可以通过用例了，但是效率低的可怕。。。。。。没有别的特别思路，看看大佬们咋做的

## 一些优化方案

### 固定vector长度

显然，这道题目可以看出明显的数学规律，返回的 ``vector`` 长度是固定的， ``k = 0`` 返回空， ``shorter = longer`` 返回长度为 ``1`` ，其它的返回 ``k+1`` 的长度。那么我们的解法中也固定好长度，既可以节省空间，直接赋值也比 ``push_back`` 更快一些

代码如下

```C++
class Solution {
public:
    vector<int> divingBoard(int shorter, int longer, int k) {
        if(k == 0) {
            vector<int>lenthlist;
            return lenthlist;
        }
        if(shorter == longer) {
            vector<int>lenthlist(1);
            lenthlist[0] = k*shorter;
            return lenthlist;
        }
        vector<int>lenthlist(k+1);
        for(int i = 0; i < k+1; ++i) {
            lenthlist[i] = (k-i)*shorter + i*longer;
        }
        return lenthlist;
    }
};
```

这一次的性能提高极大！开心！

### 递归实现

递归的话我自己用的不是很多，也就没有想到这个。但是这个题目递归也挺简单，从输入后，每一次 ``shorter - 1`` 、 ``longer + 1`` 就可以了。利用 ``sort`` 排序， ``unique`` 去重。具体代码实现如下

```C++
class Solution {
public:
vector<int> v;
    void dg(int shorter,int longer,int m,int n){
        if(m<0||n<0){
            return;
        }
        int t=shorter*m+longer*n;
        v.push_back(t);
        dg(shorter,longer,m-1,n+1);
    }
    vector<int> divingBoard(int shorter, int longer, int k) {
        if(k==0){
            return v;
        }
        dg(shorter,longer,k,0);
        sort(v.begin(),v.end());
        v.erase(unique(v.begin(),v.end()),v.end());
        return v;
    }
};
```

不过不得不说，递归的性能极差。。。。。。就当熟悉用法了

# 一些细节的总结

* ``unique`` 函数是将一组相邻重复的元素一起放到元素末尾，并返回重复区域的首地址，因此使用 ``unique`` 之前需要进行排序
* 初始化 ``vector`` 时不需要空格，指定长度用 ``()`` 
* 要多考虑一些情况，尽可能完善