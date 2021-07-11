---
title: LeetCode-274
date: 2021-07-12 00:35:33
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-274,H指数
---

### 解法

```cpp
class Solution
{

public:
    int hIndex(vector<int> &citations)
    {
        sort(citations.begin(), citations.end());
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