# 字符串算法
## 26.11.4 PTA上的四道题，关于KMP、Z函数
### KMP 字符串匹配
给定文本串 s 和模式串 p，使用 KMP 算法寻找模式串在文本串中第一次出现的位置。

思路：
KMP 的核心是利用已经匹配的部分信息，避免失配后重新从头开始比较。
先计算模式串的前缀函数 next：
next[i] 表示子串 p[0...i] 的最长相等真前缀和真后缀的长度。
匹配失败时：
```
j = next[j - 1];
```
这样就可以保留已经匹配的有效信息，不需要让文本串指针回退。

**核心代码**
```
vector<int> getNext(const string& p)
{
    int n = p.size();
    vector<int> next(n, 0);

    for (int i = 1; i < n; i++)
    {
        int j = next[i - 1];

        while (j > 0 && p[i] != p[j])
        {
            j = next[j - 1];
        }

        if (p[i] == p[j])
            j++;

        next[i] = j;
    }

    return next;
}
```
注意：
- next 数组采用的是 0 下标前缀函数写法，不是传统教材中所有版本都使用的 1 下标写法，不能混用
- 回退时应该写 next[j-1]，不能写成 next[j]。
- 真前缀：不包含字符串本身的前缀。
- next[i]：当前子串最长相等真前后缀的长度。

匹配部分：
```
int KMP(const string& s, const string& p)
{
    if (p.empty())
        return 0;

    vector<int> next = getNext(p);

    int j = 0;

    for (int i = 0; i < (int)s.size(); i++)
    {
        while (j > 0 && s[i] != p[j])
        {
            j = next[j - 1];
        }

        if (s[i] == p[j])
            j++;

        if (j == (int)p.size())
            return i - (int)p.size() + 1;
    }

    return -1;
}
```

### 艾迪字符串编码
根据字符串中每个字符右侧不同字符的数量，计算对应字符的编码。






















