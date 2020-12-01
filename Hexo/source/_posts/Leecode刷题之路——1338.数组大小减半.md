---
title: Leetcode刷题之路——1338.数组大小减半
tags: [C++]
categories: [Leetcode]
date: 2020-12-1 23:20:00
updated: 2020-12-2 01:01:00
excerpt: Leetcode题库1338.数组大小减半
---

# 题目描述

给你一个整数数组 ``arr`` 。你可以从中选出一个整数集合，并删除这些整数在数组中的每次出现。

返回 **至少** 能删除数组中的一半整数的整数集合的最小大小。

 

示例 1：

```
输入：arr = [3,3,3,3,5,5,5,2,2,7]
输出：2
解释：选择 {3,7} 使得结果数组为 [5,5,5,2,2]、长度为 5（原数组长度的一半）。
大小为 2 的可行集合有 {3,5},{3,2},{5,2}。
选择 {2,7} 是不可行的，它的结果数组为 [3,3,3,3,5,5,5]，新数组长度大于原数组的二分之一。
```

示例 2：

```
输入：arr = [7,7,7,7,7,7]
输出：1
解释：我们只能选择集合 {7}，结果数组为空。
```

示例 3：

```
输入：arr = [1,9]
输出：1
```

示例 4：

```
输入：arr = [1000,1000,3,7]
输出：1
```

示例 5：

```
输入：arr = [1,2,3,4,5,6,7,8,9,10]
输出：5
```


提示：

*  ``1 <= arr.length <= 10^5`` 
*  ``arr.length`` 为偶数
*  ``1 <= arr[i] <= 10^5`` 

# 题目分析

## 个人初尝试

显然，这个题目涉及到了数据的存储，我需要知道每个数字存在多少个，才能去选择删减。删减的思路也挺直接的，从最大的删减起，当删去后的数组大小小于或等于原长度的一半，当作删减完成，返回删减的元素数值。

设及存储，显然用数组来存是不大理智的，因为我们需要同时储存数字和个数。直接定义数组只能把下标当作数字，那就给排序带来了相当大的麻烦，且空间上不方便开辟。那么自然的，想到用 ``map`` 来存储，用一对键值对来存储数字和个数。

排序方面自然是想要用 ``sort()`` 进行排序，而且降序排序就足够了。但是无法对 ``map`` 来进行降序排序，所以在 ``map`` 生成后，我们还需要把生成的 ``map`` 中的数据转存出来。因为数字数目的不确定，而且我们的输出值不需要知道要删去哪些数字。那么，我们就用一个 ``vector`` 保存 ``map`` 中个数的那一栏就好了，然后再排序，开始进行计算。

仔细想想，不需要进行 ``map`` 的排序（因为按照key值排序没有意义），那么使用 ``unordered_map`` 似乎是一个更有助于性能提升的选择。

具体代码实现如下

```C++
class Solution {
public:
    int minSetSize(vector<int>& arr) {
        int count = 0;
        int cutsize = 0;        

        unordered_map<int,int>umap;
        
        //循环构建map
        for(int i = 0; i < arr.size(); ++i) {
            umap[arr[i]]++;
        }
        
        vector<int>numlist;

        //循环构建vector
        for(int i = 0; i < umap.size(); ++i) {
            numlist.push_back(umap[i]);
        }

        //对vector进行排序
        sort(numlist.begin(), numlist.end(), greater<int>());

        //循环开始计算
        for(int i = 0; i < numlist.size(); ++i) {
            if(cutsize * 2 >= arr.size()) {
                return count;
            }
            cutsize += numlist[i];
            count++;
        }
    }
};
```

第一次提交无法通过，提示我编译出错，没有 ``return`` 。。。。。。于是在最后一个 ``for`` 循环外面加了一个 ``return count;`` 运行通过，但是效率么。。。。。低的吓人。

## 调优

看了看官方给出的代码，思路和我是一样的，但是性能却提高特别多。代码如下

```C++
class Solution {
public:
    int minSetSize(vector<int>& arr) {
        unordered_map<int, int> freq;
        for (int num: arr) {
            ++freq[num];
        }
        vector<int> occ;
        for (auto& [k, v]: freq) {
            occ.push_back(v);
        }
        sort(occ.begin(), occ.end(), greater<int>());
        int cnt = 0, ans = 0;
        for (int c: occ) {
            cnt += c;
            ans += 1;
            if (cnt * 2 >= arr.size()) {
                break;
            }
        }
        return ans;
    }
};
```

显然，肯定是 ``for`` 循环的用法带来的巨大提升。

经过测试，将原先的代码中更改为官方给出的样式，发现如下语句的更改性能提升巨大

```C++
for (auto& [k, v]: freq) {
    occ.push_back(v);
}
```

式子中的 ``&`` 可以去掉，因为这表示可以取出的 ``[k, v]`` 进行修改，但实际上我们不需要修改。

经分析，使用 ``i`` 来循环，每次都蕴藏了一个查询的过程，自然会消耗不少的资源。而利用这样的基于范围的 ``for`` ，就不存在这样的问题。经测试，将循环量从 ``i`` 替换成 ``map`` 的迭代器，也是同样层次的性能提升。

# 一些细节的总结

* 关于 ``sort()`` 
  *  ``#include <algorithm>`` 
  * 前两个参数是范围，第三个参数是排序方法
  * 排序方法可自定义，为 ``bool`` 的返回，两个输入
  * 已定义好的排序方法（记得后面加括号）
    *  ``equal_to<Type>``
    *  ``not_equal_to<Type>``
    *  ``greater<Type>``
    *  ``greater_equal<Type>``
    *  ``less<Type>``
    *  ``less_equal<Type>``
* 基于范围的 ``for`` 以后可以多用用
* 注意 ``map`` 中的查询，要遍历还是得依靠迭代器
*  ``auto`` 是个好东西，活用