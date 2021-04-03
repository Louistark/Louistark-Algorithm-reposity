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

**解题思路：**每个平衡二叉树的左右子树都必须是平衡二叉树

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

    int ans = INT_MIN;

    int PathSum(TreeNode* root) {

        if (root==nullptr) return 0;

        int left_sum = max(0, PathSum(root->left));
        int right_sum = max(0, PathSum(root->right));
        
        int cur_maxpathsum = root->val + left_sum + right_sum;
        ans = max(ans, cur_maxpathsum);

        return max((root->val+left_sum), (root->val+right_sum));
    }

    int maxPathSum(TreeNode* root) {

        PathSum(root);

        return ans;
    }
};
```
