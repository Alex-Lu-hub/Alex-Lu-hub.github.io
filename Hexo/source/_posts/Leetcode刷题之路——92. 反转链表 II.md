---
title: Leetcode刷题之路——92. 反转链表 II
tags: [C++]
categories: [Leetcode]
date: 2021-3-18 11:08:00
updated: 2021-3-17 11:25:00
excerpt: Leetcode题库92. 反转链表 II
---

# 题目描述

给你单链表的头节点 ``head`` 和两个整数 ``left`` 和 ``right`` ，其中 ``left <= right`` 。请你反转从位置 ``left`` 到位置 ``right`` 的链表节点，返回 **反转后的链表** 。


示例 1：

```
输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
```


示例 2：

```
输入：head = [5], left = 1, right = 1
输出：[5]
```


提示：

* 链表中节点数目为 ``n`` 
*  ``1 <= n <= 500`` 
*  ``-500 <= Node.val <= 500`` 
*  ``1 <= left <= right <= n`` 


进阶： 你可以使用一趟扫描完成反转吗？

# 题目分析

## 个人初尝试

一个直接的思路是利用栈来实现这个反转，遍历到 ``left`` 所在节点，记录该节点，然后将 ``left`` 到 ``right`` 的值全部入栈。再从 ``left`` 节点开始重新出栈赋值给各个节点即可。

具体代码如下：

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        stack<int> mystack;
        ListNode* pre = head;
        for (int i = 0; i < left - 1; ++i) {
            pre = pre->next;
        }
        ListNode* leftnode = pre;
        for (int i = left; i <= right; ++i) {
            mystack.push(pre->val);
            pre = pre->next;
        }
        for (int i = left; i <= right; ++i) {
            leftnode->val = mystack.top();
            mystack.pop();
            leftnode = leftnode->next;
        }
        return head;
    }
};
```

顺利AC，时间还不错，但是空间使用不理想。看看题解咋做的。

## 穿针引线

 [官方题解](https://leetcode-cn.com/problems/reverse-linked-list-ii/solution/fan-zhuan-lian-biao-ii-by-leetcode-solut-teyq/) 写的很详尽，还有配图，可以参看。

具体的代码如下：

```c++
class Solution {
private:
    void reverseLinkedList(ListNode *head) {
        // 也可以使用递归反转一个链表
        ListNode *pre = nullptr;
        ListNode *cur = head;

        while (cur != nullptr) {
            ListNode *next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
    }

public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        // 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;

        ListNode *pre = dummyNode;
        // 第 1 步：从虚拟头节点走 left - 1 步，来到 left 节点的前一个节点
        // 建议写在 for 循环里，语义清晰
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }

        // 第 2 步：从 pre 再走 right - left + 1 步，来到 right 节点
        ListNode *rightNode = pre;
        for (int i = 0; i < right - left + 1; i++) {
            rightNode = rightNode->next;
        }

        // 第 3 步：切断出一个子链表（截取链表）
        ListNode *leftNode = pre->next;
        ListNode *curr = rightNode->next;

        // 注意：切断链接
        pre->next = nullptr;
        rightNode->next = nullptr;

        // 第 4 步：同第 206 题，反转链表的子区间
        reverseLinkedList(leftNode);

        // 第 5 步：接回到原来的链表中
        pre->next = rightNode;
        leftNode->next = curr;
        return dummyNode->next;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii/solution/fan-zhuan-lian-biao-ii-by-leetcode-solut-teyq/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

以及进阶版的穿针引线

```c++
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        // 设置 dummyNode 是这一类问题的一般做法
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode *pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }
        ListNode *cur = pre->next;
        ListNode *next;
        for (int i = 0; i < right - left; i++) {
            next = cur->next;
            cur->next = next->next;
            next->next = pre->next;
            pre->next = next;
        }
        return dummyNode->next;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii/solution/fan-zhuan-lian-biao-ii-by-leetcode-solut-teyq/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 一些细节的总结

* 穿针引线原理其实很简单，多画画图理清操作顺序就没有问题了！