---
layout: post
title: LeetCode Top 100 Liked (四)
categories: [LeetCode]
description: LeetCode Top 100 Liked (四)
keywords: LeetCode, Algorithms, 算法
---

#### 31. Minimum Window Substring

> Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).
>
> **Example:**
>
> ```
> Input: S = "ADOBECODEBANC", T = "ABC"
> Output: "BANC"
> ```
>
> **Note:**
>
> - If there is no such window in S that covers all characters in T, return the empty string `""`.
> - If there is such window, you are guaranteed that there will always be only one unique minimum window in S.

##### 思路：

这一题的难度很大，采取了"滑动窗口"的特殊策略。首先将待匹配字符串中的所有字符，按照个数存储在哈希表中，遍历原始字符串，匹配一个，就将对应的字符个数减少，如果全部匹配好，就可以考虑移动左边界

##### 解法：

```c++
class Solution {
public:
    string minWindow(string s, string t) 
    {
        if (s.empty() || t.empty())
        {
            return "";
        }
        string res = "";
        unordered_map<char, int> letterCnt;
        int left = 0, cnt = 0, minLen = INT_MAX;
        // 计算每个字符出现的次数
        for (auto c : t)
        {
            ++letterCnt[c];
        }
        for (int i = 0; i < s.size(); ++i)
        {
            // 出现过的字符减去
            if (--letterCnt[s[i]] >= 0)
            {
                ++cnt;
            }
            // 所有字符都已经找到
            while (cnt == t.size())
            {
                if (minLen > i - left + 1)
                {
                    minLen = i - left + 1;
                    res = s.substr(left, minLen);
                }
                // 左边界右移
                // 有字符未匹配
                if (++letterCnt[s[left]] > 0)
                {
                    --cnt;
                }
                ++left;
            }
        }
        return res;
    }
};
```

#### 32. Subsets

> Given a set of **distinct** integers, *nums*, return all possible subsets (the power set).
>
> **Note:** The solution set must not contain duplicate subsets.
>
> **Example:**
>
> ```
> Input: nums = [1,2,3]
> Output:
> [
>   [3],
>   [1],
>   [2],
>   [1,2,3],
>   [1,3],
>   [2,3],
>   [1,2],
>   []
> ]
> ```

##### 思路：

依旧是递归的思路，将中间变量传递到递归函数中，在函数内部解决，对于数组的子集而言，无非就是元素是否存在，两种情形，按照顺序递归

递归的顺序就是

```
[]
1
1 2
1 2 3
2 
2 3
3
```



##### 解法：

```c++
class Solution {
public:
    void helper(int start, vector<int> last, vector<int> &nums, vector<vector<int>> &res)
    {
        res.push_back(last);
        for (int i = start; i < nums.size(); ++i)
        {
            last.push_back(nums[i]);
            helper(i+1, last, nums, res);
            last.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        
        if (nums.empty())
        {
            res.push_back({});
            return res;
        }
        sort(nums.begin(), nums.end());
        helper(0, {}, nums, res);
        return res;
    }
};
```

#### 33. Word Search

> Given a 2D board and a word, find if the word exists in the grid.
>
> The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.
>
> **Example:**
>
> ```
> board =
> [
>   ['A','B','C','E'],
>   ['S','F','C','S'],
>   ['A','D','E','E']
> ]
> 
> Given word = "ABCCED", return true.
> Given word = "SEE", return true.
> Given word = "ABCB", return false.
> ```

##### 思路：

 对于这类矩阵搜索的问题，一般都是"DFS"解决。免不了要使用到递归，因为一个位置只能去一次，因此要标记，没必要使用额外的数据，就地临时更改即可，因为是递归，因此递归终止的条件就需要额外注意

##### 解法：

```c++
class Solution {
public:
    bool dfs(int start, int row, int col, vector<vector<char>> &board, string word)
    {
        if (start == word.size())
        {
            return true;
        }
        int m = board.size(),
            n = board[0].size();
        if (row < 0 || row >= m || col < 0 || col >= n || board[row][col] != word[start])
        {
            return false;
        }
        char saveChar = board[row][col] ;
        board[row][col] = '#';
        bool res = dfs(start+1, row+1, col, board, word) ||
            dfs(start+1, row-1, col, board, word) ||
            dfs(start+1, row, col+1, board, word) ||
            dfs(start+1, row, col-1, board, word);
        board[row][col] = saveChar;
        return res;
    }
    bool exist(vector<vector<char>>& board, string word) {
        if (board.empty() || board[0].empty() || word.empty())
        {
            return false;
        }
        int m = board.size(),
            n = board[0].size();
        for (int i = 0; i < m; ++i)
        {
            for (int j = 0; j < n; ++j)
            {
                if (dfs(0, i, j, board, word))
                {
                    return true;
                }
            }
        }
        return false;
    }
};
```

#### 34. Largest Rectangle in Histogram

> Given *n* non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.
>
>  
>
> ![img](https://assets.leetcode.com/uploads/2018/10/12/histogram.png)
> Above is a histogram where width of each bar is 1, given height = `[2,1,5,6,2,3]`.
>
>  
>
> ![img](https://assets.leetcode.com/uploads/2018/10/12/histogram_area.png)
> The largest rectangle is shown in the shaded area, which has area = `10` unit.
>
>  
>
> **Example:**
>
> ```
> Input: [2,1,5,6,2,3]
> Output: 10
> ```

##### 思路：

一般来说，困难的题，如果思路对了，写起来是很简单的。这一题暴力法自然是不可取的，寻找最大矩形，不仅要保证区间内最小高度比较大，而且尽量让宽度大。一个思路是寻找一个局部峰值，然后往前遍历，寻找局部峰值，是否了减少计算量：因为局部峰值的存在，因此如果前面的矩形构成的面积比较大，那么一定可以将峰值所在矩形添加上去，于是构成更大的一个矩形，所有局部峰值之前的面积就没有必要计算，即便是计算，也是浪费，因为不可能比加上局部峰值构成的矩阵更大

##### 解法：

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        if (heights.size() == 0)
        {
            return 0;
        }
        int maxArea = 0;
        for (int i = 0; i < heights.size(); ++i)
        {
            // 寻找局部峰值å
            if (i + 1 < heights.size() && heights[i] <= heights[i+1])
            {
                continue;
            }
            int minH = heights[i];
            // 向前遍历
            for (int j = i; j >= 0; --j)
            {
                // 寻找最小的高
                minH = min(minH, heights[j]);
                int area = minH * (i - j + 1);
                maxArea = max(area, maxArea);
            }
        }
        return maxArea;
    }
};
```

#### 35. Binary Tree Inorder Traversal

> Given a binary tree, return the *inorder* traversal of its nodes' values.
>
> **Example:**
>
> ```
> Input: [1,null,2,3]
>    1
>     \
>      2
>     /
>    3
> 
> Output: [1,3,2]
> ```
>
> **Follow up:** Recursive solution is trivial, could you do it iteratively?

##### 思路：

 二叉树的中序遍历，经典的问题，递归的方法简洁明白，题目要求是用循环，复杂一些。考虑中序遍历事左-根-右的顺序，因此需要一个辅助栈，先往左遍历到叶节点，然后弹出，同时转向右子树

##### 解法：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        if (!root)
        {
            return res;
        }
        TreeNode *p = root;
        stack<TreeNode *> st;
        while (p || !st.empty())
        {
            while (p)
            {
                st.push(p);
                p = p->left;
            }
            if (!st.empty())
            {
                p = st.top();
                st.pop();
                res.push_back(p->val);
                p = p->right;
            }
        }
        return res;
    }
};
```

#### 36. Unique Binary Search Tree

> Given *n*, how many structurally unique **BST's** (binary search trees) that store values 1 ... *n*?
>
> **Example:**
>
> ```
> Input: 3
> Output: 5
> Explanation:
> Given n = 3, there are a total of 5 unique BST's:
> 
>    1         3     3      2      1
>     \       /     /      / \      \
>      3     2     1      1   3      2
>     /     /       \                 \
>    2     1         2                 3
> ```

##### 思路：

这一题我是根本想不出来，参考网上的解法。思路大体是，借助一个辅助数组`dp`，`dp[i]`存储的是`i`个节点构成BST的个数，既然是n个数，那么所有的可能就是左子树的数量与右子树数量的乘积，比如说：

```
dp(3) = dp(0)*dp(2) + dp(1)*dp(1) + dp(2)*dp(0)
```

答案就呼之欲出了，这个公式就是著名的`卡塔兰数`

##### 解法：

```c++
class Solution {
public:
    int numTrees(int n)
    {
        if (n < 0)
        {
            return 0;
        }
        vector<int> dp(n+1, 0);
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; ++i)
        {
            for (int j = 0; j < i; ++j)
            {
                dp[i] += dp[j] * dp[i-j-1];
            }
        }
        return dp[n];
    }
};
```

#### 37. Validate Binary Search Tree

> Given a binary tree, determine if it is a valid binary search tree (BST).
>
> Assume a BST is defined as follows:
>
> - The left subtree of a node contains only nodes with keys **less than**the node's key.
> - The right subtree of a node contains only nodes with keys **greater than** the node's key.
> - Both the left and right subtrees must also be binary search trees.
>
>  
>
> **Example 1:**
>
> ```
>     2
>    / \
>   1   3
> 
> Input: [2,1,3]
> Output: true
> ```
>
> **Example 2:**
>
> ```
>     5
>    / \
>   1   4
>      / \
>     3   6
> 
> Input: [5,1,4,null,null,3,6]
> Output: false
> Explanation: The root node's value is 5 but its right child's value is 4.
> ```

##### 思路：

二叉查找树有特点，左子树都比根节点值小，右子树都比根节点值大。自然是递归的思路，问题在于，要将区间的范围传递到递归函数中，因此需要一个辅助函数。实现的时候范围区间类型定义为`int`，其实应该是越大越好，比如`long`

##### 解法：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isValidBST(TreeNode *node, long minVal, long maxVal)
    {
        if (!node)
        {
            return true;
        }
        if (node->val <= minVal || node->val >= maxVal)
        {
            return false;
        }
        return isValidBST(node->left, minVal, node->val) &&
                isValidBST(node->right, node->val, maxVal);
        
    }
    bool isValidBST(TreeNode* root) {
        return isValidBST(root, LONG_MIN, LONG_MAX);
    }
};
```

#### 38. Symmetric Tree

> Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).
>
> For example, this binary tree `[1,2,2,3,4,4,3]` is symmetric:
>
> ```
>     1
>    / \
>   2   2
>  / \ / \
> 3  4 4  3
> ```
>
>  
>
> But the following `[1,2,2,null,3,null,3]` is not:
>
> ```
>     1
>    / \
>   2   2
>    \   \
>    3    3
> ```
>
>  
>
> **Note:**
> Bonus points if you could solve it both recursively and iteratively.

##### 思路：

依然是递归，依然需要一个辅助函数，因为对称，需要的是两个节点的信息：

首先节点处的值要想等，其次，左子树的左子树，与右子树的右子树，以及左子树的右子树，和右子树的左子树，同时满足对称，在这个高度上，树才是对称的

##### 解法：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSymmetric(TreeNode *left, TreeNode *right) {
        if (!left && !right)
        {
            return true;
        }
        if (left && right)
        {
            if (left->val != right->val)
            {
                return false;
            }
            return isSymmetric(left->left, right->right)
                    && isSymmetric(left->right, right->left);
        }
        return false;
        
    }
    bool isSymmetric(TreeNode* root) {
        if (!root)
        {
            return true;
        }
        return isSymmetric(root->left, root->right);
    }
};
```

#### 39. Binary Level Order Traversal

> Given a binary tree, return the *level order* traversal of its nodes' values. (ie, from left to right, level by level).
>
> For example:
> Given binary tree `[3,9,20,null,null,15,7]`,
>
> ```
>     3
>    / \
>   9  20
>     /  \
>    15   7
> ```
>
> 
>
> return its level order traversal as:
>
> ```
> [
>   [3],
>   [9,20],
>   [15,7]
> ]
> ```

##### 思路：

这一题很有意思，是层次遍历，都知道是要用"队列"，但是这里要求按层输出，层的信息如何体现？有个不错的想法，用一个变量，记录下一层节点的数目，遍历的时候，用一个内循环，将该层的所有节点取出，同时将下一层节点放入队列，并计数。内循环就维护了层的信息

##### 解法：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root)
        {
            return res;
        }
        queue<TreeNode *> q;
        q.push(root);
        int count = 1;
        while (!q.empty())
        {
            int currentCnt = count;
            vector<int> temp;
            count = 0;
            for (int i = 0; i < currentCnt; ++i)
            {
                TreeNode *node = q.front();
                q.pop();
                temp.push_back(node->val);
                if (node->left)
                {
                    ++count;
                    q.push(node->left);
                }
                if (node->right)
                {
                    ++count;
                    q.push(node->right);
                }
            }
            res.push_back(temp);
        }
        return res;
    }
};
```

#### 40. Maximum Depth of Binary Tree

> Given a binary tree, find its maximum depth.
>
> The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.
>
> **Note:** A leaf is a node with no children.
>
> **Example:**
>
> Given binary tree `[3,9,20,null,null,15,7]`,
>
> ```
>     3
>    / \
>   9  20
>     /  \
>    15   7
> ```

##### 思路：

递归的绝妙应用，代码简洁到只有几行，却将问题解决。清楚，明白，有效

##### 解法：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root)
        {
            return 0;
        }
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

