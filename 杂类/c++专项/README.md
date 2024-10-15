# practice



[**题目:数字三角**](https://icpc.upc.edu.cn/problem.php?cid=2982&pid=0)

好的，让我们来优化你的代码排版并进行一些解释，以使其更加清晰易读。

### 数字三角问题的解法

**问题描述：**  
给定一个数字三角形，计算从顶点到底边的最大路径和。

### 初始DFS解法

下面是使用深度优先搜索（DFS）来解决该问题的代码。尽管这个方法在直观上简单，但由于递归深度可能会导致超时或栈溢出，特别是当`n`较大时。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

// DFS 方法
int dfs(const vector<vector<int>> &vec, int row, int col) {
    // 边界条件：越界返回 0
    if (row < 0 || col < 0 || col > vec.size() - row) {
        return 0;
    }
    // 到达底层，返回当前值
    if (row == 0) {
        return vec[row][col];
    }
    
    // 递归调用左右子树
    int res1 = dfs(vec, row - 1, col);
    int res2 = dfs(vec, row - 1, col + 1);
    
    return vec[row][col] + max(res1, res2);
}

int main() {
    int n;
    cin >> n;
    vector<vector<int>> vec(n, vector<int>(n, 0));

    // 输入三角形数据
    for (int i = n - 1; i >= 0; i--) {
        for (int j = 0; j < n - i; j++) {
            cin >> vec[i][j];
        }
    }

    // 从顶点开始计算最大路径和
    cout << dfs(n - 1, 0) << endl;

    return 0;
}
```

### 记忆化优化的解法

为了避免重复计算，使用记忆化搜索（Memoization）来优化DFS：

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int n;
vector<vector<int>> triangle;   // 存储输入的三角形
vector<vector<int>> memo;       // 记忆化缓存

// DFS + 记忆化搜索
int dfs(int row, int col) {
    // 边界检查
    if (row >= n || col < 0 || col > row) return 0;
    
    // 返回缓存值
    if (memo[row][col] != -1) return memo[row][col];

    // 递归计算两条路径的最大值（向左下和右下）
    int left = dfs(row + 1, col);
    int right = dfs(row + 1, col + 1);

    // 存入缓存
    memo[row][col] = triangle[row][col] + max(left, right);

    return memo[row][col];
}

int main() {
    cin >> n;
    triangle.assign(n, vector<int>(n, 0));
    memo.assign(n, vector<int>(n, -1));  // 初始化为 -1 表示未访问

    // 输入三角形数字
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j <= i; ++j) {
            cin >> triangle[i][j];
        }
    }

    // 从顶点 (0, 0) 开始递归搜索最大路径和
    cout << dfs(0, 0) << endl;

    return 0;
}
```

### 动态规划解法

最后，我们可以用动态规划的方法来解决这个问题，这种方式通常更高效，因为它避免了递归的开销。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    int n;
    cin >> n;
    vector<vector<int>> DP(n, vector<int>(n, 0));

    // 输入三角形数字
    for (int i = n - 1; i >= 0; i--) {
        for (int j = 0; j < n - i; j++) {
            cin >> DP[i][j];
        }
    }

    // 动态规划计算最大路径和
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (j < n - i) {
                DP[i][j] += max(DP[i - 1][j], DP[i - 1][j + 1]);
            }
        }
    }

    cout << DP[n - 1][0] << endl;

    return 0;
}
```

### 时间和空间复杂度分析

- **DFS 解法：** 
  - 时间复杂度：\(O(n^2)\)  (每个节点只计算一次)
  - 空间复杂度：\(O(n)\) (栈深度)

- **记忆化搜索：**
  - 时间复杂度：\(O(n^2)\)
  - 空间复杂度：\(O(n^2)\) (存储三角形和缓存)

- **动态规划：**
  - 时间复杂度：\(O(n^2)\)
  - 空间复杂度：\(O(n^2)\) (存储三角形)

### 总结

使用动态规划可以有效提高程序效率并避免栈溢出的问题。通过逐步计算并存储每个节点的最大路径和，程序的运行时间得到显著降低。