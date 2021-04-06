# 二叉树

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
