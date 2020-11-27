---
title: Leetcode刷题之路——1052.爱生气的书店老板
tags:  [C++]
categories: [Leetcode]
date: 2020-11-26 23:40:00
updated: 2020-11-27 23:20:00
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

将上述代码提交后，最后一个测试用例超时，查看后发现是一个超长的测试用例，看来还是代码的性能不够。。。。。。没有啥好的思路，学习一下别人的做法

## 滑动窗口方法

滑动窗口方法标准情况下采用**左右窗口指针** ``left`` 、 ``right``，用来标记窗口的位置。用 ``right - left`` 标志窗口的大小。两指针都从0开始增加， ``right`` 先增，当窗口大小超过目标时移动 ``left`` 维持窗口大小。每次移动窗口位置都需要判断窗口内需要的信息，是否需要更新需要得到的内容。

在这个例子中，具体的滑窗实现可以如下：

```C++
class Solution {
public:
    int maxSatisfied(vector<int>& customers, vector<int>& grumpy, int X) {
        //初始化变量,left, right为左右指针，satisfy为满意人数，angry为最大受技能影响的生气人数, anginwindow即技能窗口中的生气人数
        int left = 0, right = 0, satisfy = 0, angry = 0, anginwindow = 0;

        //滑窗寻找使用技能后最大满意人数
        while(right < customers.size()) {
            if(grumpy[right] == 0)//不生气的，加入到满意人数中
                satisfy += customers[right];            
            else//生气的，加入到受技能影响的生气人数中
                anginwindow += customers[right];
            ++right;
            //窗口变化的处理
            while(right - left > X) {
                //此时需要移动左指针，如果左指针有受技能影响的人数，需减去
                anginwindow -= grumpy[left]?customers[left]:0;
                ++left;
            }
            angry = max(angry,anginwindow);//每次窗口变化需要更新手机能影响的最大生气人数
        }
    return satisfy + angry;//满意人数+受技能影响最大生气人数即为最大人数
    }
};
```

这个滑窗虽然能够解决这一问题，且成功通过了所有的用例，但是在效果上不是特别理想，还可以进一步改进。

### 该题更进一步的滑窗

因为题目为固定窗口，所以可以计算出第一个窗口的值作为最大值，然后每次滑动一格算下一个窗口的值，两者比较进行保留，最终得到最大值。

具体实现代码可以如下：

```C++
class Solution {
public:
    int maxSatisfied(vector<int>& customers, vector<int>& grumpy, int X) {
        //初始化变量,satisfy为最大窗口变满意人数，sinwindow即窗口滑动过程中的变满意人数， basenum为基础满意人数
        int satisfy = 0, sinwindow = 0, basenum = 0;

        //固定滑窗的处理
        for(int i=0 ; i < customers.size(); ++i) {
            if(grumpy[i])//计算窗口内此刻的生气变满意人数，相当于右指针每次增加最右侧值
                sinwindow += customers[i];
            else//计算基础满意人数
                basenum += customers[i];
            
            //开始超出窗口大小，每次需要按要求减去最左侧，并比较得出最大窗口变满意人数
            if(i >= X-1) {
                satisfy = max(satisfy, sinwindow);
                sinwindow -= grumpy[i-(X-1)]?customers[i-(X-1)]:0;
            }
        }

        //最大值就是基础满意人数加最大窗口变满意人数
        return satisfy + basenum;
    }
};
```

# 一些小细节总结

* ``vector`` 初始化用 ``{}`` 而不是 ``[]``
* 类&对象需要先声明，就像声明基本类型的变量一样
*  ``using name space std`` 还是挺方便的，可用
*  ``vector`` 的名字本身就是一个指针，传参不需要加 ``*`` 
*  ``if`` 和 ``? :`` ， ``if`` 是用于不同逻辑代码段，而  ``? :`` 用于不同逻辑赋值
*  ``i++`` 和 ``++i`` ，在如 ``for`` 循环的使用中，用 ``++i`` 性能更好，原因如下：
  *   ``i++`` 由于是在使用当前值之后再 ``+1`` ，所以需要一个临时的变量来转存
  *  而 ``++i`` 则是在直接 ``+1`` ，省去了对内存的操作的环节，相对而言能够提高性能