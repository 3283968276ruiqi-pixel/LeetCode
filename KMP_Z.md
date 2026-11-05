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
把一个字符串S编码为另一个字符串，他设计了一个函数FS（c)，将该字符串S的某个字符c映射为另一个字符。该函数定义为：
FS（c)=chr（G（c，S))

其中G（c，S)表示字符c在字符串S中最后一次出现的位置后面不重复字符的个数。艾迪的编码算法即：依次将S中的每一个字符c替换为FS（c)。

思路：从字符串**右侧向左遍历**，使用一个布尔数组 seen[26]，记录某个小写字母是否已经在当前位置的右侧出现过
```
int seen[26] = {0};

for (int i = n - 1; i >= 0; i--)
{
    int cnt = 0;

    for (int j = 0; j < 26; j++)
    {
        if (seen[j])
            cnt++;
    }

    // 当前字符的编码为 cnt

    seen[s[i] - 'a'] = 1;
}
```
### 波比和哈丽字母游戏
波比给出一个字符串S，要求哈丽按照一定规则，基于该字符串算出一个数字X。

规则是：
- 求出S的最长重复后缀P（P是S的后缀且在S中出现大于1次，例如yacbacba的最长重复后缀是acba），
- 求出在S中去除第二长相等前后缀（S中所有相等的前后缀中第2长者，例如abcabcxxxabcabc中最长相等前后缀是abcabc，第二长的相等前后缀则是abc）后剩下的子串Q（例如abcabcxxxabcabc去除第二长相等前后缀后，剩下abcxxxabc）。

则X=P的长度+Q的长度。
注意一个字符串不能称为自己的前缀或后缀。子串Q至少为空串，其长度大于等于0，不能为负数。

基础知识：
- Z函数：z[i]表示从位置i开始的后缀与整个字符串前缀的最长公共前缀长度
- KMP：处理前后缀匹配和模式串失配
- 后缀自动机：通过状态和转移记录字符串的字串信息，适合处理不同子串及其出现情况

Z函数的基本实现：
```
vector<int> getZ(const string& s)
{
    int n = s.size();
    vector<int> z(n, 0);
    int l = 0, r = 0;
    for (int i = 1; i < n; i++)
    {
        if (i <= r)
            z[i] = min(r - i + 1, z[i - l]);

        while (i + z[i] < n && s[z[i]] == s[i + z[i]])         
        {
            z[i]++;
        }
        if (i + z[i] - 1 > r)
        {
            l = i;
            r = i + z[i] - 1;
        }
    }
    return z;
}
```
注意：需要反转字符串后取最大的Z值，对应题目要求求P的长度；第二长相等前后缀就是S中最长的子串的最长的相等前后缀，只需要再套进一个next即可

### 字符串前缀排名求和
称一个字符串的秩为：该字符串长度减去该字符串的最短相等前后缀的长度。若该字符串不存在相等的前后缀，则其秩为0。计算S及S中所有前缀子串的秩之和。

思路：使用KMP前缀函数next，并通过**DP**记录每个前缀的最短相等**真前后缀**长度。若当前前缀的最长相等
真前后缀长度为k，k=0则不存在，k>0就要判断长度为k的前缀本身是否存在更短的相等真前后缀。

状态转移方程：
```
if (k == 0)
    shortest[i] = 0;
else if (shortest[k - 1] == 0)
    shortest[i] = k;
else
    shortest[i] = shortest[k - 1];
```
注意：利用DP能降低时间复杂度，long long存储较大的整数结果









