# [游戏智商问题](https://icpc.upc.edu.cn/problem.php?id=5308)
## 题解

## 题目描述

熊大和熊二与吉吉国王决定进行一场智力比赛，以比拼谁能获得最多的水果美味值。他们有两种卡片（A 卡和 B 卡），可以通过这些卡片从位置 0 出发，跳跃到一系列格子上获得美味值。所有卡片必须使用完，并且最后必须跳到 **第 n 个格子**。

---

## 输入格式

- **第一行**：5 个整数  
  - `n`：格子的总数  
  - `na`：A 卡片数量  
  - `nb`：B 卡片数量  
  - `va`：A 卡片的跳跃步长  
  - `vb`：B 卡片的跳跃步长  
  **保证**：`n = na * va + nb * vb`  
  即卡片的数量和步长总能刚好使他们到达最后一个格子。

- **接下来 n 个整数**：依次表示每个格子上的美味值（有可能为负数）。

---

## 输出格式

- 输出一个整数：表示使用所有卡片后所能获得的最大美味值。

---

## 样例输入

```
3 1 1 2 1
1 2 3
```

## 样例输出

```
5
```

---

## 题目分析

- **目标**：  
  - 使用所有的卡片，跳跃至第 n 个格子，求最大美味值总和。
  
- **解法思路**：  
  使用 **动态规划** 解决这个问题。我们通过枚举每种卡片的使用情况，逐步更新最大收益。

---

## 状态定义

- `dp[a][b]`：使用了 `a` 张 A 卡和 `b` 张 B 卡时，能够获得的最大美味值。

---

## 状态转移方程

- 如果使用 **A 卡**：  
  ```
  dp[a][b] = max(dp[a][b], dp[a-1][b] + value[a * va + b * vb])
  ```
  条件：`a > 0` 且跳跃位置合法。

- 如果使用 **B 卡**：  
  ```
  dp[a][b] = max(dp[a][b], dp[a][b-1] + value[a * va + b * vb])
  ```
  条件：`b > 0` 且跳跃位置合法。

---

## 代码实现

```cpp
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

int main() {
    cin.tie(nullptr);
    ios::sync_with_stdio(false);

    int n, na, nb, va, vb;
    cin >> n >> na >> nb >> va >> vb;

    // 初始化 DP 数组，DP[a][b] 表示使用了 a 张 A 卡和 b 张 B 卡的最大美味值。
    vector<vector<int>> DP(na + 1, vector<int>(nb + 1, 0));

    // 读取每个格子上的美味值
    vector<int> value(n + 1, 0);
    int t = n;
    while (t--) {
        cin >> value[n - t];  // 从 1 到 n 依次存储美味值
    }

    // 动态规划求解
    for (int a = 0; a <= na; a++) {
        for (int b = 0; b <= nb; b++) {
            if (a > 0 && a * va + b * vb <= n) {
                DP[a][b] = max(DP[a][b], DP[a - 1][b] + value[a * va + b * vb]);
            }
            if (b > 0 && a * va + b * vb <= n) {
                DP[a][b] = max(DP[a][b], DP[a][b - 1] + value[a * va + b * vb]);
            }
        }
    }

    // 输出结果
    cout << DP[na][nb] << endl;

    return 0;
}
```

---

## 代码解释

1. **初始化输入和 DP 数组**：  
   使用二维数组 `DP[a][b]` 来存储使用了 `a` 张 A 卡和 `b` 张 B 卡后的最大美味值。

2. **读取美味值**：  
   将每个格子的美味值存入 `value` 数组，`value[i]` 表示第 `i` 个格子的美味值。

3. **动态规划求解**：  
   - 遍历所有可能的 `a` 和 `b` 值。
   - 每次使用一张 A 或 B 卡，更新最大收益。

4. **输出结果**：  
   打印使用所有卡片后的最大美味值。

---

## 时间复杂度分析

- **时间复杂度**：`O(na * nb)`，其中 `na` 和 `nb` 是 A 和 B 卡片的数量。  
- **空间复杂度**：`O(na * nb)`，用于存储 DP 数组。

---

## 样例分析

对于输入：
```
3 1 1 2 1
1 2 3
```

- 使用 1 张 A 卡跳 2 个格子，到达位置 2，获得美味值 2。
- 使用 1 张 B 卡跳 1 个格子，到达位置 3，获得美味值 3。

最终获得的总美味值为 `2 + 3 = 5`。

---

## 总结

这道题考察了 **动态规划** 的应用，特别是背包问题的变种。需要我们合理规划卡片的使用顺序，才能获得最大美味值。通过 DP 数组存储中间状态，我们能够高效地求解这个问题。