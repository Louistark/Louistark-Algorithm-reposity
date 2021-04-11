# 二叉树

![binary_tree](https://github.com/Louistark/Louistark-Algorithm-reposity/blob/main/image/%E4%BA%8C%E5%8F%89%E6%A0%91.png)

**前序遍历：F B A D C E G I H  （先根节点，再左子树，再右子树）**

**中序遍历：A B C D E F G H I  （先左子树，再根节点，再右子树）**

**后序遍历：A C E D B H I G F  （先左子树，再右子树，再根节点）**

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
```
## 遍历框架

### 递归遍历框架 ———— 前序、中序、后序遍历

```C++
class Solution {
public:

    vector<int> ans;

    // 前序递归遍历
    void DFS_preorder(TreeNode* root)
    {
        if (root==nullptr) return;

        ans.push_back(root->val);
        if (root->left!=nullptr) DFS(root->left);
        if (root->right!=nullptr) DFS(root->right);
    }

    // 中序递归遍历
    void DFS_inorder(TreeNode* root)
    {
        if (root==nullptr) return;

        if (root->left!=nullptr) DFS(root->left);
        ans.push_back(root->val);
        if (root->right!=nullptr) DFS(root->right);
    }

    // 后序递归遍历
    void DFS_postorder(TreeNode* root)
    {
        if (root==nullptr) return;

        if (root->left!=nullptr) DFS(root->left);
        if (root->right!=nullptr) DFS(root->right);
        ans.push_back(root->val);
    }

    vector<int> preorderTraversal(TreeNode* root) {
        ans.clear();
        DFS_preorder(root);
        return ans;
    }
};

    vector<int> inorderTraversal(TreeNode* root) {
        ans.clear();
        DFS_inorder(root);
        return ans;
    }
};

    vector<int> postorderTraversal(TreeNode* root) {
        ans.clear();
        DFS_postorder(root);
        return ans;
    }
};

```

### 迭代遍历框架 ———— 前序、中序、后序遍历

迭代方法思路与递归相同，只是将递归时的隐式栈显示地维护出来。

**[注] 第一次进入下一层递归，且返回还要用到当前节点时，将当前节点压入栈。若递归返回后，不需要当前节点，则当前节点不应该在栈中。**

**[注] 手撕迭代遍历时，可以把递归遍历框架先写出作为参考。**

前序遍历的迭代方法详解

- 只要 栈不为空 或 当前节点不为空 就循环迭代 （若栈底根节点被取出且其右节点为空，则遍历结束）
- 考虑当前节点是否为空，不为空则向下递归，为空则取出根节点的右节点
- 若当前节点不为空，则判断是否可以向左递归，否则就向右递归

```C++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
    
        // 和递归一样，先建立输出容器
        vector<int> ans;
        
        // 判定根节点
        if (root == nullptr) {
            return ans;
        }

        // 建立显式栈模拟递归的栈
        stack<TreeNode*> stk;
        
        // 建立当前节点容器模拟递归时的当前节点
        TreeNode* cur = root;
        
        // 当递归栈不为空或当前节点不为空时进行模拟递归调用
        // 若递归栈为空，表明根节点被取出，cur=root->right
        // 若当前节点为空，cur=stk.top()->right
        while (!stk.empty() || cur!=nullptr)
        {
            if (cur!=nullptr) // 先考虑当前节点不为空
            {
                // 前序遍历，根节点先输出
                ans.emplace_back(cur->val);
                
                // 再判断是否可以向左递归
                if (cur->left!=nullptr)
                {
                    stk.emplace(cur);
                    cur = cur->left;
                }
                else // 不能向左递归，则向右递归
                {
                    cur = cur->right;
                }
            } 
            else // 当前节点为空
            {
                cur = stk.top()->right;
                stk.pop();
            }
        }
        return ans;
    }
};
```

中序遍历的迭代方法和前序遍历的区别在于

前序遍历在查找左子树之**前**把根节点压入输出容器

中序遍历在查找完左子树之**后**把根节点压入输出容器

```C++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        if (root==nullptr) return ans;

        stack<TreeNode*> stk;
        TreeNode* cur = root;

        while (!stk.empty() || cur!=nullptr)
        {
            while (cur!=nullptr)
            {
                stk.emplace(cur);
                cur = cur->left;
            }
            cur = stk.top();
            stk.pop();
            
            // 中序遍历在查找完左子树后将根节点放入输出容器
            ans.emplace_back(cur->val);
            
            cur = cur->right;
        }
        return ans;
    }
};
```

后序遍历与前两者的区别在于

后序遍历每次查找完左子树后将栈顶非空节点取出查找一下右子树

确认右子树为空或刚被访问 才会将根节点压入输出容器

```C++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> ans;
        if (root==nullptr) return ans;

        stack<TreeNode*> stk;
        TreeNode* cur = root;
        TreeNode* pre = nullptr;

        while (!stk.empty() || cur!=nullptr)
        {
            // 查找左子树至底部
            while (cur!=nullptr)
            {
                stk.emplace(cur);
                cur = cur->left;
            }
            
            // 取出栈顶非空节点
            cur = stk.top();
            stk.pop();
            
            // 若右子树非空且未被访问 则压入一个右子树（然后继续查找其左子树至底部）
            if (cur->right!=nullptr && cur->right!=pre)
            {
                stk.emplace(cur);
                cur = cur->right;
            }
            else // 直到出现无左子树且无右子树或右子树刚被访问时，才将根节点压入输出容器
            {
                ans.emplace_back(cur->val);
                pre = cur;
                cur = nullptr;
            }
        }
        return ans;
    }
};
```

### Morris遍历框架 ———— 前序、中序、后序遍历

**Morris前序遍历详解**

利用二叉树底部闲置的空指针指向根节点来建立线索，以节约栈空间：

1. 建立两个指针，p1为当前根节点，p2用来寻找p1的前驱节点的最右子树。

2. 将p1压入输出容器后，p2指向原根节点，p1则转换为自己的左子树，循环往复。

**[注]** 出现以下两种情况时，进入右子树

- 当根节点的前驱节点的右子树指向根节点时，剪断线索，防止死循环。

- 当根节点无前驱节点时，将根节点压入输出容器。

```C++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode *root) {
    
        vector<int> res;
        if (root == nullptr) return res;

        // 指针p1为根节点，
        TreeNode *p1 = root, *p2 = nullptr;

        // 若根节点不为空，进入循环
        while (p1 != nullptr) {
        
            // p2 指向 p1 的前驱节点（左子树）
            p2 = p1->left;
            if (p2 != nullptr) 
            {
                // 一直找到 p2 节点的最右子节点（若无右节点则不进行这一步）
                while (p2->right != nullptr && p2->right != p1) 
                {
                    p2 = p2->right;
                }
                if (p2->right == nullptr) 
                {
                    // 将根节点的值放入输出容器
                    res.emplace_back(p1->val);

                    // 将根节点前驱节点的最右子节点的右子树指向根节点（建立线索）
                    p2->right = p1;

                    // 下一个根节点设置为根节点的左子树，重新开始大循环
                    p1 = p1->left;
                    continue;
                } 
                else // 当根节点的前驱节点的右子树指向根节点时进入此处
                {
                    // 防止程序在此处陷入死循环，剪断线索
                    p2->right = nullptr;
                }
            }
            else // 当根节点无前驱节点时将根节点压入输出容器
            {
                res.emplace_back(p1->val);
            }

            // 当根节点前驱节点右子树指向根节点或根节点无前驱节点时，进入右子树
            p1 = p1->right;
        }
        return res;
    }
};
```

## 递归框架
———— 以公共祖先搜索为例

```C++
// 有返回值的DFS在递归返回过程中解决子问题
// 无返回值的DFS通过单次遍历在全局变量中记录数据
bool DFS(TreeNode* root, TreeNode* p, TreeNode* q) {

    // 定义递归退出条件
    if (root==nullptr) return false;

    // 设置向下递归入口
    bool left_son = DFS(root->left, p, q);
    bool right_son = DFS(root->right, p, q);
    bool self = (root->val==p->val) || (root->val==q->val);

    // 全局问题拦截判断
    if ( !Ancestor && ((left_son&&right_son) || (left_son&&self) || (self&&right_son)) )
        Ancestor = root;

    // 解决递归子问题
    if (left_son || right_son || self)
        return true;
    else 
        return false;
}
```

### 104.二叉树的最大深度

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

- DFS深度优先搜索递归方法
```C++
class Solution {
public:
    int maxDepth(TreeNode* root) {
    
        // 判断当前根节点是否为空
        // 递归至二叉树底部时空指针时返回基础深度0
        if (root == nullptr) return 0;
        
        // 该子树最大深度 为其 左子树 和 右子树 深度最大值 +1
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

- BFS广度优先搜索方法
```C++
class Solution {
public:
    int maxDepth(TreeNode* root) {
    
        // 判断根节点是否为空
        if ( root == nullptr ) return 0;
        
        // 先创建输出
        int ans = 0;
        
        // 创建搜索队列并压入第一层（根节点）
        queue<TreeNode*> Q;
        Q.push(root);
        
        // 若搜索队列不为空 则逐个取出队列最前端的节点 并压入该节点的左右子节点
        // sz表示当前搜索层的剩余节点数 当sz归零表示一层搜索结束
        // 每搜索完一层 将下一层的节点总数赋值给sz
        // 直到下一层节点总数为零 即搜索完最后一层时 退出循环
        while ( !Q.empty() )
        {
            int sz = Q.size();
            while ( sz != 0 )
            {
                TreeNode* cur = Q.front();
                Q.pop();
                sz--;
                if ( cur->left != nullptr )
                    Q.push(cur->left);
                if ( cur->right != nullptr )
                    Q.push(cur->right);
            }
            ans++;
        }
        return ans;
    }
};
```

### 110.平衡二叉树

- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：
一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 。

**解题思路：**

每个平衡二叉树的左右子树都必须是平衡二叉树

- DFS递归
```C++
class Solution {
public:
    // 检测当前子树最大深度
    int maxDepth(TreeNode* root) {
        if (root==nullptr) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
    // 平衡判定的递归函数
    bool isBalanced(TreeNode* root) {
    
        // 空子树是平衡二叉树
        if (root==nullptr) return true;
        
        // 平衡二叉树的充要条件：
        // 1.左子树是平衡二叉树
        // 2.右子树是平衡二叉树
        // 3.左右子树高度差不能大于1
        if (!isBalanced(root->left) || !isBalanced(root->right) || (abs(maxDepth(root->left)-maxDepth(root->right))>1)) 
            return false;
        else
            return true;
    }
};
```

### 124.二叉树中最大路径和

- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。

路径和 是路径中各节点值的总和。

给你一个二叉树的根节点 root ，返回其 最大路径和 。

**解题思路：**

将每一个节点视为一个通道，其路径值可以有三种情况：根节点、根节点+左通道最大路径值、根节点+右通道最大路径值。

每个节点的最大路径和有四种情况：根节点、根节点+左通道最大路径值、根节点+右通道最大路径值、根节点+左通道最大路径值+右通道最大路径值。

建立一个全局变量“最大路径和”，初始值设为 INT_MIN 

建立一个通道最大路径和的递归函数，计算每个节点的最大路径和，更新全局最大路径和，并返回该节点作为通道的最大路径值。

建立一个全局最大路径和函数，调用通道最大路径和函数计算根节点开始往下所有节点的最大路径和，返回全局最大路径和。

```C++
class Solution {
public:
    
    // 全局最大路径和 初始值为int型的最小值
    int ans = INT_MIN;

    // 通道最大路径值的递归函数
    int maxPath(TreeNode* root) {

        // 空节点的最大通道路径值为0
        if (root==nullptr) return 0;

        // 计算左右通道的最大路径值 若是负数 则写0
        int left_sum = max(0, maxPath(root->left));
        int right_sum = max(0, maxPath(root->right));
        
        // 计算当前节点的最大路径和 并更新全局最大路径和
        int cur_maxpathsum = root->val + left_sum + right_sum;
        ans = max(ans, cur_maxpathsum);

        // 返回当前节点作为通道的最大路径值
        return max((root->val+left_sum), (root->val+right_sum));
    }
    
    // 全局最大路径和函数
    int maxPathSum(TreeNode* root) {
        
        // 从根节点计算其下每个节点的最大路径和
        PathSum(root);

        // 返回更新后的全局最大路径和
        return ans;
    }
};
```

### 236.二叉树的最近公共祖先

- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

**解题思路一：**

最近公共祖先的充分必要条件：

左子树的分支中 或 右子树的分支中 或 该节点本身，三者中有且仅有两者包含p或q。

因此，从二叉树的底部向上查找，寻找满足条件的节点

```C++
class Solution {
public:
    
    // 声明全局变量公共祖先
    TreeNode* Ancestor = nullptr;

    // 深度优先搜索，递归，从下往上逐个节点判断
    // 左子树 右子树 自身 是否包含 p 或 q，满足一条则返回true
    // 逐个节点判断 是否是最近公共祖先 若是 则最近公共祖先产生
    bool DFS(TreeNode* root, TreeNode* p, TreeNode* q) {

        if (root==nullptr) return false;

        bool left_son = DFS(root->left, p, q);
        bool right_son = DFS(root->right, p, q);
        bool self = (root->val==p->val) || (root->val==q->val);

        if ( !Ancestor && ((left_son&&right_son) || (left_son&&self) || (self&&right_son)) )
            Ancestor = root;

        if (left_son || right_son || self)
            return true;
        else 
            return false;
    }
    
    // 全局问题输出函数
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    
        // 从根部调用深度优先搜索，在搜索中解决全局问题
        DFS(root,p,q);

        // 输出全局问题结果
        return Ancestor;
    }
};
```

**解题思路二：**

进行一次深度优先搜索，用散列表记录所有节点的父节点

从下往上遍历一次 P 节点的所有祖先节点，并记录在 visited 散列表中

依次遍历 Q 节点所有的祖先节点，若发现已记录于 visited 中 则为最近祖先

```C++
class Solution {
public:

    // 建立父节点散列表 和 visited散列表
    unordered_map<int, TreeNode*> father;
    unordered_map<int, bool> visited;

    // 深度优先搜索递归遍历所有节点，记录其父节点，故无需返回值
    void DFS(TreeNode* root)
    {
        if (root->left != nullptr)
        {
            father[root->left->val] = root;
            DFS(root->left);
        }
        if (root->right != nullptr)
        {
            father[root->right->val] = root;
            DFS(root->right);
        }
    }
        
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    
        // 记录根节点父节点为空，为后续遍历创造退出条件
        father[root->val] = nullptr;
        
        // 调用一遍DFS记录所有节点父节点
        DFS(root);

        // 遍历 P 的所有祖先节点并记录
        while (p!=nullptr)
        {
            visited[p->val] = true;
            p = father[p->val];
        }
        
        // 从下往上遍历 Q 的祖先节点，逐个排查
        while (q!=nullptr)
        {
            if (visited[q->val]) return q;
            q = father[q->val];
        }
        
        // 若无祖先节点 返回空（不存在的情况，但是函数需要返回值）
        return nullptr;
    }
};
```

### 102.二叉树的层序遍历

- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

**BFS广度优先搜索**

```C++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
    
        // 定义输出变量
        vector<vector<int>> ans;
        
        // 检查根节点
        if (root==nullptr) return ans;
        
        // 创建等待队列
        queue<TreeNode*> Q;
        Q.push(root);

        // 遍历结束条件：等待队列为空
        while (!Q.empty())
        {
            // 计算当前层的 size
            int sz = Q.size();
            
            // 创建当前层的输出数组
            vector<int> cur_level;
            
            // 若当前层未空，持续输出当前层，并将下一层加入等待队列
            while (sz>0)
            {
                TreeNode* cur = Q.front();
                Q.pop();
                if (cur->left!=nullptr) Q.push(cur->left);
                if (cur->right!=nullptr) Q.push(cur->right);
                cur_level.push_back(cur->val);
                
                // 注意：每次循环，当前层 size 减一
                sz--;
            }
            ans.push_back(cur_level);
        }
        return ans;
    }
};
```

### 107.二叉树的层序遍历-II

- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

**BFS广度优先搜索**

```C++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
    
        // 定义输出变量
        vector<vector<int>> ans;
        
        // 检查根节点
        if (root==nullptr) return ans;
        
        // 创建等待队列
        queue<TreeNode*> Q;
        Q.push(root);

        // 遍历结束条件：等待队列为空
        while (!Q.empty())
        {
            // 计算当前层的 size
            int sz = Q.size();
            
            // 创建当前层的输出数组
            vector<int> cur_level;
            
            // 若当前层未空，持续输出当前层，并将下一层加入等待队列
            while (sz>0)
            {
                TreeNode* cur = Q.front();
                Q.pop();
                if (cur->left!=nullptr) Q.push(cur->left);
                if (cur->right!=nullptr) Q.push(cur->right);
                cur_level.push_back(cur->val);
                
                // 注意：每次循环，当前层 size 减一
                sz--;
            }
            ans.push_back(cur_level);
        }
        
        // 从下往上层序遍历，仅需将结果翻转即可
        reverse(ans.begin(), ans.end());
        
        return ans;
    }
};
```

### 103.二叉树的锯齿形层序遍历

- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

**BFS广度优先搜索**

```C++
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
    
        // 定义输出变量
        vector<vector<int>> ans;
        
        // 检查根节点
        if (root==nullptr) return ans;
        
        // 创建等待队列
        queue<TreeNode*> Q;
        Q.push(root);

        int level_cnt = 1;
            
        // 遍历结束条件：等待队列为空
        while (!Q.empty())
        {
            // 计算当前层的 size
            int sz = Q.size();
            
            // 创建当前层的输出数组
            vector<int> cur_level;

            // 若当前层未空，持续输出当前层，并将下一层加入等待队列
            while (sz>0)
            {
                TreeNode* cur = Q.front();
                Q.pop();
                if (cur->left!=nullptr) Q.push(cur->left);
                if (cur->right!=nullptr) Q.push(cur->right);
                cur_level.push_back(cur->val);
                
                // 注意：每次循环，当前层 size 减一
                sz--;
            }

            // 在每次推入该层结果前进行判断，偶数层则翻转
            if ((++level_cnt)%2)  reverse(cur_level.begin(), cur_level.end());
            
            ans.push_back(cur_level);
        }
        return ans;
    }
};
```
