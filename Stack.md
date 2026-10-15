## 26.10.14 今日重点：矩阵查找、栈、字符串解码、C++基础语法
栈的后进先出、双栈模拟、字符串遍历、字符判断、二维矩阵搜索、前缀积与后缀积
### 基础知识
- s.length() 或 s.size()：获取字符串长度。s.empty()：判断字符串是否为空。
- isdigit() 字符判断函数：判断字符是否是从“0~9”的数字
- 字符转整数：c - '0'。多位数字需要累积：num = num * 10 + (c - '0')
- char c : s   依次读取字符串 s 中的每一个字符，每次将当前字符赋给变量 c。
- 栈操作：
  - stack<int> st
  - st.push(x)
  - st.pop()  出栈栈顶元素
  - st.top()  获取栈顶元素
  - st.empty()  判断是否为空
- 构造函数，与类同名，没有返回类型。如果没有声明任何构造函数，编译器通常会提供默认构造函数。但需要特别初始化的成员仍然应该明确初始化。

<img width="1049" height="609" alt="image" src="https://github.com/user-attachments/assets/83986b11-1ffe-428f-b6dd-8cd351a18fa1" />


### LeetCode 240：搜索二维矩阵 II

给定一个满足以下条件的二维矩阵：每一行从左到右递增；每一列从上到下递增。判断目标值 target 是否存在于矩阵中。

核心思路：从右上角开始搜索，判断右上角元素与target的大小关系，比target大则往左边走，反之往下走

### LeetCode 20：有效的括号
给定只包含 ()、[]、{} 的字符串，判断括号是否有效。
有效字符串必须满足：
左右括号类型匹配。
括号闭合顺序正确。
每个右括号都有对应的左括号。

核心思路：栈。左括号入栈，遇到右括号时检查栈顶：
- 如果栈为空，说明没有左括号可以匹配，返回 false。
- 如果栈顶与当前右括号匹配，弹出栈顶。
- 如果不匹配，返回 false。
- 遍历结束后，只有栈为空才有效。
```
#include <string>
#include <stack>
using namespace std;

class Solution {
public:
    bool isValid(string s) {
        stack<char> st;

        for (char c : s) {
            if (c == '(' || c == '[' || c == '{') {
                st.push(c);
            }
            else {
                if (st.empty()) {
                    return false;
                }

                char top = st.top();

                if ((c == ')' && top == '(') ||
                    (c == ']' && top == '[') ||
                    (c == '}' && top == '{')) {
                    st.pop();
                }
                else {
                    return false;
                }
            }
        }

        return st.empty();
    }
};
```
### LeetCode 155：最小栈
设计一个栈，支持：push(val)：入栈；pop()：删除栈顶；top()：获取栈顶；
getMin()：获取当前最小元素；
要求 getMin() 的时间复杂度为 O(1)。

核心思路：双栈。
- stack<int> xStack;    // 保存所有元素
- stack<int> minStack;  // 保存每一步的最小值
```
class MinStack {
private:
    stack<int> xStack;
    stack<int> minStack;

public:
    void push(int val) {
        xStack.push(val);

        if (minStack.empty()) {
            minStack.push(val);
        }
        else {
            minStack.push(min(val, minStack.top()));
        }
    }

    void pop() {
        xStack.pop();
        minStack.pop();
    }

    int top() {
        return xStack.top();
    }

    int getMin() {
        return minStack.top();
    }
};
```
注意：两个栈必须同步出栈，否则最小值历史记录就会错位。
### LeetCode 394：字符串解码
编码规则：k[encoded_string] 表示将括号内的字符串重复 k 次。

核心思路：双栈
```
class Solution {
public:
    string decodeString(string s) {
        stack<int> numStack;
        stack<string> strStack;

        string cur = "";
        int num = 0;

        for (char c : s) {
            if (isdigit(c)) {
                num = num * 10 + (c - '0');
            }
            else if (c == '[') {
                numStack.push(num);
                strStack.push(cur);

                num = 0;
                cur = "";
            }
            else if (c == ']') {
                int k = numStack.top();
                numStack.pop();

                string prev = strStack.top();
                strStack.pop();

                string temp = "";
                for (int i = 0; i < k; i++) {
                    temp += cur;  //表示字符串拼接
                }

                cur = prev + temp;
            }
            else {
                cur += c;
            }
        }

        return cur;
    }
};
```
