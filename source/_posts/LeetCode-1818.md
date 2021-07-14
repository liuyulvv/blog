---
title: LeetCode-1818
date: 2021-07-14 22:41:32
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-1818,绝对差值和
---

### 解法

```cpp
class Solution
{

public:
    int minAbsoluteSumDiff(vector<int> &nums1, vector<int> &nums2)
    {
        decltype(nums1.size()) index = 0, n = nums1.size();
        long long res = 0, maxn = 0;
        vector<int> rec(nums1);
        sort(rec.begin(), rec.end());
        for (; index < n; ++index)
        {
            long long cur = abs(nums1[index] - nums2[index]);
            res += cur;
            auto j = lower_bound(rec.begin(), rec.end(), nums2[index]) - rec.begin();
            if (j < n)
            {
                maxn = max(maxn, cur - (rec[j] - nums2[index]));
            }
            if (j > 0)
            {
                maxn = max(maxn, cur - (nums2[index] - rec[j - 1]));
            }
        }
        return (res - maxn) % 1000000007;
    }
};
```