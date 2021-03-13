---
title: Leetcode刷题之路——705. 设计哈希集合
tags: [C++]
categories: [Leetcode]
date: 2021-3-13 20:26:00
updated: 2021-3-12 20:51:00
excerpt: Leetcode题库705. 设计哈希集合

---

# 题目描述

不使用任何内建的哈希表库设计一个哈希集合（HashSet）。

实现 ``MyHashSet`` 类：

*  ``void add(key)`` 向哈希集合中插入值 ``key`` 。
*  ``bool contains(key)`` 返回哈希集合中是否存在这个值 ``key`` 。
*  ``void remove(key)`` 将给定值 ``key`` 从哈希集合中删除。如果哈希集合中没有这个值，什么也不做。

示例：

```
输入：
["MyHashSet", "add", "add", "contains", "contains", "add", "contains", "remove", "contains"]
[[], [1], [2], [1], [3], [2], [2], [2], [2]]
输出：
[null, null, null, true, false, null, true, null, false]

解释：
MyHashSet myHashSet = new MyHashSet();
myHashSet.add(1);      // set = [1]
myHashSet.add(2);      // set = [1, 2]
myHashSet.contains(1); // 返回 True
myHashSet.contains(3); // 返回 False ，（未找到）
myHashSet.add(2);      // set = [1, 2]
myHashSet.contains(2); // 返回 True
myHashSet.remove(2);   // set = [1]
myHashSet.contains(2); // 返回 False ，（已移除）
```


提示：

*  ``0 <= key <= 106`` 
* 最多调用 ``10^4`` 次 ``add`` 、 ``remove`` 和 ``contains`` 。


进阶：你可以不使用内建的哈希集合库解决此问题吗？

# 题目分析

## 个人初尝试

对于哈希的具体实现我自己不是特别了解，但是我知道 ``unordered_map`` 底层是一个哈希表，就用这个来实现了。

具体代码如下：

```c++
class MyHashSet {
public:
    unordered_map<int, int> myhash;
    
    /** Initialize your data structure here. */
    MyHashSet() {
    }
    
    void add(int key) {
        myhash.insert(pair(key, 1));
    }
    
    void remove(int key) {
        myhash.erase(key);
    }
    
    /** Returns true if this set contains the specified element */
    bool contains(int key) {
        if(myhash.find(key) != myhash.end()) {
            return true;
        }
        else {
            return false;
        }
    }
};

/**
 * Your MyHashSet object will be instantiated and called as such:
 * MyHashSet* obj = new MyHashSet();
 * obj->add(key);
 * obj->remove(key);
 * bool param_3 = obj->contains(key);
 */
```

通过肯定是没问题了，性能还可以。但是还是要学习哈希表的实现。看题解！

## 链地址法

设哈希表的大小为 ``base`` ，则可以设计一个简单的哈希函数：$$\text{hash}(x) = x \bmod \textit{base}$$ 

我们开辟一个大小为 ``base`` 的数组，数组的每个位置是一个链表。当计算出哈希值之后，就插入到对应位置的链表当中。

由于我们使用整数除法作为哈希函数，为了尽可能避免冲突，应当将 ``base`` 取为一个质数。在这里，我们取 ``base=769`` 。

具体代码如下：

```c++
class MyHashSet {
private:
    vector<list<int>> data;
    static const int base = 769;
    static int hash(int key) {
        return key % base;
    }
public:
    /** Initialize your data structure here. */
    MyHashSet(): data(base) {}
    
    void add(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                return;
            }
        }
        data[h].push_back(key);
    }
    
    void remove(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                data[h].erase(it);
                return;
            }
        }
    }
    
    /** Returns true if this set contains the specified element */
    bool contains(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                return true;
            }
        }
        return false;
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/design-hashset/solution/she-ji-ha-xi-ji-he-by-leetcode-solution-xp4t/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
