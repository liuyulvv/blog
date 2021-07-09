---
title: LeetCode-1418
date: 2021-07-07 14:48:43
categories: [LeetCode]
tags: [LeetCode,C++]
description: LeetCode-1418,点菜展示表
---

### 解法

```cpp
class Solution
{

private:
    map<int, unordered_map<string, int>> foodCount;
    set<string> foodName;

public:
    vector<vector<string>> displayTable(vector<vector<string>> &orders)
    {
        vector<vector<string>> res;

        for (auto &order : orders)
        {
            foodName.insert(order[2]);
            ++foodCount[stoi(order[1])][order[2]];
        }

        vector<string> head{"Table"};

        for (auto &name : foodName)
        {
            head.push_back(name);
        }

        res.push_back(head);

        for (auto i = foodCount.begin(); i != foodCount.end(); ++i)
        {
            vector<string> vstr{to_string(i->first)};

            for (auto &name : foodName)
            {
                vstr.push_back(to_string(i->second[name]));
            }

            res.push_back(vstr);
        }
        return res;
    }
};
```

### 收获

```cpp
// 新建map
map<string,int> temp_map;

// 添加
++temp_map["temp"]; //temp_map["temp"]取得的值是0，因此该操作的结果是temp_map["temp"]变为1
```

```cpp
#include <iostream>
#include <map>
#include <string>

using namespace std;

int main()
{
    map<string, int> temp_int_map;
    cout << (temp_int_map["temp"] == 0) << endl;        // 1
    map<string, string> temp_string_map;
    cout << (temp_string_map["temp"] == "") << endl;    // 1
    map<string, double> temp_double_map;
    cout << (temp_double_map["temp"] == 0) << endl;     // 1
    map<string, char> temp_char_map;
    cout << (temp_char_map["temp"] == '\0') << endl;    // 1
    return 0;
}
```

在map用不存在的key获取value时，返回的value是符合直觉的值，0或空。