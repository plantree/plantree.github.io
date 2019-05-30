---
layout: post
title: LeetCode Top 100 Liked (三)
categories: [LeetCode]
description: LeetCode Top 100 Liked (三)
keywords: LeetCode, Algorithms, 算法
---

#### 21. Rotate Image

> You are given an *n* x *n* 2D matrix representing an image.
>
> Rotate the image by 90 degrees (clockwise).
>
> **Note:**
>
> You have to rotate the image [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm), which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.
>
> **Example 1:**
>
> ```
> Given input matrix = 
> [
>   [1,2,3],
>   [4,5,6],
>   [7,8,9]
> ],
> 
> rotate the input matrix in-place such that it becomes:
> [
>   [7,4,1],
>   [8,5,2],
>   [9,6,3]
> ]
> ```
>
> **Example 2:**
>
> ```
> Given input matrix =
> [
>   [ 5, 1, 9,11],
>   [ 2, 4, 8,10],
>   [13, 3, 6, 7],
>   [15,14,12,16]
> ], 
> 
> rotate the input matrix in-place such that it becomes:
> [
>   [15,13, 2, 5],
>   [14, 3, 4, 1],
>   [12, 6, 8, 9],
>   [16, 7,10,11]
> ]
> ```

##### 思路：

这一题是个脑筋急转弯，主要是寻找旋转90度之后，对应的四个数，有什么规律。两层循环是不可避免的，循环的边界要格外注意，旋转以行为单位，每遍历一行，剩下需要旋转的就变小了。对应的四个数，在行、列以及矩阵大小之间存在某种规律

##### 解法：

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        if (matrix.empty() || matrix[0].empty())
        {
            return;
        }
        int m = matrix.size();
        int n = matrix[0].size();
        if (m != n)
        {
            return;
        }
        for (int i = 0; i < m/2; ++i)
        {
            for (int j = i; j < m-1-i; ++j)
            {
                int t = matrix[i][j];
                matrix[i][j] = matrix[m-1-j][i];
                matrix[m-1-j][i] = matrix[m-1-i][m-1-j];
                matrix[m-1-i][m-1-j] = matrix[j][m-1-i];
                matrix[j][m-1-i] = t;
            }
        }
    }
};
```

#### 22. Group Anagrams

> Given an array of strings, group anagrams together.
>
> **Example:**
>
> ```
> Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
> Output:
> [
>   ["ate","eat","tea"],
>   ["nat","tan"],
>   ["bat"]
> ]
> ```
>
> **Note:**
>
> - All inputs will be in lowercase.
> - The order of your output does not matter.

##### 思路：

这一题的解题思路还是明确的。首先要把握好共性：无论字符串怎么变，字符就那么多，立马想到排序，这是最简单的不变量。接着，就考虑用哈希表，一个`unordered_map`，映射排序后的键到一个`vector`里，最后遍历一遍即可

##### 解法：

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        if (strs.empty())
        {
            return res;
        }
        unordered_map<string, vector<string>> um;
        for (auto &str : strs)
        {
            string temp = str;
            sort(temp.begin(), temp.end());
            um[temp].push_back(str);
        }
        for (auto item : um)
        {
            res.push_back(item.second);
        }
        return res;
    }
};
```

#### 23. Maximum Subarray

> Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.
>
> **Example:**
>
> ```
> Input: [-2,1,-3,4,-1,2,1,-5,4],
> Output: 6
> Explanation: [4,-1,2,1] has the largest sum = 6.
> ```
>
> **Follow up:**
>
> If you have figured out the O(*n*) solution, try coding another solution using the divide and conquer approach, which is more subtle.

##### 思路：

最大子列和的问题，可以说是动态规划的经典题型。`O(n)`的时间复杂度，只需遍历一次，记住一点，如果中间某个子列和为负，说明该清零了，因为它只会对后面的数产生负面影响，不要也罢。可以尝试分治，有难度

##### 解法：

```c++
// solution 1
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.empty())
        {
            return 0;
        }
        int maxSum = INT_MIN, sum = 0;
        for (int i= 0; i < nums.size(); ++i)
        {
            sum += nums[i];
            if (sum > maxSum)
            {
                maxSum = sum;
            }
            if (sum < 0)
            {
                sum = 0;
            }
        }
        return maxSum;
    }
};

// solution 2
class Solution {
public:
    int maxSubArray(vector<int> &nums, int left, int right)
    {
        int maxLeftSum, maxRightSum;
        int maxLeftBorderSum, maxRightBorderSum;
        int leftBorderSum, rightBorderSum;
        int mid, i;
        
        if (left == right)
        {
            return nums[left];
        }
        
        mid = left + (right - left)/2;
        maxLeftSum = maxSubArray(nums, left, mid);
        maxRightSum = maxSubArray(nums, mid+1, right);
        
        maxLeftBorderSum = INT_MIN, leftBorderSum = 0;
        for (i = mid; i >= left; --i)
        {
            leftBorderSum += nums[i];
            if (leftBorderSum > maxLeftBorderSum)
            {
                maxLeftBorderSum = leftBorderSum;
            }
        }
        
        maxRightBorderSum = INT_MIN, rightBorderSum = 0;
        for (i = mid+1; i <= right; ++i)
        {
            rightBorderSum += nums[i];
            if (rightBorderSum > maxRightBorderSum)
            {
                maxRightBorderSum = rightBorderSum;
            }
        }
        return max({maxLeftSum, maxRightSum, maxLeftBorderSum+maxRightBorderSum});
    }
    
    int maxSubArray(vector<int>& nums) {
        if (nums.empty())
        {
            return 0;
        }
        return maxSubArray(nums, 0, nums.size()-1);
    }
};
```

#### 24. Jump Game

> Given an array of non-negative integers, you are initially positioned at the first index of the array.
>
> Each element in the array represents your maximum jump length at that position.
>
> Determine if you are able to reach the last index.
>
> **Example 1:**
>
> ```
> Input: [2,3,1,1,4]
> Output: true
> Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
> ```
>
> **Example 2:**
>
> ```
> Input: [3,2,1,0,4]
> Output: false
> Explanation: You will always arrive at index 3 no matter what. Its maximum
>              jump length is 0, which makes it impossible to reach the last index.
> ```

##### 思路：

这一题可以考虑动态规划，但是更直接的，是贪心算法。有两个地方需要注意，一个是能抵达的最远距离，但是有个前提，必须要先抵达此刻的位置，因此只需要维护一个变量，存储当前所能抵达的最远距离，如果遍历到下一个位置，返现比最远距离要大，说明无法抵达，返回`false`。动态规划维护一个状态数组，记录所能抵达的位置，如果该位置无法抵达，则直接退出

##### 解法：

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        if (nums.empty())
        {
            return true;
        }
        int len = nums.size();
        // dp
        vector<bool> dp(nums.size(), false);
        dp[0] = true;
        for (int i = 0; i < nums.size(); ++i) {
            if (dp[i] == true) {
                int step = nums[i];
                for (int j = 1; j <= step; ++j) {
                    if (i + j >= len)
                    {
                        return true;
                    }
                    dp[i+j] = true;
                }
            } else {
                return false;
            }
        }
        return dp[nums.size()-1];
        
        
        // greedy
        /*
        int reach = 0;
        for (int i = 0; i < len; ++i)
        {
            if (i > reach || reach >= len-1)
            {
                break;
            }
            reach = max(reach, i+nums[i]);
        }
        return (reach >= len-1);*/
    }
};
```

#### 25. Merge Intervals

> Given a collection of intervals, merge all overlapping intervals.
>
> **Example 1:**
>
> ```
> Input: [[1,3],[2,6],[8,10],[15,18]]
> Output: [[1,6],[8,10],[15,18]]
> Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
> ```
>
> **Example 2:**
>
> ```
> Input: [[1,4],[4,5]]
> Output: [[1,5]]
> Explanation: Intervals [1,4] and [4,5] are considered overlapping.
> ```
>
> **NOTE:** input types have been changed on April 15, 2019. Please reset to default code definition to get new method signature.

##### 思路：

这一题虽不至于很难，但是细节要注意。这里是区间，首先肯定要按首位排序，然后比较后面的数的首位，和前一个区间末位，是否合并就在这里。如果可以合并，还需要注意后面的区间末位是完全有可能比前一个区间末位要小的

##### 解法：

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        if (intervals.empty() || intervals[0].empty())
        {
            return res;
        }
        sort(intervals.begin(), intervals.end(), [](vector<int> &a, vector<int> &b) {
            return a[0] < b[0];   
        });
        int len = intervals.size();
        int base = 0;
        for (int i = 1; i < len; ++i)
        {
            if (intervals[i][0] <= intervals[base][1])
            {
                intervals[base][1] = max(intervals[i][1], intervals[base][1]);
            }
            else 
            {
                res.push_back(intervals[base]);
                base = i;
            }
        }
        res.push_back(intervals[base]);
        return res;
    }
};
```

#### 26. Unique Paths

> A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).
>
> The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
>
> How many possible unique paths are there?
>
> ![img](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)
> Above is a 7 x 3 grid. How many possible unique paths are there?
>
> **Note:** *m* and *n* will be at most 100.
>
> **Example 1:**
>
> ```
> Input: m = 3, n = 2
> Output: 3
> Explanation:
> From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
> 1. Right -> Right -> Down
> 2. Right -> Down -> Right
> 3. Down -> Right -> Right
> ```
>
> **Example 2:**
>
> ```
> Input: m = 7, n = 3
> Output: 28
> ```

##### 思路：

这一题明显就是动态规划，因为状态转移有很明显的依赖关系。首先初始化一个数组`dp[m][n]`，记录到达该位置的可能性。首先初始化第一行和第一列，肯定只有一种可能，然后遍历，状态转移公式为：

`dp[i][j]=dp[i-1][j]+dp[i][j-1]`

因为叨叨该位置的可能只能是来自左方和上方的，因此得解

##### 解法：

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        if (m < 0 || n < 0)
        {
            return 0;
        }
        // init
        vector<vector<int>> dp(m, vector<int>(n, 0));
        for (int i = 0; i < m; ++i)
        {
            dp[i][0] = 1;
        }
        for (int i = 0; i < n; ++i)
        {
            dp[0][i] = 1;
        }
        
        // move
        for (int i = 1; i < m; ++i)
        {
            for (int j = 1; j < n; ++j)
            {
                // 只能来自左边和上边
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```

#### 27. Minimum Path Sum

> Given a *m* x *n* grid filled with non-negative numbers, find a path from top left to bottom right which *minimizes* the sum of all numbers along its path.
>
> **Note:** You can only move either down or right at any point in time.
>
> **Example:**
>
> ```
> Input:
> [
>   [1,3,1],
>   [1,5,1],
>   [4,2,1]
> ]
> Output: 7
> Explanation: Because the path 1→3→1→1→1 minimizes the sum.
> ```

##### 思路：

与上一题的思路基本一致，还是动态规划，因为都是正数，因此不存在折返的情形，因此每一个位置，只有从上或者左边两个方向抵达，选择其中最小的，保持局部的最优，到达最后位置，就是全局的最优解，状态之间的递推关系还是很明显的

##### 解法：

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty())
        {
            return -1;
        }
        int m = grid.size(),
            n = grid[0].size();
        vector<vector<int>> dp(m, vector<int>(n, 0));
        dp[0][0] = grid[0][0];
        // init
        for (int i = 1; i < m; ++i)
        {
            dp[i][0] = dp[i-1][0] + grid[i][0];
        }
        for (int i = 1; i < n; ++i)
        {
            dp[0][i] = dp[0][i-1] + grid[0][i];
        }
        
        for (int i = 1; i < m; ++i)
        {
            for (int j = 1; j < n; ++j)
            {
                dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[m-1][n-1];
    }
};
```

#### 28. Climbing Stairs

> You are climbing a stair case. It takes *n* steps to reach to the top.
>
> Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
>
> **Note:** Given *n* will be a positive integer.
>
> **Example 1:**
>
> ```
> Input: 2
> Output: 2
> Explanation: There are two ways to climb to the top.
> 1. 1 step + 1 step
> 2. 2 steps
> ```
>
> **Example 2:**
>
> ```
> Input: 3
> Output: 3
> Explanation: There are three ways to climb to the top.
> 1. 1 step + 1 step + 1 step
> 2. 1 step + 2 steps
> 3. 2 steps + 1 step
> ```

##### 思路：

爬梯子也是经典的动态规划。一次可以爬一步，也可以爬两步，因此此刻的状态，就取决于之前的两步，不要忘记初始的状态设定即可

##### 解法：

```c++
class Solution {
public:
    int climbStairs(int n) {
        if (n <= 0)
        {
            return -1;
        }
        vector<int> dp(n+1, 0);
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <=n; ++i)
        {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];  
    }
};
```

#### 29. Edit Distance

> Given two words *word1* and *word2*, find the minimum number of operations required to convert *word1* to *word2*.
>
> You have the following 3 operations permitted on a word:
>
> 1. Insert a character
> 2. Delete a character
> 3. Replace a character
>
> **Example 1:**
>
> ```
> Input: word1 = "horse", word2 = "ros"
> Output: 3
> Explanation: 
> horse -> rorse (replace 'h' with 'r')
> rorse -> rose (remove 'r')
> rose -> ros (remove 'e')
> ```
>
> **Example 2:**
>
> ```
> Input: word1 = "intention", word2 = "execution"
> Output: 5
> Explanation: 
> intention -> inention (remove 't')
> inention -> enention (replace 'i' with 'e')
> enention -> exention (replace 'n' with 'x')
> exention -> exection (replace 'n' with 'c')
> exection -> execution (insert 'u')
> ```

##### 思路：

这一题相当难，虽然知道是动态规划，但是递推过程还真没有那么容易理解。数组`dp[i][j]`表示字符串1的`i`位置到字符串2的`j`位置所需要的编辑距离。初始化的时候，第一行第一列要设置好，如果某一个字符串为空，那么就是单纯插入或者删除。开始循环，如果对应位置字符相同，那么`dp[i][j]=dp[i-1][j-1]`，如果不相同，就有插入、删除或者更新三种可能，于是`dp[i][j]=min({dp[i-1][j-1], dp[i-1][j], dp[i][j-1]})+1`

##### 解法：

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int len1 = word1.size(),
            len2 = word2.size();
        vector<vector<int>> dp(len1+1, vector<int>(len2+1, 0));
        for (int i = 0; i <= len1; ++i)
        {
            dp[i][0] = i;
        }
        for (int j = 0; j <= len2; ++j)
        {
            dp[0][j] = j;
        }
        for (int i = 1; i <= len1; ++i)
        {
            for (int j = 1; j <= len2; ++j)
            {
                if (word1[i-1] == word2[j-1])
                {
                    dp[i][j] = dp[i-1][j-1];
                }
                else 
                {
                    dp[i][j] = min({dp[i-1][j-1], dp[i][j-1], dp[i-1][j]})+1;
                }
            }
        }
        return dp[len1][len2];
    }
};
```

#### 30. Sort Colors

> Given an array with *n* objects colored red, white or blue, sort them **in-place** so that objects of the same color are adjacent, with the colors in the order red, white and blue.
>
> Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.
>
> **Note:** You are not suppose to use the library's sort function for this problem.
>
> **Example:**
>
> ```
> Input: [2,0,2,1,1,0]
> Output: [0,0,1,1,2,2]
> ```
>
> **Follow up:**
>
> - A rather straight forward solution is a two-pass algorithm using counting sort.
>   First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
> - Could you come up with a one-pass algorithm using only constant space?

##### 思路：

题目有要求，只遍历一次，这个排序并不复杂，因为只有三种数，考虑双指针，一个指向0，一个指向2，在遍历的时候，将合适的数交换到合适的位置。唯一需要注意的是，2的指针是倒着走的，因此交换之后，需要对于交换后的数字重新判断

##### 解法：

```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        if (nums.empty())
        {
            return;
        }
        int left = 0, right = nums.size()-1;
        for (int i = 0; i <= right; ++i)
        {
            if (nums[i] == 0)
            {
                swap(nums[i], nums[left++]);
            }
            else if (nums[i] == 2)
            {
                swap(nums[i], nums[right--]);
                --i;
            }
        }
    }
};
```

