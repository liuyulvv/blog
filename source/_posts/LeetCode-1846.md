---
title: LeetCode-1846
date: 2021-07-15 00:16:51
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-1846,减小和重新排列数组后的最大元素
---

### 解法

```cpp
class Solution
{

public:
    int maximumElementAfterDecrementingAndRearranging(vector<int> &arr)
    {
        sort(arr.begin(), arr.end());
        arr[0] = 1;
        for (int i = 1; i < arr.size(); ++i)
        {
            if (arr[i] - arr[i - 1] > 1)
            {
                arr[i] = arr[i - 1] + 1;
            }
        }
        return arr.back();
    }
};
```