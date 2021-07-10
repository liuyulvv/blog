---
title: LeetCode-981
date: 2021-07-10 11:57:32
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-981,基于时间的键值存储
---

### 解法

```cpp
class TimeMap
{

private:
    struct VT
    {
        vector<string> value;
        vector<int> timestamp;
    };

    unordered_map<string, VT> um;

public:
    TimeMap()
    {
    }

    void set(string key, string value, int timestamp)
    {
        auto it = um.find(key);
        if (um.end() == it)
        {
            VT vt;
            vt.value.push_back(value);
            vt.timestamp.push_back(timestamp);
            um[key] = vt;
        }
        else
        {
            it->second.value.push_back(value);
            it->second.timestamp.push_back(timestamp);
        }
    }

    string get(string key, int timestamp)
    {
        auto it = um.find(key);
        if (um.end() == it)
        {
            return "";
        }
        else
        {
            unsigned right = it->second.value.size(), left = 0, center = (right + left) / 2;
            while (left < right - 1)
            {
                if (it->second.timestamp[center] == timestamp)
                {
                    return it->second.value[center];
                }
                else if (it->second.timestamp[center] > timestamp)
                {
                    right = center;
                    center = (right + left) / 2;
                }
                else
                {
                    left = center;
                    center = (right + left) / 2;
                }
            }
            if (it->second.timestamp[left] <= timestamp)
            {
                return it->second.value[left];
            }
            return "";
        }
    }
};
```

### 收获

利用进行set操作的timestamp严格递增的特性，使用二分搜索。