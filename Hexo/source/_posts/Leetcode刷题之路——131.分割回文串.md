---
title: Leetcode刷题之路——131.分割回文串
tags: [C++]
categories: [Leetcode]
date: 2021-3-7 19:30:00
updated: 2021-3-7 21:35:00
excerpt: Leetcode题库131.分割回文串

---

# 题目描述

给你一个字符串 ``s`` ，请你将 ``s`` 分割成一些子串，使每个子串都是 **回文串** 。返回 ``s`` 所有可能的分割方案。

 **回文串** 是正着读和反着读都一样的字符串。

示例 1：

```
输入：s = "aab"
输出：[["a","a","b"],["aa","b"]]
```

示例 2：

```
输入：s = "a"
输出：[["a"]]
```


提示：

*  ``1 <= s.length <= 16`` 
*  ``s`` 仅由小写英文字母组成

# 题目分析

## 回溯法

泻药，今天这个题目，要是单独找回文串还是挺简单的，但是要找到全部的子回文串我确实没啥特别好的办法。。。。。。遂看题解，了解了回溯法。此处推荐 [负雪明烛的题解](https://leetcode-cn.com/problems/palindrome-partitioning/solution/hui-su-fa-si-lu-yu-mo-ban-by-fuxuemingzh-azhz/) ，个人觉得讲得挺清晰的！

回溯法的整体思路是：搜索每一条路，每次回溯是对具体的一条路径而言的。对当前搜索路径下的的未探索区域进行搜索，则可能有两种情况：

* 当前未搜索区域满足结束条件，则保存当前路径并退出当前搜索；
* 当前未搜索区域需要继续搜索，则遍历当前所有可能的选择：如果该选择符合要求，则把当前选择加入当前的搜索路径中，并继续搜索新的未探索区域。

一个回溯法的模板如下

```
res = []
path = []

def backtrack(未探索区域, res, path):
    if 未探索区域满足结束条件:
        res.add(path) # 深度拷贝
        return
    for 选择 in 未探索区域当前可能的选择:
        if 当前选择符合要求:
            path.add(当前选择)
            backtrack(新的未探索区域, res, path)
            path.pop()

作者：fuxuemingzhu
链接：https://leetcode-cn.com/problems/palindrome-partitioning/solution/hui-su-fa-si-lu-yu-mo-ban-by-fuxuemingzh-azhz/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

根据回溯法的思路，具体代码如下：

```c++
class Solution {
public:
    bool juggement(string s) {
        int left = 0, right = s.size() - 1;
        while(left <= right) {
            if(s[left] != s[right]) {
                return false;
            }
            ++left;
            --right;
        }
        return true;
    }

    void traceback(string s, vector<vector<string>>& ans, vector<string> path) {
        if (s.size() == 0) {
            ans.push_back(path);
            return;
        } 
        for(int i = 1; i <= s.size(); ++i) {
            string pre = s.substr(0, i);
            if(juggement(pre)) {
                path.push_back(pre);
                traceback(s.substr(i), ans, path);
                path.pop_back();
            }
        }
    } 
    
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        traceback(s, ans, {});
        return ans;
    }
};
```

## 动态规划的回文串判断方法

使用一个二维的数组 ``f[i, j]`` 来表示字符串中从 ``i`` 到 ``j`` 是否为回文串。如果是的话该值为 ``1`` ，否则为 ``0`` 。那么就可以得出一个判断的状态转移方程。

要想 ``f[i, j]`` 是回文串，需要对应的 ``i`` 、 ``j`` 处字符相等，且 ``f[i + 1, j - 1]`` 是回文串。

再来考虑初始化的情况。哪些我们可以显然判断为回文串呢？字符串长度为 ``1`` 的，即 ``i == j`` 的就可以。而对于空串，即 ``i > j`` 的情况，我们无所谓赋 ``0`` 或是 ``1`` ，因为我们在后面判断中肯定不会判断这样的情况。但是在分析了 [这个特例](https://leetcode-cn.com/problems/palindromic-substrings/solution/647-hui-wen-zi-chuan-dong-tai-gui-hua-fang-shi-qiu/) 后，发现赋值为 ``1`` 的话就可以解决这个特例的问题。那么在建立这个二维数组时，只需要把上述的两种情况赋好值即可。

接下来的遍历过程，可以画一个表来判断一下

| 纵i横j |  a   |  b   |  a   |
| :----: | :--: | :--: | :--: |
|   a    |  1   |      |      |
|   b    |  1   |  1   |      |
|   a    |  1   |  1   |  1   |

可以看出来，一个值得判断取决于其左下方的值（即 ``[i + 1, j - 1]``），那么我们从右下角开始遍历，就可以避免和未初始化的数据判断了。

具体代码如下：

```c++
void preprocess(string s) {
        n = s.size();
        f.assign(n, vector<int>(n, true));

        for (int i = n - 1; i >= 0; --i) {
            for (int j = i + 1; j < n; ++j) {
                f[i][j] = (s[i] == s[j]) && f[i + 1][j - 1];
            }
        }
}
```