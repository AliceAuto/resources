为了求解这个问题，我们可以使用一种高效的字符串匹配算法，如 KMP（Knuth-Morris-Pratt）算法。这种算法在处理字符串查找时能有效地避免重复检查，从而提高效率，尤其是当字符串长度较大时。

### KMP 算法简介

KMP 算法的核心在于构建一个部分匹配表（也称为前缀表），该表能够在字符串匹配失败时，指示下一次匹配时应该跳过多少字符。这使得算法能够在 O(n + m) 的时间复杂度内完成查找，其中 n 和 m 分别为字符串 A 和 B 的长度。

### 代码实现

以下是实现字符串 B 在字符串 A 中出现次数的 C++ 代码：

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

// 构建前缀表
vector<int> buildPrefixTable(const string& B) {
    int m = B.size();
    vector<int> prefix(m, 0);
    int j = 0; // 长度为 j 的前缀
    for (int i = 1; i < m; ++i) {
        while (j > 0 && B[i] != B[j]) {
            j = prefix[j - 1]; // 失败时跳转
        }
        if (B[i] == B[j]) {
            j++; // 匹配时增加 j
        }
        prefix[i] = j; // 记录前缀表
    }
    return prefix;
}

// KMP 算法
int kmpSearch(const string& A, const string& B) {
    vector<int> prefix = buildPrefixTable(B);
    int n = A.size(), m = B.size();
    int j = 0; // 当前匹配的 B 的索引
    int count = 0; // B 在 A 中出现的次数

    for (int i = 0; i < n; ++i) {
        while (j > 0 && A[i] != B[j]) {
            j = prefix[j - 1]; // 失败时跳转
        }
        if (A[i] == B[j]) {
            j++; // 匹配时增加 j
        }
        if (j == m) { // 找到一个匹配
            count++; // 统计出现次数
            j = prefix[j - 1]; // 继续查找可能的重叠匹配
        }
    }
    return count;
}

int main() {
    string A, B;
    cin >> A >> B;

    int result = kmpSearch(A, B);
    cout << result << endl;

    return 0;
}
```

### 代码解析

1. **前缀表构建**:
   - `buildPrefixTable` 函数用于构建字符串 B 的前缀表。前缀表用于存储字符串的最长相等前后缀的长度。

2. **KMP 查找**:
   - `kmpSearch` 函数实现 KMP 算法的核心逻辑。它通过遍历字符串 A 并根据前缀表来判断字符是否匹配，从而统计 B 在 A 中的出现次数。

3. **主函数**:
   - 在 `main` 函数中，读取字符串 A 和 B，并调用 `kmpSearch` 函数进行匹配，最后输出结果。

### 性能分析

- **时间复杂度**: O(n + m)，其中 n 为字符串 A 的长度，m 为字符串 B 的长度。相较于暴力算法，KMP 显著提高了性能，适合处理长度较大的字符串。

### 示例输入输出

#### 输入

```
zyzyzyz
zyz
```

#### 输出

```
3
```

该代码实现了求解 B 在 A 中出现次数的功能，并能处理较大的输入数据。可以根据实际情况进行扩展和优化。