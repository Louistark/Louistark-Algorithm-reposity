# 二叉树

### 104.二叉树的最大深度
-  [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

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
