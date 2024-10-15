下面是标准的 KMP（Knuth-Morris-Pratt）字符串匹配算法的 C++ 实现模板。KMP 算法通过预处理模式串构建一个部分匹配表（也称为前缀表），以加速匹配过程。

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

// KMP 算法
class KMP {
public:
    // 构建部分匹配表
    vector<int> buildPartialMatchTable(const string& pattern) {
        int m = pattern.size();
        vector<int> lps(m, 0); // lps 数组初始化为 0
        int len = 0; // 前缀的长度
        int i = 1;

        while (i < m) {
            if (pattern[i] == pattern[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1]; // 回退
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        return lps;
    }

    // KMP 字符串匹配
    void kmpSearch(const string& text, const string& pattern) {
        int n = text.size();
        int m = pattern.size();
        vector<int> lps = buildPartialMatchTable(pattern);
        int i = 0; // 文本指针
        int j = 0; // 模式指针

        while (i < n) {
            if (pattern[j] == text[i]) {
                i++;
                j++;
            }

            if (j == m) {
                cout << "Pattern found at index " << (i - j) << endl;
                j = lps[j - 1]; // 继续搜索
            } else if (i < n && pattern[j] != text[i]) {
                if (j != 0) {
                    j = lps[j - 1]; // 回退
                } else {
                    i++;
                }
            }
        }
    }
};

int main() {
    string text = "ABABDABACDABABCABAB";
    string pattern = "ABABCABAB";

    KMP kmp;
    kmp.kmpSearch(text, pattern);

    return 0;
}
```

### 代码说明：
1. **部分匹配表（LPS）**: `buildPartialMatchTable` 函数用于构建模式串的部分匹配表，帮助算法确定在匹配失败时模式串应该向右移动多少位。
2. **KMP 搜索**: `kmpSearch` 函数在给定的文本中搜索模式串，并输出匹配的位置。
3. **主函数**: 在 `main` 函数中，定义了待搜索的文本和模式串，并调用 KMP 搜索。

### 使用方法：
将代码复制到你的 C++ 环境中，修改 `text` 和 `pattern` 变量以测试不同的字符串匹配。