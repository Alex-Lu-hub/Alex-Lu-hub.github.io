---
title: Leetcode刷题之路——1052.爱生气的书店老板
tags:  [C++]
categories: [Leetcode]
date: 2020-11-26 23:40:00
updated: 2020-11-26 23:40:00
excerpt: Leetcode题库1052.爱生气的书店老板
---

# 题目描述

今天，书店老板有一家店打算试营业 ``customers.length`` 分钟。每分钟都有一些顾客（ ``customers[i]`` ）会进入书店，所有这些顾客都会在那一分钟结束后离开。

在某些时候，书店老板会生气。 如果书店老板在第 ``i`` 分钟生气，那么 ``grumpy[i] = 1`` ，否则 ``grumpy[i] = 0`` 。 当书店老板生气时，那一分钟的顾客就会不满意，不生气则他们是满意的。

书店老板知道一个秘密技巧，能抑制自己的情绪，可以让自己连续 ``X`` 分钟不生气，但却只能使用一次。

请你返回这一天营业下来，最多有多少客户能够感到满意的数量。


示例：

```
输入：customers = [1,0,1,2,1,1,7,5], grumpy = [0,1,0,1,0,1,0,1], X = 3
输出：16
解释：
书店老板在最后 3 分钟保持冷静。
感到满意的最大客户数量 = 1 + 1 + 1 + 1 + 7 + 5 = 16.
```

提示：

*  ``1 <= X <= customers.length == grumpy.length <= 20000`` 
*  ``0 <= customers[i] <= 1000`` 
*  ``0 <= grumpy[i] <= 1`` 

# 题目分析

## 个人初尝试

输入： `` vector<int>&cusstomers、vector<int>&grumpy、int X`` 

输出： ``int maxnum``

可以看到，在不考虑使用技能的情况下，满意的人数是确定的。很直接一个思路，将确定的满意人数保存下来，采取遍历的方法，每个时段把使用技能后增加的人数加上，得到一个最大的人数返回。

代码如下：

```C++
class Solution {
public:
    int maxSatisfied(vector<int>& customers, vector<int>& grumpy, int X) {
        //初始化变量
        int len = customers.size();//时间长度
        int basenum = 0;//基本的满意人数
        int maxnum = 0;//最大满意人数

        //遍历存储不用技能情况下的满意人数
        for(int i = 0; i < len; i++) {
            if(grumpy[i] == 0)
                basenum += customers[i];
        }

        //遍历寻找使用技能后最大满意人数
        for(int i = 0; i < len; i++) {
            int tempnum = basenum;//寻找最大值的中间变量
            
            //X时间的处理
            for(int j = 0; j < X; j++) {
                //防止越界
                if(i + j < len){
                    if(grumpy[i + j] == 1)//如果技能期间本来要生气，加上
                        tempnum += customers[i + j];
                }
            }

            if(tempnum > maxnum)//如果得到的大于目前最大的，更换目前最大值
                maxnum = tempnum;  
        }
    return maxnum;
    }
};
```

# 一些总结

* ``vector`` 初始化用 ``{}`` 而不是 ``[]``
* 类&对象需要先声明，就像声明基本类型的变量一样
*  ``using name space std`` 还是挺方便的，可用
*  ``vector`` 的名字本身就是一个指针，传参不需要加 ``*`` 