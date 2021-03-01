---
title: Leetcode刷题之路——303.区域和检索 - 数组不可变
tags: [C++]
categories: [Leetcode]
date: 2021-3-1 16:10:00
updated: 2021-3-1 17:56:00
excerpt: Leetcode题库303.区域和检索 - 数组不可变
---

# 题目描述

给定一个整数数组  ``nums`` ，求出数组从索引 ``i`` 到 ``j``（ ``i ≤ j`` ）范围内元素的总和，包含 ``i`` 、 ``j`` 两点。

实现 ``NumArray`` 类：

*  ``NumArray(int[] nums)`` 使用数组 ``nums`` 初始化对象
*  ``int sumRange(int i, int j)`` 返回数组 ``nums`` 从索引 ``i`` 到 ``j``（ ``i ≤ j`` ）范围内元素的总和，包含  ``i`` 、 ``j`` 两点（也就是 ``sum(nums[i], nums[i + 1], ... , nums[j])`` ）

示例：

```
输入：
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
输出：
[null, 1, -1, -3]

解释：
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1)) 
numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))
```


提示：

*  ``0 <= nums.length <= 104`` 
*  ``-105 <= nums[i] <= 105`` 
*  ``0 <= i <= j < nums.length`` 
* 最多调用 ``10^4`` 次 ``sumRange`` 方法

# 题目分析

## 个人初尝试

这个题目比较简单，直观的想法就是把数组存下来，然后按照范围遍历一遍去加起来就可以了。当然，在加的时候想了一下，可以同时从两头加，应该可以提高一些时间，便这么做了。（虽然在建立向量和保存内容的时候犯了亿点点地基错误233）

具体代码如下

```C++
class NumArray {
public:
    vector<int> numarray;
    NumArray(vector<int>& nums) {
        for(auto data: nums) {
            numarray.push_back(data);
        }
    }
    
    int sumRange(int i, int j) {
        int count = 0;
        int sum = 0;
        while(i + count < j - count) {
            sum += numarray[i + count] + numarray[j - count];
            count ++;
        }
        if(i + count == j - count) {
            sum += numarray[i + count];
        }
        return sum;
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * int param_1 = obj->sumRange(i,j);
 */
```

执行下来后可以通过，但是在速度上和内存消耗上确实是比较拉跨，思考一下怎么调优。

## 前缀和

自己思考无果，毕竟想着累加总是要遍历的嘛，怎么降复杂度呢？看了题解，原来不需要把数组拷贝下来，只需要新建一个数组，用于保存在这个下标前的总和大小，即前缀和即可。。。。。。当需要知道 ``i~j`` 的和时，只需要把这个前缀和数组的 ``j+1`` 项（前 ``j`` 个的和）减去 ``i`` 项（前 ``i-1`` 个的和），就可以得到了。

具体代码如下

```c++
class NumArray {
public:
    vector<int> sums;

    NumArray(vector<int>& nums) {
        int n = nums.size();
        sums.resize(n + 1);
        for (int i = 0; i < n; i++) {
            sums[i + 1] = sums[i] + nums[i];
        }
    }

    int sumRange(int i, int j) {
        return sums[j + 1] - sums[i];
    }
};
```

# 一些细节的总结

* 在能明确空间大小的时候，最好限定开辟结构的大小，以节省空间
* 平时多练练看看，好久不写连基本的用法都忘了。。。。。。

