---
layout: post
title: LeetCode Top 100 Liked (一)
categories: [LeetCode]
description: LeetCode Top 100 Liked (一)
keywords: LeetCode, Algorithms, 算法
---

#### 1. Add Two Numbers

> You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
>
> You may assume the two numbers do not contain any leading zero, except the number 0 itself.
>
> **Example:**
>
> ```
> Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
> Output: 7 -> 0 -> 8
> Explanation: 342 + 465 = 807.
> ```

##### 思路：

利用链表模拟整数的加法，而且链表是倒序的，就更容易了：因为加法要对齐，从最低位开始。唯一需要考虑的就是进位的问题，只需要两个变量记录即可

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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) 
    {
        if (!l1) 
        {
            return l2;
        }
        if (!l2)
        {
            return l1;
        }
        ListNode *p1 = l1, *p2 = l2;
        // dummy node
        ListNode *first = new ListNode(-1), *p = first;
        int sum = 0, carry = 0;
        while (p1 || p2) 
        {
            sum = carry;
            if (p1)
            {
                sum += p1->val;
                p1 = p1->next;
            }
            if (p2) 
            {
                sum += p2->val;
                p2 = p2->next;
            }
            carry = sum / 10;
            ListNode *temp = new ListNode(sum%10);
            p->next = temp;
            p = p->next;
        }
        if (carry)
        {
            ListNode *temp = new ListNode(1);
            p->next = temp;
        }
        return first->next;
    }
};
```

#### 2. Largest Substring Without Repeating Characters

> Given a string, find the length of the **longest substring** without repeating characters.
>
> **Example 1:**
>
> ```
> Input: "abcabcbb"
> Output: 3 
> Explanation: The answer is "abc", with the length of 3. 
> ```
>
> **Example 2:**
>
> ```
> Input: "bbbbb"
> Output: 1
> Explanation: The answer is "b", with the length of 1.
> ```
>
> **Example 3:**
>
> ```
> Input: "pwwkew"
> Output: 3
> Explanation: The answer is "wke", with the length of 3. 
>              Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
> ```

##### 思路：

这一题实在是漂亮的不像话。寻找不重复出现元素的最长字符串，暴力的方法当然可以解决，`O(n^2)`的时间复杂度，线性的不好想，直接说思路。首先肯定要找个东西记录字符是否出现过，然后遍历字符串，维护一个滑动窗口，字符没有出现过，右边界滑动，如果这个字符前面出现了，那么就要将左边界滑动到首次出现的位置，保证了滑动窗口内没有重复的元素，怎么知道首次出现的位置呢，当然可以用一个数组来记录。具体在写程序的时候，左边界用`left`控制，表示的是首次出现的位置，当然要初始化为-1，因为真正长度的计算是从一个字符开始的，右边界靠遍历的位置即可。在窗口滑动的时候，有两种可能，首次出现的元素在滑动窗口内，那么就要及时调整左边界，还有种可能就是不在滑动窗口内，那么继续遍历即可

##### 解法：

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) 
    {
        if (s.empty())
        {
            return 0;
        }
        vector<int> mark(255, -1);
        int maxLen = 0, left = -1;
        for (int i = 0; i < s.size(); ++i)
        {
            // 调整左侧边界
            left = max(left, mark[s[i]]);
            // 更新字符首次出现位置
            mark[s[i]] = i;
            maxLen = max(maxLen, i-left);
        }
        return maxLen;
    }
};
```

#### 3. Median of Two Sorted Arrays

> There are two sorted arrays **nums1** and **nums2** of size m and n respectively.
>
> Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
>
> You may assume **nums1** and **nums2** cannot be both empty.
>
> **Example 1:**
>
> ```
> nums1 = [1, 3]
> nums2 = [2]
> 
> The median is 2.0
> ```
>
> **Example 2:**
>
> ```
> nums1 = [1, 2]
> nums2 = [3, 4]
> 
> The median is (2 + 3)/2 = 2.5
> ```

##### 思路：

这一题是相当难的，当然可以组合成一个数组，但是时间复杂度是线性的，找题目的意思，是要用二分，难点在于如何在两个有序数组了进行二分。其实原理还是一致的，也还是要找两个数组的mid，然后进行比较，递归的处理，要注意的是k在递归的时候数量的变化，很容易弄错索引，需要格外小心

##### 解法：

```c++
class Solution {
public:
    int findKth(vector<int> &nums1, int i, vector<int> &nums2, int j, int k)
    {
        if (i >= nums1.size())
        {
            return nums2[j+k-1];
        }
        if (j >= nums2.size())
        {
            return nums1[i+k-1];
        }
        if (k == 1)
        {
            return min(nums1[i], nums2[j]);
        }
        int mid1 = (i+k/2-1 >= nums1.size()) ? INT_MAX : nums1[i+k/2-1];
        int mid2 = (j+k/2-1 >= nums2.size()) ? INT_MAX : nums2[j+k/2-1];
        return (mid1 < mid2 ? findKth(nums1, i+k/2, nums2, j, k-k/2) : findKth(nums1, i, nums2, j+k/2, k-k/2));
    }
    
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) 
    {
        int len1 = nums1.size(), len2 = nums2.size();
        int left = (len1 + len2 + 1) / 2;
        int right = (len1 + len2 + 2) / 2;
        return (findKth(nums1, 0, nums2, 0, left) + findKth(nums1, 0, nums2, 0, right)) / 2.0;
    }
};
```

#### 4. Longest Palindromic Substring

> Given a string **s**, find the longest palindromic substring in **s**. You may assume that the maximum length of **s** is 1000.
>
> **Example 1:**
>
> ```
> Input: "babad"
> Output: "bab"
> Note: "aba" is also a valid answer.
> ```
>
> **Example 2:**
>
> ```
> Input: "cbbd"
> Output: "bb"
> ```

##### 思路：

这一题可以考虑动态规划。数组`dp[i][j]`记录从位置`i`到`j`处的子字符串是否是回文字符串。规则大致如下：

```
dp[i][j] = 1   if i == j
				 = (s[i] == s[j])  if j == i+1
				 = (s[i] == s[j] && dp[i+1][j-1]) if j > i+1
```

其实不难理解，因为有了辅助数组的参与，我们没必要反复判断相同的子字符串是否是回文，保留中间状态

##### 解法：

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.empty())
        {
            return "";
        }
        int len = s.size();
        int dp[len][len] = {0};
        int left = 0, right = 0;
        for (int i = 0; i < len; ++i)
        {
            dp[i][i] = 1;
            for (int j = 0; j < i; ++j)
            {
                if (i == j+1)
                {
                    dp[j][i] = (s[i] == s[j]);
                }
                else if (i > j+1)
                {
                    dp[j][i] = (s[i] == s[j] && dp[j+1][i-1]);
                }
                if (dp[j][i] && (i-j > right-left))
                {
                    left = j;
                    right = i;
                }
            }
        }
        return s.substr(left, right-left+1);
    }
};
```

#### 5. Regular Expression Matching

> Given an input string (`s`) and a pattern (`p`), implement regular expression matching with support for `'.'` and `'*'`.
>
> ```
> '.' Matches any single character.
> '*' Matches zero or more of the preceding element.
> ```
>
> The matching should cover the **entire** input string (not partial).
>
> **Note:**
>
> - `s` could be empty and contains only lowercase letters `a-z`.
> - `p` could be empty and contains only lowercase letters `a-z`, and characters like `.` or `*`.
>
> **Example 1:**
>
> ```
> Input:
> s = "aa"
> p = "a"
> Output: false
> Explanation: "a" does not match the entire string "aa".
> ```
>
> **Example 2:**
>
> ```
> Input:
> s = "aa"
> p = "a*"
> Output: true
> Explanation: '*' means zero or more of the precedeng element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
> ```
>
> **Example 3:**
>
> ```
> Input:
> s = "ab"
> p = ".*"
> Output: true
> Explanation: ".*" means "zero or more (*) of any character (.)".
> ```
>
> **Example 4:**
>
> ```
> Input:
> s = "aab"
> p = "c*a*b"
> Output: true
> Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore it matches "aab".
> ```
>
> **Example 5:**
>
> ```
> Input:
> s = "mississippi"
> p = "mis*is*p*."
> Output: false
> ```

##### 思路：

有意思的题难度都很大，这一题就是。考虑用递归，看看初始清醒，以`pattern`为判断依据

1. `p`为空，这个时候就要根据`s`是否为空判断
2. `p`的大小为1，说明没有`*`，比较简单，如果`s`不能为空，而且要判断同一位置字母是否相等，或者`p[0]=='.'`
3. `p`的大小超过1了，就要看第二个位置是否是`*`，如果不是，那么就按位置匹配，然后继续判断剩余字符串的匹配情况；如果第二个位置是`*`，考虑到可以匹配0个或者多个，继续判断，这是整个代码的核心

##### 解法：

```c++
class Solution {
public:
    bool isMatch(string s, string p) 
    {
        if (p.empty())
        {
            return s.empty();
        }
        // 不存在*
        if (p.size() == 1)
        {
            return (s.size() == 1 && (p[0] == '.' || s[0] == p[0]));
        }
        if (p[1] != '*')
        {
            if (s.empty())
            {
                return false;
            }
            return (s[0] == p[0] || p[0] == '.') && isMatch(s.substr(1), p.substr(1));
        }
        return isMatch(s, p.substr(2)) || (!s.empty() && (s[0] == p[0] || p[0] == '.') && isMatch(s.substr(1), p));
    }
};
```

#### 6. Container With Most Water

> Given *n* non-negative integers *a1*, *a2*, ..., *an* , where each represents a point at coordinate (*i*, *ai*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *ai*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.
>
> **Note:** You may not slant the container and *n* is at least 2.
>
>  
>
> ![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)
>
> The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
>
>  
>
> **Example:**
>
> ```
> Input: [1,8,6,2,5,4,8,3,7]
> Output: 49
> ```

##### 思路：

这一题，看起来复杂，想清楚就简单。两个指针，一左一右，从两边逼近，此时宽度最大，唯一影响的就是最小的那个高度。指针移动的唯一原因，是因为高度小，影响了面积，只有通过移动，在减小宽度的同时，尽量寻找较大的高度来弥补。控制好变量和不变量

##### 解法：

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        if (height.size() <= 1)
        {
            return 0;
        }
        int left = 0, right = height.size()-1;
        int maxVal = min(height[left], height[right]) * (right-left);
        while (left < right)
        {
            if (height[left] < height[right])
            {
                ++left;
            } 
            else
            {
                --right;
            }
            int temp = min(height[left], height[right]) * (right-left);
            maxVal = max(maxVal, temp);
        }
        return maxVal;
    }
};
```

#### 7. 3Sum

> Given an array `nums` of *n* integers, are there elements *a*, *b*, *c* in `nums`such that *a* + *b* + *c* = 0? Find all unique triplets in the array which gives the sum of zero.
>
> **Note:**
>
> The solution set must not contain duplicate triplets.
>
> **Example:**
>
> ```
> Given array nums = [-1, 0, 1, 2, -1, -4],
> 
> A solution set is:
> [
>   [-1, 0, 1],
>   [-1, -1, 2]
> ]
> ```

##### 思路：

处理数组类的问题，先排序然后使用双指针，真是个不错的方法，而且效率很高。计算三元组的问题，首先要固定一个数，然后变化剩下的数，尽可能的减少比较的个数，就是使用双指针，根据已知的信息，常数范围的移动

##### 解法：

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        if (nums.empty())
        {
            return res;
        }
        sort(nums.begin(), nums.end());
        int len = nums.size();
        if (nums.front() > 0 || nums.back() < 0)
        {
            return res;
        }
        for (int i = 0; i < len; ++i)
        {
            // skip the same
            if (i != 0 && nums[i] == nums[i-1])
            {
                continue;
            }
            int j = i+1, k = len-1;
            while (j < k)
            {
                int sum = nums[i] + nums[j] + nums[k];
                if (sum == 0)
                {
                    res.push_back({nums[i], nums[j], nums[k]});
                    while (j < k && nums[j+1] == nums[j])
                    {
                        ++j;
                    }
                    while (j < k && nums[k-1] == nums[k])
                    {
                        --k;
                    }
                    ++j;
                    --k;
                        
                }
                else if (sum < 0)
                {
                    ++j;
                }
                else
                {
                    --k;
                }
            }
        }
        return res;
    }
};
```

#### 8. Letter Combinations of a Phone Number

> Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent.
>
> A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.
>
> ![img](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)
>
> **Example:**
>
> ```
> Input: "23"
> Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
> ```
>
> **Note:**
>
> Although the above answer is in lexicographical order, your answer could be in any order you want.

##### 思路：

这一题像是一种排列组合，第一个字符串里选一个，第二个再选，有点像是金字塔，一层层的往底层推进，有递归的味道，事实上解决的办法也是递归的，处理完第一层，递归的处理下一层，可见调用的递归函数会越来越多，但是最后都会收敛，只要初始条件设置好，不要忘记`return`

##### 解法：

```c++
class Solution {
public:
    void helper(string last, int index, vector<string> &svec, vector<string> &res)
    {
        if (index == svec.size())
        {
            res.push_back(last);
            return;
        }
        for (auto c : svec[index])
        {
            helper(last+c, index+1, svec, res);
        }
    }
    vector<string> letterCombinations(string digits) {
        vector<string> res;
        if (digits.empty())
        {
            return res;
        }
        vector<string> m = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        vector<string> svec;
        for (auto c : digits)
        {
            if (c < '2' || c > '9')
            {
                continue;
            }
            svec.push_back(m[c-'0']);
        }
        helper("", 0, svec, res);
        return res;
    }
};
```

#### 9. Remove Nth Node From End of List

> 
> Given a linked list, remove the *n*-th node from the end of list and return its head.
>
> **Example:**
>
> ```
> Given linked list: 1->2->3->4->5, and n = 2.
> 
> After removing the second node from the end, the linked list becomes 1->2->3->5.
> ```
>
> **Note:**
>
> Given *n* will always be valid.
>
> **Follow up:**
>
> Could you do this in one pass?

##### 思路：

当然可以遍历一遍，计算长度，然后计算差值。其实一遍就够了，假设链表长度为`m`，从后数第`n`个位置，从前面看就是`m-n`，双指针，一个先走，走`n`步，然后两个指针齐头并进，要考虑第一次走到尾部的情况

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (!head || n <= 0)
        {
            return nullptr;
        }
        ListNode *fast = head,
                 *slow = head;
        for (int i = 0; i < n; ++i)
        {
            fast = fast->next;
        }
        if (!fast)
        {
            return head->next;
        }
        while (fast->next)
        {
            fast = fast->next;
            slow = slow->next;
        }
        ListNode *temp = slow->next;
        slow->next = temp->next;
        delete temp;
        return head;
    }
};
```

#### 10. Valid Parentheses

> Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['`and `']'`, determine if the input string is valid.
>
> An input string is valid if:
>
> 1. Open brackets must be closed by the same type of brackets.
> 2. Open brackets must be closed in the correct order.
>
> Note that an empty string is also considered valid.
>
> **Example 1:**
>
> ```
> Input: "()"
> Output: true
> ```
>
> **Example 2:**
>
> ```
> Input: "()[]{}"
> Output: true
> ```
>
> **Example 3:**
>
> ```
> Input: "(]"
> Output: false
> ```
>
> **Example 4:**
>
> ```
> Input: "([)]"
> Output: false
> ```
>
> **Example 5:**
>
> ```
> Input: "{[]}"
> Output: true
> ```

##### 思路：

对于这类存在对称的题，一般要想到栈。而且括号的闭合，本身就满足后入先出，因此思路清楚

##### 解法：

```c++
class Solution {
public:
    bool isValid(string s) {
        if (s.empty())
        {
            return true;
        }
        stack<char> st;
        for (const auto c : s)
        {
            if (c == '}' || c == ')' || c == ']')
            {
                if (c == '}')
                {
                    if (!st.empty() && st.top() == '{')
                    {
                        st.pop();
                    }
                    else 
                    {
                        return false;
                    }
                }
                else if (c == ')')
                {
                    if (!st.empty() &&st.top() == '(')
                    {
                        st.pop();
                    }
                    else 
                    {
                        return false;
                    }
                }
                else if (c == ']')
                {
                    if (!st.empty() && st.top() == '[')
                    {
                        st.pop();
                    }
                    else 
                    {
                        return false;
                    }
                }
            }
            else
            {
                st.push(c);
            }
        }
        return st.empty();
    }
};
```

