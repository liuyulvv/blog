---
title: LeetCode-930
date: 2021-07-08 10:17:39
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-930,和相同的二元子数组
---

### 解法

1. 哈希表

```cpp
class Solution
{

public:
    int numSubarraysWithSum(vector<int> &nums, int goal)
    {
        unordered_map<int, int> map_sum;
        int sum = 0, res = 0;
        for (const auto &num : nums)
        {
            ++map_sum[sum];
            sum += num;
            res += map_sum[sum - goal];
        }
        return res;
    }
};
```

2. 滑动窗口

学习中。

### 收获

哈希表解法for循环内的语句顺序较关键。