# 数组与矩阵（C++）
## 26.10.12 今日重点：数组、二维矩阵、原地算法、前缀积与后缀积
双指针、动态规划、三次反转、矩阵转置、边界模拟、原地标记
### 基础知识
二维 vector 的定义与访问：vector<vector<int>>& matrix

使用 reverse() 时，右端迭代器不包含在反转范围内。

常用操作：

- int m = matrix.size();       // 行数
- int n = matrix[0].size();    // 列数
- int x = matrix[i][j];        // 访问第 i 行、第 j 列
- matrix[i][j] = 0;            // 修改元素
### Leetcode 283：移动零
将数组中的所有 0 移动到末尾，同时保持非零元素的相对顺序，并且必须原地操作。

核心思路：双指针。
- fast：遍历数组，寻找非零元素。
- slow：指向下一个应该存放非零元素的位置。遇到非零元素时，将其放到 slow 所在位置，然后 slow++。
### Leetcode 53：最大子数组和
给定一个整数数组，找到具有最大和的连续子数组，并返回其和。

**动态规划**：定义 cur 为以当前位置结尾的最大子数组和。遍历到 nums[i] 时，有两种选择：把当前元素接到之前的子数组后面；
不接之前的子数组，从当前元素重新开始。使用 ans 保存遍历过程中出现的最大值。
```
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int cur = nums[0];
        int ans = nums[0];

        for (int i = 1; i < nums.size(); i++) {
            cur = max(nums[i], cur + nums[i]);
            ans = max(ans, cur);
        }

        return ans;
    }
};
```
容易出错的地方:
- 不能把 cur 初始化为 0，否则可能错误处理全负数数组。
- cur 表示以当前位置结尾的最大和，ans 表示遍历到目前为止的最大和，两者含义不同。
### Leetcode 189:轮转数组
（本题默认数组非空） k 个位置。核心思路：**三次反转**.整个数组反转;反转前k个元素；反转剩余的n-k个元素。
```
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        k %= n;

        reverse(nums.begin(), nums.end());  //左开右闭
        reverse(nums.begin(), nums.begin() + k);   //.end()表示数组最后一个元素的后一个位置
        reverse(nums.begin() + k, nums.end());
    }
};
```
### Leetcode 48:旋转图像
将一个 n × n 的二维矩阵顺时针旋转 90 度。
要求直接修改原矩阵，不能创建另一个同样大小的矩阵。

核心思路：先转置，再反转每一行，转置就是交换矩阵的行和列。就能得到顺时针旋转90°的结果。

如果顺时针旋转180°：每一行反转，再将矩阵上下交换；或者是先转置，再将每一列上下反转。

顺时针旋转270°：先转置，再将每一列上下反转。
### Leetcode 54:螺旋矩阵
按照顺时针螺旋顺序，返回矩阵中的所有元素。

核心思路：四个边界模拟。使用四个变量：
```
使用四个变量：

int top = 0;//尚未遍历区域的上边界
int bottom = matrix.size() - 1;//下边界
int left = 0;  //左边界
int right = matrix[0].size() - 1;
```
```
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;

        int top = 0;
        int bottom = matrix.size() - 1;
        int left = 0;
        int right = matrix[0].size() - 1;

        while (top <= bottom && left <= right) {
            // 从左向右
            for (int j = left; j <= right; j++) {
                res.push_back(matrix[top][j]);
            }
            top++;

            // 从上向下
            for (int i = top; i <= bottom; i++) {
                res.push_back(matrix[i][right]);
            }
            right--;

            // 从右向左：先确认还有剩余行
            if (top <= bottom) {
                for (int j = right; j >= left; j--) {
                    res.push_back(matrix[bottom][j]);
                }
                bottom--;
            }

            // 从下向上：先确认还有剩余列
            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    res.push_back(matrix[i][left]);
                }
                left++;
            }
        }

        return res;
    }
};
```
### Leetcode 73:矩阵置零
如果矩阵中的某个元素为 0，就把它所在的整行和整列全部置为 0。使用原地算法.

思路：运用第一行和第一列，每当发现matrix[i][j] == 0，就记录matrix[i][0] = 0;
matrix[0][j] = 0。用两个布尔变量单独记录第一行、第一列原本是否包含 0：
```
bool firstRowZero = false;
bool firstColZero = false;
```
```
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();

        bool firstRowZero = false;
        bool firstColZero = false;

        // 记录第一行原本是否有 0
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) {
                firstRowZero = true;
                break;
            }
        }

        // 记录第一列原本是否有 0
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                firstColZero = true;
                break;
            }
        }

        // 利用第一行、第一列标记其他行列
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }

        // 根据标记处理其余元素
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 ||
                    matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

        // 最后处理第一行
        if (firstRowZero) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }

        // 最后处理第一列
        if (firstColZero) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
    }
};
```
容易出错的思路：发现0就立即清零整列、整行，这会把新产生的0当作原始的0，造成错误的扩散。

### LeetCode 238：除自身以外数组的乘积
给定一个整数数组 nums，返回数组 answer，其中：
answer[i] 等于 nums 中除 nums[i] 之外所有元素的乘积。
要求不能使用除法，时间复杂度为 O(n)。

核心思路：前缀积与后缀积,answer[i] = 左侧所有元素的乘积 × 右侧所有元素的乘积。
这里左侧乘积在 i = 0 时没有元素，因此初始化为乘法单位元 1；右侧乘积在最后一个位置同理为 1。
```
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> answer(n, 1);

        int prefix = 1;

        // 第一遍：计算每个位置左侧所有元素的乘积
        for (int i = 0; i < n; i++) {
            answer[i] = prefix;
            prefix *= nums[i];
        }

        int suffix = 1;

        // 第二遍：乘上每个位置右侧所有元素的乘积
        for (int i = n - 1; i >= 0; i--) {
            answer[i] *= suffix;
            suffix *= nums[i];
        }

        return answer;
    }
};
```
注意：前缀积用answer记录，后缀积可以使用变量来计算并直接给到answer，这样能将额外辅助空间降到O(1)















