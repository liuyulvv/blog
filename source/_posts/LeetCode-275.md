---
title: LeetCode-275
date: 2021-07-12 10:58:48
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-274,H指数II
---

### 解法

```cpp
class Solution
{

public:
    int hIndex(vector<int> &citations)
    {
        auto s = citations.size(), h = s;
        while (h > 0)
        {
            if (citations[s - h] >= h)
            {
                return h;
            }
            else
            {
                --h;
            }
        }
        return 0;
    }
};
```
