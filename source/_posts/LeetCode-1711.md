---
title: LeetCode-1711
date: 2021-07-07 14:48:22
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-1711,大餐计数
---

### 解法

```cpp
class Solution
{

public:
    int countPairs(vector<int> &deliciousness)
    {
        vector<int> vecInt;
        unsigned long long res = 0;
        for (int i = 0; i <= 21; ++i)
        {
            vecInt.push_back(pow(2, i));
        }
        map<int, unsigned long long> mapInt;
        for (auto &i : deliciousness)
        {
            ++mapInt[i];
        }
        for (const auto &[key, value] : mapInt)
        {
            for (const auto &num : vecInt)
            {
                if (num - key >= key)
                {
                    auto it = mapInt.find(num - key);
                    if (mapInt.end() != it)
                    {
                        if (key == num - key)
                        {
                            res += value * (value - 1) / 2;
                        }
                        else
                        {
                            res += value * it->second;
                        }
                    }
                }
            }
        }
        return res % (1000000007);
    }
};
```

### 收获

```cpp
// map的键值对遍历
for (const auto &[key, value] : mapInt)
```

解法中值得注意的是用到了组合计数原理，同时为了防止溢出，采用了unsigned long long。