---
title: Leetcode刷题之路——1047. 删除字符串中的所有相邻重复项
tags: [C++]
categories: [Leetcode]
date: 2021-3-9 19:21:00
updated: 2021-3-9 19:57:00
excerpt: Leetcode题库1047. 删除字符串中的所有相邻重复项
---

# 题目描述

给出由小写字母组成的字符串 ``S`` ， **重复项删除操作** 会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

 

示例：

```
输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```


提示：

*  ``1 <= S.length <= 20000`` 
*  ``S`` 仅由小写英文字母组成。

# 题目分析

## 个人初尝试

自己的一个比较直观的想法，是用递归来实现这个删除，删除掉第一对重复字符后，将新的字符串再送入这个函数递归，直到遍历完没有重复的字符返回。

具体代码如下：

```c++
class Solution {
public:
    string removeDuplicates(string S) {
        if(S.empty()){
            return S;
        }
        int len = S.size();
        for(int i = 1; i < len; ++i) {
            if(i + 1 > S.size()) {
                return S;
            }
            if(S[i] == S[i - 1]) {
                S.erase(i - 1, 2);
                S = removeDuplicates(S);
            }
        }
        return S;
    }
};
```

在这个代码的测试过程中有碰到两个问题。

第一个，最后得到的 ``S`` 只是第一次删除后的，并没有后续的变化。经过分析是因为我每次传到下一层递归的 ``S`` ，在返回上一层后就被销毁了，没有被保留下来。所以需要在递归处加上 ``S = `` 把返回的S保留，才能得到正确的结果。

第二个，编译器提示我有越界操作，我直接问号？！理论上逻辑分析还有自己手画递归过程都没有问题。于是进行了 ``cout`` debug大法，最后终于确定了。 ``S.size()`` 返回的是一个无符号数，当为 ``0`` 后进行 `` - 1`` 操作后，会得到一个巨大的数！导致退出递归的逻辑无法触发。将判断从 ``i > S.size() - 1`` 改成 ``i + 1 > S.size()`` 问题解决。

AC是能AC了，但是性能极差。尝试调优，在lpy和露露的提醒下想到了栈，用栈来解决这个问题。

## 栈

栈是先入后出的嘛。那么就一次让原字符串入栈，当碰到要入的和栈顶的元素相同时，就把栈顶元素出栈。最后再把栈内元素给一个新的字符串即可，依次赋值后记得要转置一下，用 ``reverse(str.begin(), str.end())`` 即可。

具体代码如下：

```c++
class Solution {
public:
    string removeDuplicates(string S) {
        stack<char> mystack;
        int len = S.size();
        for(int i = 0; i < len; ++i) {
            if(mystack.empty() || S[i] != mystack.top()) {
                mystack.push(S[i]);
            }
            else {
                mystack.pop();
            }
        }
        string ans;
        while(!mystack.empty()) {
            ans.push_back(mystack.top());
            mystack.pop();
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

看了一下官方题解，发现可以直接把字符串当栈用了，就避免了中间新建一个栈的麻烦。

具体代码如下：

```c++
class Solution {
public:
    string removeDuplicates(string S) {
        string stk;
        for (char ch : S) {
            if (!stk.empty() && stk.back() == ch) {
                stk.pop_back();
            } else {
                stk.push_back(ch);
            }
        }
        return stk;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/solution/shan-chu-zi-fu-chuan-zhong-de-suo-you-xi-4ohr/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 一些细节的总结

* 要注意在不同函数中的变量 **形参** 、 **实参** 的关系以及存活时间，像这个题目中就是我在递归使用时没有注意到，花费了不少debug时间
* 另外对于变量的类型不确定的时候，到涉及 ``0`` 还要 ``- 1`` 的操作还是把 ``-`` 换成 ``+`` 更为合理

*  ``print/cout`` 是世界上最好的调试方法！！！（大雾）