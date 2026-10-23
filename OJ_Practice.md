# 第一次上机练习笔记：结构体、类封装、栈与队列
## 基础知识 
### 把结构体封装进类
```
struct Stack
{
    int top;
    char data[MAXSIZE];
};

class StackLink
{
private:
    Stack s;
```
当你在主函数中写：StackLink st，编译器内部就会有自己的Stack s，因此不需要另外创建Stack s，也不需要把它作为参数传入构造函数

如果要从外部把结构体传进类
```
class StackLink
{
private:
    Stack s;

public:
    StackLink(const Stack& other)
    {
        s = other;
    }
};

int main()
{
  Stack s1;
  s1.top = -1;
  StackLink st(s1);
}
```
### 构造函数与析构函数
构造函数：创建对象时自动调用，常用于初始化成员变量
```
//写法1.
StackLink()
{
    s.top = -1;
}
//写法2.
StackLink() : s{}
{
    s.top = -1; //s{}会将函数体成员值初始化，其中top会先变为0，随后函数体将其设为-1
}
```
不能直接写 StackLink() : s.top(-1)，因为 s.top 不是 StackLink 的直接成员，而是其成员对象 s 内部的成员。

析构函数：没有返回类型，也不接受参数，对象生命周期结束时自动调用，常用于释放对象自己申请并负责管理的动态资源。
当前的栈使用固定大小的数组，没有调用 new 动态申请内存，因此通常不需要自己编写析构函数。编译器会生成默认析构函数。

类内部使用 new 申请动态数组，并且该类负责管理这块内存时，就需要考虑在析构函数中使用 delete[] 释放。
```
class Example
{
private:
    int* p;

public:
    Example()
    {
        p = new int[10];
    }

    ~Example()
    {
        delete[] p;
    }
};
```
判断字符串的某个字符是否为空：str[i] != '\0'
### 队列
队列遵循“先进先出”
<img width="861" height="477" alt="image" src="https://github.com/user-attachments/assets/24073b8d-67fd-4a76-9faa-0e285d91becb" />
STL队列的基本使用
```
#include <queue>
queue<int> q;
q.push(10);    // 队尾加入 10
q.push(20);    // 队尾加入 20
q.push(30);    // 队尾加入 30

q.front();     // 队头元素：10
q.back();      // 队尾元素：30

q.pop();       // 删除队头元素 10
q.empty();     // 判断是否为空
q.size();      // 获取元素个数
```
有时rear指向最后一个元素，有时指向下一个空位置。为了避免普通数组队列删除元素后前面空间无法服用的问题，实际实现时经常采用循环队列，
循环队列会通过模运算让下标绕回数组开头。
```
rear = (rear + 1) % MAXSIZE
```
这表示队尾下标加一，如果达到数组末尾，就回到下标0。
循环队列还需要明确区分“队列为空”和“队列已满”的条件，常见做法是预留一个空位置，或者额外维护元素个数。

## 括号匹配题的思路与错误复盘
题目思路：
- 遇到左括号：先检查新括号与栈顶括号的嵌套等级是否符合要求，再入栈。
- 遇到右括号：栈必须非空，并且右括号必须与栈顶左括号匹配；匹配后出栈。
- 遇到普通字符：忽略。
- 所有字符处理完毕：只有栈为空，才说明所有左括号都匹配完成。

嵌套等级
```
int getRank(char c)
{
    if (c == '{') return 1;
    if (c == '[') return 2;
    if (c == '(') return 3;
    if (c == '<') return 4;
    return 0;
}
```
# 第二次上机练习笔记：动态规划、BFS、**快速幂**、多位整数读取、复杂度分析
## K进制转换
核心原理：把十进制正整数转换为K进制，可以反复进行**除K取余**，把余数逆序输出。可以采用数组保存余数，也可以递归输出。
```
void convert(int n, int K) {
    if (n == 0) {
        return;
    }

    convert(n / K, K);
    cout << n % K;
}
```
## 动态规划：每次可走1、2、3、4阶的台阶问题
每次可走1、2、3、4阶，求到达第n阶有多少种不同走法

**动态规划**的三个关键步骤：
- 定义状态，dp[i]，表示到达第i阶的不同走法数量
- 推到状态转移方程，到达第i阶，可能从第i-1阶再走1阶、从第i-2阶再走2阶、从第i-3阶再走3阶、从第i-4阶再走4阶。所以dp[i]=dp[i-1]+dp[i-2]+dp[i-3]+dp[i-4]（适用于i>=4）
- 确定初始化：dp[0]=0,dp[1]=1,dp[2]=2,dp[3]=4。这里的dp[0]=1理解为什么都不走，有一种空方案，这样统一使用转移方程也方便

## BFS:接驳车网格最短路径
地图是n行m列的网格：0为可以通行的空地，1为障碍物，3为起点，4为终点。每次只能上下左右移动一格，每次耗时1，无法从起点到达终点则输出unreachable，输入有**多组数据**，直到文件结束

BFS是广度优先搜索，会按照距离从近到远逐层扩展。在每次移动代价相同的网格中，BFS第一次到达某个位置时，得到的就是从起点到该位置的最短距离。

核心代码：
```
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
queue<pair<int, int>> q;      //队列的基础知识

q.push({startX, startY});
dist[startX][startY] = 0;

while (!q.empty()) {
    int x = q.front().first;    //队列的基础知识
    int y = q.front().second;
    q.pop();

    if (x == endX && y == endY) {
        break;
    }

    for (int i = 0; i < 4; i++) {
        int nx = x + dx[i];             //分别计算距离该点最近的上下左右四个方向的点的位置坐标
        int ny = y + dy[i];

        if (nx < 0 || nx >= n || ny < 0 || ny >= m) { 
            continue;            //新位置越界
        }

        if (a[nx][ny] == 1) {
            continue;            //新位置是障碍物
        }

        if (dist[nx][ny] != -1) {
            continue;            //新位置已经访问过了
        }

        dist[nx][ny] = dist[x][y] + 1;        //距离更新
        q.push({nx, ny});
    }
}
```
初始化dist[][]数组：
```
for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
        dist[i][j] = -1;
    }
}
```
不能用int dist[][]={-1}，这只会把整个二维数组的第一个元素设置为-1，其余的会被初始化为0

由于本题是**多组输入**，注意不要把 while 放错位置，必须先读本组n,m，再读本组地图，不能在n.m尚未读取时，就进行地图处理

## 中缀表达式求值
计算包含以下运算符的中缀表达式：+  -  *  /  ^  (  )

要求：幂运算不能调用 pow()；除数为 0 时输出 INVALID。

**双栈思想**：使用两个栈
```
stack<long long> nums;//存放数字
stack<char> ops;//存放字符
```
注意：遇到数字，要**解析完整数字**之后再压入栈。遇到左括号直接入栈，遇到右括号要先不断计算，直到遇到对应的左括号 

运算符号的优先级：
```
int priority(char op) {
    if (op == '+' || op == '-')
        return 1;
    if (op == '*' || op == '/')
        return 2;
    if (op == '^')
        return 3;
    return 0;
}
```
**读取多位整数代码**：
```
if (s[i] >= '0' && s[i] <= '9') {
    long long num = 0;

    while (i < (int)s.size() &&                                 //s.size() 返回字符串长度，类型通常是无符号整数类型，而 i 是 int。
           s[i] >= '0' && s[i] <= '9') {
        num = num * 10 + (s[i] - '0');
        i++;
    }

    nums.push(num);
    continue;
}
```
判断能否进行运算并计算一次运算记录结果：
```
bool calculate(stack<long long>& nums,
               stack<char>& ops) {
    long long b = nums.top();
    nums.pop();

    long long a = nums.top();
    nums.pop();
//这里需要注意操作数的顺序不要弄混了，先弹出的是右操作数
    char op = ops.top();
    ops.pop();

    long long result = 0;

    if (op == '+')
        result = a + b;
    else if (op == '-')
        result = a - b;
    else if (op == '*')
        result = a * b;
    else if (op == '/') {
        if (b == 0)
            return false;

        result = a / b;
    }
    else if (op == '^')
        result = power(a, b);

    nums.push(result);
    return true;
}
```
右括号的处理：
```
while (!ops.empty() && ops.top() != '(') {
    if (!calculate(nums, ops)) {
        valid = false;
        break;
    }
}

if (!valid)
    break;

if (!ops.empty() && ops.top() == '(')
    ops.pop();
```
**幂运算的两种实现**：
```
long long power(long long a, long long b) {
    long long result = 1;

    for (long long i = 0; i < b; i++) {
        result *= a;
    }

    return result;
}
// 普通幂，时间复杂度是O(n)
```
```
long long power(long long a, long long b) {
    long long result = 1;

    while (b > 0) {
        if (b % 2 == 1)            
            result *= a;

        b /= 2;

        if (b > 0)
            a *= a;
    }

    return result;
}
// 快速幂：每次将指数折半，时间复杂度为O(log n)
```

## 基础知识
<img width="925" height="597" alt="image" src="https://github.com/user-attachments/assets/855aaf75-43f0-4110-a03f-4f7c78548ba1" />

**重点：多位整数读取，BFS的执行流程（先取队头，再检查四个方向，最后更新距离并入队），快速幂的原理**




















