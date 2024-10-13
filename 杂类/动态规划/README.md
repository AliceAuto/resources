
# 题目:[小红的矩阵行走](https://ac.nowcoder.com/acm/contest/91592/C)
**这个题目是一个一眼看上去像贪心**
**实际上这个题目可以用动态规划，可以用DFS，也可以用BFS,但是不能贪心**


## 首先我们分析一下,为什么我知道不是贪心?
---
#### 来来来，我们回顾一下!

##### 贪心算法的核心是什么？
```
核心是我可以选择一个局部最优解，但是b不一定是全局最优解
```
##### 那我怎么知道不能局部推全局呢？
- 最直接的方法当然是找反例

```
你贪心地向着目标移动,但是有时候可是会进"死胡同"啊!!
这就是客观事实，这就是我们的依据
```
- 那最自然的思路呢?
```
遇到可行路径问题,一定第一反应向着DP与图论想，因为他是解决最优化问题非常优雅的数学模型
```
- DP 与图论那个又更好呢
  **答案是各有优缺点**


| **算法**     | **时间复杂度**       | **空间复杂度**     | **优缺点**                          | **适用场景**                      |
|--------------|----------------------|--------------------|-------------------------------------|-----------------------------------|
| **DFS**      | 指数级 \(O(2^{n \times m})\)  | 递归栈开销            | 简单实现，适合小规模矩阵；栈溢出风险 | 小规模矩阵，简单判断问题         |
| **BFS**      | \(O(n \times m)\)    | \(O(n \times m)\)   | 避免栈溢出，找最短路径；队列开销大  | 路径长度要求，避免递归问题       |
| **DP**       | \(O(n \times m)\)    | \(O(n \times m)\)   | 避免重复计算，代码简洁；无法还原路径 | 可达性问题，路径长度不重要       |
| **并查集**   | \(O(n \times m \cdot \alpha(n \times m))\) | \(O(n \times m)\)   | 性能高效，适合稀疏矩阵；实现复杂   | 频繁连通性判断，稀疏矩阵场景     |

#### **总结推荐**  
- **小规模矩阵**：选择 **DFS**。
- **大规模矩阵**：优先 **BFS**（路径问题）或 **DP**（可达性）。
- **稀疏矩阵**：使用 **并查集** 处理连通性问题。
---
#### 大的思路引导我们已经找到了本质，接下来就是具体分析

## **动态规划思路**




#### **1. 深度优先搜索 (DFS)**

##### **直觉启发**
- 在探索路径问题时，我们的直觉告诉我们，从起点出发，逐步尝试每一个可能的路径，直到找到终点。

##### **原因分析**
- DFS 能够通过递归的方式深入每一条路径，适合解决所有可能的路径的探索问题。

##### **挑战**
- 然而，当矩阵规模较大时，DFS 可能导致栈溢出，特别是路径较长或者遇到死胡同时。

##### **实现思路**
- 我们定义一个递归函数 `dfs(x, y)`，检查位置 \((x, y)\) 的可达性。
- 使用布尔数组来记录已访问的节点，避免重复遍历。
- 基准条件：如果当前节点是终点，返回成功；如果越界或不可达，返回失败。
- 在探索过程中，检查四个方向（上、下、左、右），递归调用 `dfs`。
##### **代码**
```c++
#include <iostream>
#include <vector>

using namespace std;

const int directions[4][2] = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}}; // 下、上、右、左
vector<vector<int>> matrix;
vector<vector<bool>> visited;

bool dfs(int x, int y, int n, int m) {
    if (x < 0 || x >= n || y < 0 || y >= m || visited[x][y] || matrix[x][y] == 0) {
        return false; // 越界或已经访问过或不可达
    }
    if (x == n - 1 && y == m - 1) {
        return true; // 找到终点
    }
    visited[x][y] = true; // 标记为已访问
    for (auto &d : directions) {
        if (dfs(x + d[0], y + d[1], n, m)) {
            return true; // 递归探索相邻节点
        }
    }
    return false;
}

int main() {
    // 示例输入矩阵
    int n = 3, m = 3;
    matrix = {{1, 0, 0}, {1, 1, 0}, {0, 1, 1}};
    visited.resize(n, vector<bool>(m, false));

    if (dfs(0, 0, n, m)) {
        cout << "Yes" << endl; // 可达终点
    } else {
        cout << "No" << endl; // 不可达终点
    }

    return 0;
}

```
##### **结果**
- 如果 DFS 成功到达终点，输出 "Yes"；否则输出 "No"。

---

#### **2. 广度优先搜索 (BFS)**

##### **直觉启发**
- 我们想要从起点出发，逐层探索可能的路径，以确保找到最短路径。

##### **原因分析**
- BFS 利用队列结构，能有效管理待探索的节点，适合处理路径长度要求的问题。

##### **优势**
- 由于 BFS 从起点开始，按层次处理节点，避免了深度过大带来的风险。

##### **实现思路**
- 初始化队列，将起点加入队列。
- 使用布尔数组记录已访问的节点，避免重复处理。
- 在队列非空时，取出队头节点，判断是否为终点；如果不是，检查其邻接节点，并将可达且未访问的节点加入队列。

##### **结果**
- 如果 BFS 找到终点，输出 "Yes"；否则输出 "No"。
##### **代码**
```c++
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

const int directions[4][2] = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
vector<vector<int>> matrix;
vector<vector<bool>> visited;

bool bfs(int n, int m) {
    queue<pair<int, int>> q;
    q.push({0, 0}); // 从起点 (0, 0) 开始
    visited[0][0] = true; // 标记起点为已访问

    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        if (x == n - 1 && y == m - 1) {
            return true; // 找到终点
        }
        for (auto &d : directions) {
            int nx = x + d[0], ny = y + d[1];
            if (nx >= 0 && nx < n && ny >= 0 && ny < m && !visited[nx][ny] && matrix[nx][ny] == 1) {
                visited[nx][ny] = true; // 标记为已访问
                q.push({nx, ny}); // 将相邻节点加入队列
            }
        }
    }
    return false; // 遍历结束仍未找到终点
}

int main() {
    // 示例输入矩阵
    int n = 3, m = 3;
    matrix = {{1, 0, 0}, {1, 1, 0}, {0, 1, 1}};
    visited.resize(n, vector<bool>(m, false));

    if (bfs(n, m)) {
        cout << "Yes" << endl; // 可达终点
    } else {
        cout << "No" << endl; // 不可达终点
    }

    return 0;
}
```
---

#### **3. 动态规划 (DP)**

##### **直觉启发**
- 在可达性问题中，递归思维让我们可以从起点开始，逐步构建每个位置的可达性。

##### **原因分析**
- DP 通过保存每个状态的结果，避免重复计算，能有效提升性能。

##### **转移关系**
- 定义状态 \(DP[i][j]\) 表示是否可以到达位置 \((i, j)\)。
- 转移方程为：
  \[
  DP[i][j] = (DP[i-1][j] \land (matrix[i-1][j] == matrix[i][j])) \lor (DP[i][j-1] \land (matrix[i][j-1] == matrix[i][j]))
  \]
- 这表示如果上方或左侧的状态是可达的，并且相应位置的值相同，则当前状态也是可达的。

##### **结果**
- 如果 \(DP[n-1][m-1]\) 为真，则输出 "Yes"；否则输出 "No"。
##### **代码**
```c++
#include <iostream>
#include <vector>

using namespace std;

vector<vector<int>> matrix;
vector<vector<bool>> DP;

bool canReach(int n, int m) {
    DP[0][0] = (matrix[0][0] == 1); // 起点可达性初始化

    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            if (matrix[i][j] == 1) {
                if (i > 0) DP[i][j] = DP[i][j] || DP[i - 1][j]; // 来自上方
                if (j > 0) DP[i][j] = DP[i][j] || DP[i][j - 1]; // 来自左侧
            }
        }
    }
    return DP[n - 1][m - 1]; // 返回终点可达性
}

int main() {
    // 示例输入矩阵
    int n = 3, m = 3;
    matrix = {{1, 0, 0}, {1, 1, 0}, {0, 1, 1}};
    DP.resize(n, vector<bool>(m, false));

    if (canReach(n, m)) {
        cout << "Yes" << endl; // 可达终点
    } else {
        cout << "No" << endl; // 不可达终点
    }

    return 0;
}
```
---

#### **4. 并查集 (Union-Find)**

##### **直觉启发**
- 对于稀疏矩阵的连通性问题，我们可以考虑使用并查集，以高效管理节点之间的连接关系。

##### **原因分析**
- 并查集能够快速判断两个节点是否在同一连通分量，适用于需要频繁检查连通性的场景。

##### **实现思路**
- 初始化每个节点的父节点，并设置秩以优化合并操作。
- 通过 `find` 函数查找节点的根，并进行路径压缩。
- 使用 `union` 函数合并两个节点的集合，并保持树的平衡。
- 遍历矩阵，将相同值的节点合并，最后检查起点和终点是否属于同一集合。

##### **结果**
- 如果起点和终点在同一集合中，则输出 "Yes"；否则输出 "No"。
##### **代码**
```c++
#include <iostream>
#include <vector>

using namespace std;

vector<vector<int>> matrix;
vector<int> parent, rank;

int find(int x) {
    if (parent[x] != x) {
        parent[x] = find(parent[x]); // 路径压缩
    }
    return parent[x];
}

void unionSets(int x, int y) {
    int rootX = find(x), rootY = find(y);
    if (rootX != rootY) {
        if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
    }
}

bool canReach(int n, int m) {
    int totalNodes = n * m;
    parent.resize(totalNodes);
    rank.resize(totalNodes, 0);
    for (int i = 0; i < totalNodes; ++i) {
        parent[i] = i; // 初始化每个节点的父节点
    }

    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            if (matrix[i][j] == 1) {
                if (i > 0 && matrix[i - 1][j] == 1) {
                    unionSets(i * m + j, (i - 1) * m + j); // 上方节点合并
                }
                if (j > 0 && matrix[i][j - 1] == 1) {
                    unionSets(i * m + j, i * m + (j - 1)); // 左侧节点合并
                }
            }
        }
    }

    return find(0) == find(n * m - 1); // 检查起点和终点是否属于同一集合
}

int main() {
    // 示例输入矩阵
    int n = 3, m = 3;
    matrix = {{1, 0, 0}, {1, 1, 0}, {0, 1, 1}};

    if (canReach(n, m)) {
        cout << "Yes" << endl; // 可达终点
    } else {
        cout << "No" << endl; // 不可达终点
    }

    return 0;
}
```
---

### **总结**
通过这种引导式分析，我们明确了每种算法的直觉、原因、实现思路和最终结果，使得整体逻辑更加连贯。这样不仅有助于理解每种方法的核心思想，也能在面对不同类型的问题时，快速找到合适的解决方案。