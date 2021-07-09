---
title: LeetCode-面试题-17.10
date: 2021-07-09 11:07:59
categories: [Leetcode]
tags: [LeetCode,C++]
description: LeetCode面试题17.10,主要元素
---

### 解法

```cpp
class Solution
{

public:
    int majorityElement(vector<int> &nums)
    {
        int major = 0, count = 0;
        for (auto &num : nums)
        {
            if (count == 0)
                major = num;
            if (major == num)
                ++count;
            else
                --count;
        }
        int mayCount = 0;
        for (auto &num : nums)
            if (num == major)
                ++mayCount;
        if (mayCount > nums.size() / 2)
            return major;
        else
            return -1;
    }
};
```

### 收获

本题可使用哈希表求解，由于题目要求空间复杂度O(n)，时间复杂度O(1)，因此，只能使用Boyer-Moore投票算法。

该算法的流程如下：

1. 任选一个值major作为候选的主要元素，候选的主要元素出现的次数count
2. 遍历数组，对于元素num：
    > 1. 如果count为0，则num赋值给major
    > 2. 如果major和num相等，count+=1，否则count-=1
3. 再次遍历数组，统计major出现的次数，如果次数大于数组长度的一半，则为主要元素，否则，没有主要元素