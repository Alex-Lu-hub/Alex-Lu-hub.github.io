---
title: Leetcode刷题之路——354. 俄罗斯套娃信封问题
tags: [C++]
categories: [Leetcode]
date: 2021-3-4 14:56:00
updated: 2021-3-4 15:14:00
excerpt: Leetcode题库354. 俄罗斯套娃信封问题
---

# 题目描述

给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 ``(w, h)`` 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

说明:
不允许旋转信封。

示例:

```
输入: envelopes = [[5,4],[6,4],[6,7],[2,3]]
输出: 3 
解释: 最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。
```

# 题目分析

## 个人初尝试

最开始的思路是采用滑窗，将一个信封的设定为窗口的下限和上限，再遍历所有的信封，当有符合上下限变动条件，即可以被装下或者装下别的信封时，更新上下限，并计数+1。如此对每一个信封进行这样的操作，就可以得到最大的信封数了。

具体代码如下：

```c++
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        int max = 1;
        if(envelopes.size() != 0) {
            int size = envelopes.size();
            for(int j = 0; j < size; j++) {
                int tmp = 1;
                vector<int> minenv = envelopes[j];
                vector<int> maxenv = envelopes[j];
                for(int i = 0; i < size; ++i) {
                    if(envelopes[i][0] > maxenv[0] && envelopes[i][1] > maxenv[1]) {
                        maxenv = envelopes[i];
                        ++tmp;
                    }
                    if(envelopes[i][0] < minenv[0] && envelopes[i][1] < minenv[1]) {
                        minenv = envelopes[i];
                        ++tmp;
                    }
                }
                if(tmp > max) {
                    max = tmp;
                }
            }
        }
        return max;
    }
};
```

但这个算法并不能成功，有一个明显的问题开始时没有想到。那就是当在最开始的时候就碰到了最小的或者最大的信封，即使后面遍历到了可以装下去的，也不会再计数了。。。。。。一时半会没有想到好的解决方案，看题解！

## 动态规划

按照题解的思路，这个题目本质是要找到最长严格递增子序列，二维的不好同时考虑，就控制一维，再处理另外一维。为了避免在前一维相同的情况下，导致第二维最长子序列求出来不符合要求，所以在排序的时候可以将第一维升序，第二维降序排列，避免出现例外情况。具体可参考 [官方题解](https://leetcode-cn.com/problems/russian-doll-envelopes/solution/e-luo-si-tao-wa-xin-feng-wen-ti-by-leetc-wj68/)

具体代码如下

```c++
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        if(envelopes.size() == 0) {
            return 0;
        }

        int size = envelopes.size();
        
        sort(envelopes.begin(), envelopes.end(), [](const auto& a, const auto& b) {
        if(a[0] == b[0]) {
            return a[1] > b[1];
        }
        else {
            return a[0] < b[0];
        }
    });
        
        vector<int> fmax(size, 1);
        for(int i = 1; i < size; ++i) {
            for(int j = 0; j < i; ++j) {
                if(envelopes[j][1] < envelopes[i][1]) {
                    fmax[i] = max(fmax[i], fmax[j] + 1);
                }
            }
        }
        return *max_element(fmax.begin(), fmax.end());
    }
};
```

# 一些细节的总结

* sort的用法比较灵活，可以手撸自己的排序规则，并直接写在第三个参数中
* C++中的 ``&`` 指示引用，不可和C中的取地址符弄混！引用的使用用法需要进行后续总结

