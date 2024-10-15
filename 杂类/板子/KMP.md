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
