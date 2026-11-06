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
















