---
title: Leetcode刷题之路——706. 设计哈希映射
tags: [C++]
categories: [Leetcode]
date: 2021-3-14 13:10:00
updated: 2021-3-14 13:15:00
excerpt: Leetcode题库706. 设计哈希映射
---

# 题目描述

不使用任何内建的哈希表库设计一个哈希映射（HashMap）。

实现 ``MyHashMap`` 类：

*  ``MyHashMap()`` 用空映射初始化对象
*  ``void put(int key, int value)`` 向 HashMap 插入一个键值对 ``(key, value)`` 。如果 ``key`` 已经存在于映射中，则更新其对应的值 ``value`` 。
*  ``int get(int key)`` 返回特定的 ``key`` 所映射的 ``value`` ；如果映射中不包含 ``key`` 的映射，返回 ``-1`` 。
*  ``void remove(key)`` 如果映射中存在 ``key`` 的映射，则移除 ``key`` 和它所对应的 ``value`` 。


示例：

```
输入：
["MyHashMap", "put", "put", "get", "get", "put", "get", "remove", "get"]
[[], [1, 1], [2, 2], [1], [3], [2, 1], [2], [2], [2]]
输出：
[null, null, null, 1, -1, null, 1, null, -1]

解释：
MyHashMap myHashMap = new MyHashMap();
myHashMap.put(1, 1); // myHashMap 现在为 [[1,1]]
myHashMap.put(2, 2); // myHashMap 现在为 [[1,1], [2,2]]
myHashMap.get(1);    // 返回 1 ，myHashMap 现在为 [[1,1], [2,2]]
myHashMap.get(3);    // 返回 -1（未找到），myHashMap 现在为 [[1,1], [2,2]]
myHashMap.put(2, 1); // myHashMap 现在为 [[1,1], [2,1]]（更新已有的值）
myHashMap.get(2);    // 返回 1 ，myHashMap 现在为 [[1,1], [2,1]]
myHashMap.remove(2); // 删除键为 2 的数据，myHashMap 现在为 [[1,1]]
myHashMap.get(2);    // 返回 -1（未找到），myHashMap 现在为 [[1,1]]
```


提示：

*  ``0 <= key, value <= 106``
*  最多调用 ``10^4`` 次 ``put`` 、 ``get`` 和 ``remove`` 方法

进阶：你能否不使用内置的 HashMap 库解决此问题？

# 题目分析

## 个人初尝试

 ``unordered_map`` 解君愁~

具体代码如下：

```c++
class MyHashMap {
public:
    unordered_map<int, int> myhash;
    
    /** Initialize your data structure here. */
    MyHashMap() {
        
    }
    
    /** value will always be non-negative. */
    void put(int key, int value) {
        if(myhash.find(key) != myhash.end()) {
            myhash.erase(key);
        }
        myhash.insert(pair(key, value));
    }
    
    /** Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key */
    int get(int key) {
        if(myhash.find(key) != myhash.end()) {
            return myhash[key];
        }
        else {
            return -1;
        }

    }
    
    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    void remove(int key) {
        myhash.erase(key);
    }
};

/**
 * Your MyHashMap object will be instantiated and called as such:
 * MyHashMap* obj = new MyHashMap();
 * obj->put(key,value);
 * int param_2 = obj->get(key);
 * obj->remove(key);
 */
```

## 链地址法

设哈希表的大小为 ``base`` ，则可以设计一个简单的哈希函数：$$\text{hash}(x) = x \bmod \textit{base}$$ 

我们开辟一个大小为 ``base`` 的数组，数组的每个位置是一个链表。当计算出哈希值之后，就插入到对应位置的链表当中。

由于我们使用整数除法作为哈希函数，为了尽可能避免冲突，应当将 ``base`` 取为一个质数。在这里，我们取 ``base=769`` 。

具体代码如下：

```c++
class MyHashMap {
private:
    vector<list<pair<int, int>>> data;
    static const int base = 769;
    static int hash(int key) {
        return key % base;
    }
public:
    /** Initialize your data structure here. */
    MyHashMap(): data(base) {}
    
    /** value will always be non-negative. */
    void put(int key, int value) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it).first == key) {
                (*it).second = value;
                return;
            }
        }
        data[h].push_back(make_pair(key, value));
    }
    
    /** Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key */
    int get(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it).first == key) {
                return (*it).second;
            }
        }
        return -1;
    }
    
    /** Removes the mapping of the specified value key if this map contains a mapping for the key */
    void remove(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it).first == key) {
                data[h].erase(it);
                return;
            }
        }
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/design-hashmap/solution/she-ji-ha-xi-ying-she-by-leetcode-soluti-klu9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```