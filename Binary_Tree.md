# 二叉树
## 2026.11.5 今日重点：二叉树基本概念、建树、递归、遍历、查找、删除、序列重建
递归：一棵二叉树可以分解为根结点、左子树和右子树，而左右子树本身又是二叉树。

结点深度：从根结点到当前结点经过的边数。二叉树高度：从根结点到最深叶子结点经过的最大边数。
### 二叉树的结构体定义
```
struct TreeNode
{
  int val;
  TreeNode*left;
  TreeNode*right;
  TreeNode(int x)
  {
    val=x;
    left=nullptr;
    right=nullptr;
  }
}
```
### 先序遍历
```
void Preorder(TreeNode*root)
{
  if(root==nullptr)
    return;
  cout<<root->val;
  Preorder(root->left);
  Preorder(root->right);
}
```
根据带空指针信息的先根序列利用**递归**创建二叉树
```
TreeNode* Create()
{
	int x;
	cin >> x;
	if (x == 0)
		return nullptr;
	TreeNode* root = new TreeNode(x);
	root->left = Create();
	root->right = Create();
	return root;
}
```
若有较大的节点数量现在，还可以利用**指针槽位**建树
```
TreeNode* Create()
{
  int a;
  cin>>a;
  TreeNode*root=new TreeNode(a);
	vector<TreeNode**>slots;
	slots.push_back(&root);
	while (!slots.empty())
	{
		TreeNode** p = slots.back();//获取栈顶指针
		slots.pop_back();
		int x;
		cin >> x;
		if (x == 0)
			*p = nullptr;
		else
		{
			*p = new TreeNode(x);
			slots.push_back(&((*p)->right));
			slots.push_back(&((*p)->left));
		}

	}

}
```

### 中序遍历
```
void Inorder(TreeNode* root)
{
	if (root == nullptr)
		return;
	Inorder(root->left);
	cout << root->val;
	Inorder(root->right);

}
```
### 后根遍历
```
void Postorder(TreeNode* root)
{
    if (root == nullptr)
        return;

    Postorder(root->left);
    Postorder(root->right);
    cout << root->val;
}
```
### 求二叉树的高度
```
int Height(TreeNode* root)
{
	if (root == nullptr)
		return -1;
	return max(Height(root->left), Height(root->right)) + 1;

}
```
### 二叉树的空间释放
按照后根顺序释放
```
void Destroy(TreeNode* root)
{
    if (root == nullptr)
        return;

    Destroy(root->left);
    Destroy(root->right);

    delete root;
}
```
### 查找指定节点及其父节点
```
bool Find(TreeNode* root, int x,
          TreeNode* parent,       //普通指针参数，并不能修改
          TreeNode*& target,          //指针的引用，允许递归函数直接修改外部的target指针变量
          TreeNode*& father)
{
    if (root == nullptr)
        return false;

    if (root->val == x)
    {
        target = root;
        father = parent;
        return true;
    }

    if (Find(root->left, x, root, target, father))
        return true;

    if (Find(root->right, x, root, target, father))
        return true;

    return false;
}
```
找到父节点的val值，也是利用相似的递归
### 删除指定节点及以此节点为根的子树
```
bool DeleteSubtree(TreeNode*& root, int x)        //采用指针的引用，能够修改root变量
{
    if (root == nullptr)
        return false;

    if (root->val == x)
    {
        Destroy(root);
        root = nullptr;
        return true;
    }

    if (DeleteSubtree(root->left, x))
        return true;

    if (DeleteSubtree(root->right, x))
        return true;

    return false;
}
```
### 根据中根序列和后根序列重建二叉树
后根序列的最后一个结点一定是根结点；中根序列中根节点左边是左子树，右边是右子树。
多组输入可以这样写:
```
string post, in;
while (getline(cin, post) && getline(cin, in))
{
    // 处理一组数据
}
//使用getline函数处理多组数据，或者直接cin>>post>>in;
```

核心递归代码：

```
TreeNode* Build(string& post, string& in,
    int pl, int pr, int il, int ir, bool& valid)
{
    if (!valid)
        return nullptr;

    if (pl > pr && il > ir)
        return nullptr;

    if (pl > pr || il > ir)
    {
        valid = false;
        return nullptr;
    }

    if (pr - pl != ir - il)
    {
        valid = false;
        return nullptr;
    }

    char rootVal = post[pr];

    int k = il;

    while (k <= ir && in[k] != rootVal)
        k++;

    if (k > ir)
    {
        valid = false;
        return nullptr;
    }

    int leftSize = k - il;

    TreeNode* root = new TreeNode(rootVal);

    root->left = Build(post, in,
        pl, pl + leftSize - 1,
        il, k - 1, valid);

    root->right = Build(post, in,
        pl + leftSize, pr - 1,
        k + 1, ir, valid);

    if (!valid)
    {
        Destroy(root);
        return nullptr;
    }

    return root;
}
```
## 26.11.10 今日重点：二叉树中的递归、树的高度、双指针递归、镜像判断、全局变量
递归可以根据题目需要决定是否接收递归返回值，如果函数本身不是void类型的，若题目不需要也可以不接收返回值

### LeetCode 94:二叉树的中序遍历
给定一个二叉树的根节点 root，返回它的中序遍历结果。

解题思路：**DFS递归**，使用一个vector<int>保存遍历结果，通过辅助函数递归访问左右子树。或者通过**栈**

执行顺序：
- 判断当前节点是否为空
- 递归遍历左子树
- 将当前节点的值加入数组
- 递归遍历右子树
- 返回遍历结果
```
class Solution
{
public:
    vector<int> order;

    void inorder(TreeNode* root)
    {
        if (root == nullptr)
            return;

        inorder(root->left);

        order.push_back(root->val);

        inorder(root->right);
    }

    vector<int> inorderTraversal(TreeNode* root)
    {
        order.clear();
        inorder(root);
        return order;
    }
};
```
如果数组order的长度不固定，就不需要初始的时候写成order(100,0),后续有需要添加的话可以通过order.push_back(x)

### LeetCode 104：二叉树的最大深度
给定一个二叉树的根节点 root，返回该树的最大深度。
最大深度是从根节点到最远叶子节点的最长路径上的节点数量。

解题思路：采用 DFS 递归。
对于每个节点，分别计算其左子树和右子树的最大深度，取两者较大值，再加上当前节点本身。
```
int maxDepth(TreeNode* root)
{
    if (root == nullptr)
        return 0;

    int left = maxDepth(root->left);
    int right = maxDepth(root->right);

    return 1 + max(left, right);
}
```
### LeetCode 226：翻转二叉树
给定一棵二叉树的根节点 root，将整棵二叉树翻转，并返回根节点。
翻转要求：交换每个节点的左子树和右子树。

解题思路:
采用 DFS 递归。
核心操作是交换当前节点的左右孩子指针，然后递归翻转左右子树。

```
class Solution
{
public:
    TreeNode* invertTree(TreeNode* root)
    {
        if (root == nullptr)
            return nullptr;

        TreeNode* temp = root->left;

        root->left = root->right;
        root->right = temp;

        invertTree(root->left);
        invertTree(root->right);

        return root;
    }
};
```
这里利用递归的时候就没有接收返回值

### LeetCode 101：对称二叉树
给定一棵二叉树的根节点 root，判断它是否轴对称。
对称的本质：左子树和右子树互为镜像。

解题思路：
采用 DFS 递归，使用两个指针同时比较左右子树。
```
class Solution
{
public:
    bool isMirror(TreeNode* left, TreeNode* right)
    {
        if (left == nullptr && right == nullptr)
            return true;

        if (left == nullptr || right == nullptr)
            return false;

        if (left->val != right->val)
            return false;

        return isMirror(left->left, right->right)
            && isMirror(left->right, right->left);
    }

    bool isSymmetric(TreeNode* root)
    {
        if (root == nullptr)
            return true;

        return isMirror(root->left, root->right);
    }
};
```

### LeetCode 543：二叉树的直径
给定一棵二叉树，计算其直径。
二叉树的直径是任意两个节点之间最长路径的边数。
注意：最长路径不一定经过根节点。

解题思路:
采用 DFS 递归。
利用第104题的思路计算每个节点的左右子树最大深度。
对于每个节点，计算经过该节点的路径长度：
当前节点的直径 = 左子树深度 + 右子树深度

最重要的是：使用**全局变量ans保存目前找到的最大直径**
```
class Solution
{
public:
    int ans = 0;

    int Depth(TreeNode* root)
    {
        if (root == nullptr)
            return 0;

        int left = Depth(root->left);
        int right = Depth(root->right);

        ans = max(ans, left + right);

        return 1 + max(left, right);
    }

    int diameterOfBinaryTree(TreeNode* root)
    {
        Depth(root);
        return ans;
    }
};
```

































