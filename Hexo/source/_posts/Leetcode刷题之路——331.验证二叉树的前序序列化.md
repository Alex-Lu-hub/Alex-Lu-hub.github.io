---
title: Leetcode刷题之路——331. 验证二叉树的前序序列化
tags: [C++]
categories: [Leetcode]
date: 2021-3-12 14:36:00
updated: 2021-3-12 15:00:00
excerpt: Leetcode题库331. 验证二叉树的前序序列化
---

# 题目描述

序列化二叉树的一种方法是使用前序遍历。当我们遇到一个非空节点时，我们可以记录下这个节点的值。如果它是一个空节点，我们可以使用一个标记值记录，例如 ``#`` 。

```
     _9_
    /   \
   3     2
  / \   / \
 4   1  #  6
/ \ / \   / \
# # #
```

例如，上面的二叉树可以被序列化为字符串 ``"9,3,4,#,#,1,#,#,2,#,6,#,#"`` ，其中 ``#`` 代表一个空节点。

给定一串以逗号分隔的序列，验证它是否是正确的二叉树的前序序列化。编写一个在不重构树的条件下的可行算法。

每个以逗号分隔的字符或为一个整数或为一个表示 ``null`` 指针的 ``'#'`` 。

你可以认为输入格式总是有效的，例如它永远不会包含两个连续的逗号，比如 ``"1,,3"`` 。

示例 1:

```
输入: "9,3,4,#,#,1,#,#,2,#,6,#,#"
输出: true
```


示例 2:

```
输入: "1,#"
输出: false
```


示例 3:

```
输入: "9,#,#,1"
输出: false
```

# 题目分析

## 个人初尝试

比较直观的思路是用栈来计算，当进入左右孩子的下一层，就将父节点入栈。但是因为是一个字符串，我们需要手动记忆每一个节点的左右孩子访问情况。我是利用一个 ``flag`` 来记忆的，``0`` 代表左右孩子都没访问， ``1`` 代表访问了左孩子没访问右孩子， ``2`` 代表访问了左右孩子，需要将这个节点退出回到父节点查看下一步情况了。

考虑最后退出的情况，一定是遇到了 ``#`` 才会退出，否则会继续深入到其孩子节点去。所以，我们的 ``flag`` 仅在碰到了 ``#`` 才会增加，或者是有出栈情况才会把前一个节点的 ``flag`` 增加。按照这个想法，我们还需要一个栈来保存 ``flag`` 以便节点出栈时仍能获得其之前的 ``flag`` 情况。

考虑各栈的入栈出栈情况：当碰到数字时，表明其是孩子节点，需要将数字入栈，前一节点的 ``flag`` 入栈，为新节点的 ``flag`` 初始化为 ``0``；当碰到 ``#`` 号时， ``++flag`` ；如果 ``flag == 2`` ，表明这个节点需要退出了，数字出栈，同时需要获得前一节点的 ``flag`` 进行 ``+ 1`` 更新。

如此循环，最后如果是正确的序列，一定会使两个栈都为空，否则就不是正确序列。

具体代码如下：

```c++
class Solution {
public:
    bool isValidSerialization(string preorder) {
        stack<int> mystack;
        stack<int> flagstack;
        int flag = 0, len = preorder.size();
        for(int i = 0; i < len; ++i) {
            if(preorder[i] == ',')
                continue;
            else if(preorder[i] == '#') {
                ++flag;
            }
            else {
                int num = 0;
                while(preorder[i] >= '0' && preorder[i] <= '9') {
                    num = num * 10 + preorder[i] - '0';
                    ++i;
                }
                mystack.push(num);
                flagstack.push(flag);
                flag = 0;
            }
            while(flag == 2) {
                if(mystack.empty() || flagstack.empty()) {
                    return false;
                }
                mystack.pop();
                flag = flagstack.top() + 1;
                flagstack.pop();
            }
        }
        if(mystack.empty() && flagstack.empty()) {
            return true;
        }
        else {
            return false;
        }
    }
};
```

## 优化

查看题解，题解的思路和我的有些类似，但只用了一个栈。因为这个题目不关心二叉树的内容，只关心它的节点数目是否符合条件。所以可以只用一个栈，这个栈用来保存每个节点所剩余的槽位，也就是可以放至节点的位置。如果最后槽位不为 ``0`` ，说明树没有放满，不符合要求；否则则证明序列正确。

```c++

class Solution {
public:
    bool isValidSerialization(string preorder) {
        int n = preorder.length();
        int i = 0;
        stack<int> stk;
        stk.push(1);
        while (i < n) {
            if (stk.empty()) {
                return false;
            }
            if (preorder[i] == ',') {
                i++;
            } else if (preorder[i] == '#'){
                stk.top() -= 1;
                if (stk.top() == 0) {
                    stk.pop();
                }
                i++;
            } else {
                // 读一个数字
                while (i < n && preorder[i] != ',') {
                    i++;
                }
                stk.top() -= 1;
                if (stk.top() == 0) {
                    stk.pop();
                }
                stk.push(2);
            }
        }
        return stk.empty();
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/verify-preorder-serialization-of-a-binary-tree/solution/yan-zheng-er-cha-shu-de-qian-xu-xu-lie-h-jghn/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

还可以甚至连栈都不用，直接用一个数来统计整个树的槽位数目，更加节省空间。

具体代码如下：

```c++

class Solution {
public:
    bool isValidSerialization(string preorder) {
        int n = preorder.length();
        int i = 0;
        int slots = 1;
        while (i < n) {
            if (slots == 0) {
                return false;
            }
            if (preorder[i] == ',') {
                i++;
            } else if (preorder[i] == '#'){
                slots--;
                i++;
            } else {
                // 读一个数字
                while (i < n && preorder[i] != ',') {
                    i++;
                }
                slots++; // slots = slots - 1 + 2
            }
        }
        return slots == 0;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/verify-preorder-serialization-of-a-binary-tree/solution/yan-zheng-er-cha-shu-de-qian-xu-xu-lie-h-jghn/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

