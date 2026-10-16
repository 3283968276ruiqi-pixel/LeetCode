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






























