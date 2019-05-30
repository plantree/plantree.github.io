---
layout: post
title: LeetCode Top 100 Liked (二)
categories: [LeetCode]
description: LeetCode Top 100 Liked (二)
keywords: LeetCode, Algorithms, 算法
---

#### 11. Merge Two Sorted Lists

>Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.
>
>**Example:**
>
>```
>Input: 1->2->4, 1->3->4
>Output: 1->1->2->3->4->4
>```

##### 思路：

合并两个有序链表，有点归并排序合并的操作，不算复杂，要处理的情形也比较简单，设置一个空头节点，依次遍历即可

##### 解法：

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1)
        {
            return l2;
        }
        if (!l2)
        {
            return l1;
        }
        ListNode *first = new ListNode(-1);
        ListNode *p1 = l1, *p2 = l2, *p = first;
        while (p1 && p2)
        {
            if (p1->val < p2->val)
            {
                p->next = p1;
                p1 = p1->next;
            }
            else
            {
                p->next = p2;
                p2 = p2->next;
            }
            p = p->next;
        }
        p->next = (p1 ? p1 : p2);
        return first->next;
        return first->next;
    }
};
```

#### 12. Generate Parentheses

> Given *n* pairs of parentheses, write a function to generate all combinations of well-formed parentheses.
>
> For example, given *n* = 3, a solution set is:
>
> ```
> [
>   "((()))",
>   "(()())",
>   "(())()",
>   "()(())",
>   "()()()"
> ]
> ```

##### 思路：

要将所有合法的括号的组合结果输出。因为括号必然是先左后右，因此左右括号的使用次数必然是要体现出来的，考虑用递归，考虑基准情形：

1. 左右剩余数都为0，可以输出
2. 左为0，说明之后的都是添加有括号，知道抵达第一种情形
3. 左括号数量等于有括号，说明之前的已经闭合，下一个必然为左（同时也保证了左括号的使用数量永远大于等于右括号）
4. 左括号数量少于右括号，因为这个时候下一个位置既可以是左（情形2保证不为0），也可以是右，递归调用

##### 解法：

```c++
class Solution {
public:
    void helper(string last, int left, int right, vector<string> &res)
    {
        if (left == 0 && right == 0)
        {
            res.push_back(last);
            return;
        }
        if (left == 0)
        {
            helper(last+")", left, right-1, res);
            return;
        }
        if (left == right)
        {
            helper(last+"(", left-1, right, res);
        }
        else if (left < right)
        {
            helper(last+"(", left-1, right, res);
            helper(last+")", left, right-1, res);
        }
    }
    vector<string> generateParenthesis(int n) {
        vector<string> res;
        if (n <= 0)
        {
            return res;
        }
        helper("", n, n, res);
        return res;
    }
};
```

#### 13. Merge k Sorted Lists

> Merge *k* sorted linked lists and return it as one sorted list. Analyze and describe its complexity.
>
> **Example:**
>
> ```
> Input:
> [
>   1->4->5,
>   1->3->4,
>   2->6
> ]
> Output: 1->1->2->3->4->4->5->6
> ```

##### 思路：

解决`k` 的问题，往往是要从简单的入手，比如先解决两个链表合并的问题，然后推而广之，怎么推广呢，无疑是两两合并的思路，有点像是针对链表的归并排序。一个难点在与两两查找过程中，链表组的数量也在动态变化，要及时调整，直到最后合并为一个。时间复杂度大体为`O(Nlogk)`，`k`是链表个数

##### 解法：

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *mergeTwoLists(ListNode *p1, ListNode *p2)
    {
        if (!p1)
        {
            return p2;
        }
        if (!p2)
        {
            return p1;
        }
        
        ListNode *first = new ListNode(-1);
        ListNode *cur1 = p1, *cur2 = p2, *cur = first;
        while (cur1 && cur2)
        {
            if (cur1->val < cur2->val)
            {
                cur->next = cur1;
                cur1 = cur1->next;
            }
            else 
            {
                cur->next = cur2;
                cur2 = cur2->next;
            }
            cur = cur->next;
        }
        cur->next = (cur1 ? cur1 : cur2);
        cur = first->next;
        delete first;
        return cur;
    }
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty())
        {
            return nullptr;
        }
        int k = lists.size();
        while (k > 1)
        {
            for (int i = 0; i < k/2; ++i)
            {
                lists[i] = mergeTwoLists(lists[i], lists[k-i-1]);
            }
            k = (k+1)/2;
        }
        return lists[0];
    }
};
```

#### 14. Next Permutation

> Implement **next permutation**, which rearranges numbers into the lexicographically next greater permutation of numbers.
>
> If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).
>
> The replacement must be **in-place** and use only constant extra memory.
>
> Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.
>
> ```
> 1,2,3` → `1,3,2`
> `3,2,1` → `1,2,3`
> `1,1,5` → `1,5,1
> ```

##### 思路：

这一题很有趣。寻找下一个更大的一种排列，可以想，如果后面的数比前面的数小，那么交换是没有意义的，那么继续往前寻找，直到找到一个小一点的数，那么怎么替换，当然是选择后面已经遍历过的所有数中最小的一个，交换后，将后面的数逆序排列

##### 解法：

```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        if (nums.empty())
        {
            return;
        }
        int len = nums.size();
        for (int i = len-2; i >= 0; --i)
        {
            if (nums[i] < nums[i+1])
            {
                // find the first larger than nums[i]
                int j;
                for (j = len-1; j > i; --j)
                {
                    if (nums[j] > nums[i])
                    {
                        break;
                    }
                }
                swap(nums[i], nums[j]);
                reverse(nums.begin()+i+1, nums.end());
                return;
            }
        }
        // not find, then reverse
        reverse(nums.begin(), nums.end());
    }
};
```

#### 15. Longest Valid Parentheses

> Given a string containing just the characters `'('` and `')'`, find the length of the longest valid (well-formed) parentheses substring.
>
> **Example 1:**
>
> ```
> Input: "(()"
> Output: 2
> Explanation: The longest valid parentheses substring is "()"
> ```
>
> **Example 2:**
>
> ```
> Input: ")()())"
> Output: 4
> Explanation: The longest valid parentheses substring is "()()"
> ```

##### 思路：

这一题看起来复杂，从简单的地方入手，还是要借助栈，栈里面的元素存储的是位置信息。现在开始遍历，如果是`(`，那么直接将位置入栈，如果是`)`，那么就有几种可能性了，如果此刻栈为空，说明没有闭合匹配，那么直接将新的计算起始位置设置为下一个，如果栈不为空，因为入栈的只有`(`，说明匹配上，弹出，接下来还要判断栈是否为空，为什么，因为这里匹配的可能只是局部，如果栈不为空，说明当前匹配的元素是和此时栈顶位置的差值，否则就要从起始点开始计算。相当精彩的一道题，栈的绝妙应用

##### 解法：

```c++
int longestValidParenthese(string s)
{
    if (s.empty())
    {
        return 0;
    }
    int res = 0, start = 0;
    stack<int> st;
    for (int i = 0; i < s.size(); ++i)
    {
        if (s[i] == '(')
        {
            st.push(i);
        }
        else if (s[i] == ')')
        {
            if (st.empty())
            {
                start = i + 1;
            }
            else {
                st.pop();
                res = st.empty() ? max(res, i-start+1) : max(res, i-st.top());
            }
        }
    }
    return res;
}
```

#### 16. Search In Rotated Sorted Array

> Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.
>
> (i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).
>
> You are given a target value to search. If found in the array return its index, otherwise return `-1`.
>
> You may assume no duplicate exists in the array.
>
> Your algorithm's runtime complexity must be in the order of *O*(log *n*).
>
> **Example 1:**
>
> ```
> Input: nums = [4,5,6,7,0,1,2], target = 0
> Output: 4
> ```
>
> **Example 2:**
>
> ```
> Input: nums = [4,5,6,7,0,1,2], target = 3
> Output: -1
> ```

##### 思路：

虽然有序的数组被局部颠倒，但是有序还是局部存在的，借助这个信息，就可以用二分查找。难点在于判断局部有序的位置，其实这个无非是要么是前半部分，要么是后半部分，对于有序的查找，我们是很熟悉的，多几个判断，本质是不变的

##### 解法：

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if (nums.empty())
        {
            return -1;
        }
        int len = nums.size();
        int left = 0, right = len-1;
        while (left <= right)
        {
            int mid = left + (right-left)/2;
            if (nums[mid] == target)
            {
                return mid;
            }
            else if (nums[mid] < nums[right])
            {
                if (nums[mid] < target && nums[right] >= target)
                {
                    left = mid+1;
                }
                else
                {
                    right = mid-1;
                }
            }
            else
            {
                if (nums[left] <= target && nums[mid] > target)
                {
                    right = mid-1;
                }
                else
                {
                    left = mid+1;
                }
            }
        }
        return -1;
    }
};
```

#### 17. Find First and Last Position of Element in Sorted Array

> Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.
>
> Your algorithm's runtime complexity must be in the order of *O*(log *n*).
>
> If the target is not found in the array, return `[-1, -1]`.
>
> **Example 1:**
>
> ```
> Input: nums = [5,7,7,8,8,10], target = 8
> Output: [3,4]
> ```
>
> **Example 2:**
>
> ```
> Input: nums = [5,7,7,8,8,10], target = 6
> Output: [-1,-1]
> ```

##### 思路：

这一题其实是个二分查找，但是又有一些不同，这里寻找的，是`target`的一个区间。那么问题就是寻找左右边界，而不是单纯的找到就退出查找了。边界判断，存在于已经寻找到目标值的位置，但是不清楚具体在区间的位置，那么就需要一些额外的判断，寻找左边界，是尽可能的把可能区间左移，反之就要往右移动

##### 解法：

```c++
class Solution {
public:
    int binarySearch(vector<int> &nums, int target, bool isLeft)
    {
        int len = nums.size();
        int left = 0, right = len-1;
        while (left <= right)
        {
            int mid = left + (right-left)/2;
            if (nums[mid] == target)
            {
                // 寻找左边界
                if (isLeft) 
                {
                    if (mid == 0 || (nums[mid-1] != target))
                    {
                        return mid;
                    }
                    else
                    {
                        right = mid-1;
                    }
                }
                // 寻找右边界
                else
                {
                    if (mid == len-1 || (nums[mid+1] != target))
                    {
                        return mid;
                    }
                    else
                    {
                        left = mid+1;
                    }
                }
                
            }
            else if (nums[mid] > target)
            {
                right = mid-1;
            }
            else 
            {
                left = mid+1;
            }
        }
        return -1;
    }
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res{-1, -1};
        if (nums.empty())
        {
            return res;
        }
        res[0] = binarySearch(nums, target, true);
        res[1] = binarySearch(nums, target, false);
        return res;
    }
};
```

#### 18. Combination Sum

> Given a **set** of candidate numbers (`candidates`) **(without duplicates)**and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sums to `target`.
>
> The **same** repeated number may be chosen from `candidates` unlimited number of times.
>
> **Note:**
>
> - All numbers (including `target`) will be positive integers.
> - The solution set must not contain duplicate combinations.
>
> **Example 1:**
>
> ```
> Input: candidates = [2,3,6,7], target = 7,
> A solution set is:
> [
>   [7],
>   [2,2,3]
> ]
> ```
>
> **Example 2:**
>
> ```
> Input: candidates = [2,3,5], target = 8,
> A solution set is:
> [
>   [2,2,2,2],
>   [2,3,3],
>   [3,5]
> ]
> ```

##### 思路：

对于这种求组合的问题，基本都是用递归的策略，将中间的变量放入递归函数中，递归的过程中动态增添。对于这道题，首先应该排序，接下来，因此同一个元素可以重复使用，因此在递归函数中，核心的循环需要格外的注意

##### 解法：

```c++
class Solution {
public:
    void helper(int start, vector<int> last, vector<int> &candidates, int target, vector<vector<int>> &res)
    {
        if (target < 0)
        {
            return;
        }
        if (target == 0)
        {
            res.push_back(last);
            return;
        }
        for (int i = start; i < candidates.size(); ++i)
        {
            last.push_back(candidates[i]);
            helper(i, last, candidates, target-candidates[i], res);
            last.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        if (candidates.empty())
        {
            return res;
        }
        sort(candidates.begin(), candidates.end());
        helper(0, {}, candidates, target, res);
        return res;
    }
};
```

#### 19. Trapping Rain Water

> Given *n* non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.
>
> ![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)
> The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. **Thanks Marcos** for contributing this image!
>
> **Example:**
>
> ```
> Input: [0,1,0,2,1,0,1,3,2,1,2,1]
> Output: 6
> ```

##### 思路：

这一题很难，但是越是难题其实解法越有意思。

第一种方法，双指针，一左一右，选取其中比较小的那个，然后从该方向继续遍历，同时计算存储的水的容量，为什么选择短的，因为短板效应。

第二种方法，动态规划，其实仔细考察，会发现原理和方法一是一样的，但是要遍历两次，寻找的都是左右方向上的所有高度的最小值。

##### 解法：

```c++
// solution 1
class Solution {
public:
    int trap(vector<int>& height) {
        if (height.size() < 2)
        {
            return 0;
        }
        int res = 0, left = 0, right = height.size()-1;
        while (left < right)
        {
            int minVal = min(height[left], height[right]);
            if (minVal == height[left])
            {
                ++left;
                while (left < right && height[left] < minVal)
                {
                    res += minVal - height[left++];
                }
            }
            else 
            {
                --right;
                while (left < right && height[right] < minVal)
                {
                    res += minVal - height[right--];
                }
            }
        }
        return res;
    }
};

// solution 2
class Solution {
public:
    int trap(vector<int>& height) {
        if (height.size() < 2)
        {
            return 0;
        }
        int res = 0, maxVal = 0, n = height.size();
        vector<int> dp(n);
        for (int i = 0; i < n; ++i)
        {
            dp[i] = maxVal;
            maxVal = max(maxVal, height[i]);
        }
        maxVal = 0;
        for (int i = n-1; i >=0; --i)
        {
            dp[i] = min(dp[i], maxVal);
            maxVal = maxVal(height[i], maxVal);
            if (dp[i] > height[i])
            {
                res += dp[i] - height[i];
            }
        }
        return res;
    }
};
```

#### 20. Permutations 

> Given a collection of **distinct** integers, return all possible permutations.
>
> **Example:**
>
> ```
> Input: [1,2,3]
> Output:
> [
>   [1,2,3],
>   [1,3,2],
>   [2,1,3],
>   [2,3,1],
>   [3,1,2],
>   [3,2,1]
> ]
> ```

##### 思路：

计算所有的排列组合，有趣的一道题。对于这种，免不了是递归，因为排列组合的顺序是很混乱的，因此考虑着借助交换，从前往后，每次交换一个位置，递归回来之后，再交换回来

##### 解法：

```c++
class Solution {
public:
    void helper(int start, vector<int> &nums, vector<vector<int>> &res)
    {
        if (start == nums.size())
        {
            res.push_back(nums);
            return;
        }
        for (int i = start; i < nums.size(); ++i)
        {
            swap(nums[i], nums[start]);
            helper(start+1, nums, res);
            swap(nums[start], nums[i]);
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        if (nums.empty())
        {
            return res;
        }
        sort(nums.begin(), nums.end());
        helper(0, nums, res);
        return res;
    }
};
```

