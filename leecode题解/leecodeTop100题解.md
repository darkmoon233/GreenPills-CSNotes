
所有题目来源:力扣（LeetCode）
链接：<https://leetcode-cn.com/problems/minimum-path-sum>

# 1. 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

1. 解法1：暴力，双层循环，时间复杂度O(n2),空间复杂度O(1)

    ```cpp
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            vector<int> ans;
            for(int i = 0;i<nums.size();i++){
                for(int j = i+1;j<nums.size();j++){
                    if(nums.at(i) + nums.at(j) == target) {
                        ans.push_back(i);
                        ans.push_back(j);
                    }
                }
            }
            return ans;
        }
    };
    ```

2. 解法2：利用map是红黑树的特性，将nums中的以[值，下标]的形式存储在一个map中，再逐个检查nums所需的元素（target-nums）是否存在于map中，若存在且并非自身，即找到。

    ```cpp
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            map<int,int> mNum;
            vector<int> vAns;
            for(int i = 0;i < nums.size();i++)
                mNum[nums.at(i)] = i;
            for(int i = 0;i < nums.size();i++){
                int temp = target - nums.at(i);
                if(mNum.count(temp) && mNum[temp] != i){
                    vAns.push_back(i);
                    vAns.push_back(mNum[temp]);
                    return vAns;
                }
            }
            return vAns;
        }
    };
    ```

3. 解法3：利用unordered_map的地层是哈希表的特性，将nums中的以[值，下标]的形式存储在一个unordered_map中，再逐个检查nums所需的元素（target-nums）是否存在于unordered_map中，若存在且并非自身，即找到。(unordered_map使用的哈希表对于哈希冲突的处理是不允许冲突，从这个角度来说，unordered_map和map在使用和表现上没有任何区别，但是事实上哈希的时间会比红黑树更快，前者是O(1),后者是O(lgn))

    ```cpp
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            unordered_map<int,int> mNum;
            vector<int> vAns;
            for(int i = 0;i < nums.size();i++)
                mNum[nums.at(i)] = i;
            for(int i = 0;i < nums.size();i++){
                int temp = target - nums.at(i);
                if(mNum.count(temp) && mNum[temp] != i){
                    vAns.push_back(i);
                    vAns.push_back(mNum[temp]);
                    return vAns;
                }
            }
            return vAns;
        }
    };
    ```

# 2. 两数相加
给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807

题解：顺序相加，注意进位。还有一种方式是自动为不等长的链表补齐长度计算。
```cpp
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
	if (l1 == NULL)
		return l2;
	if (l2 == NULL)
		return l1;
	int jinwei = 0;
	ListNode* head=NULL, * cur=NULL;
	while (l1 != NULL && l2 != NULL) {
		int num = (l1->val + l2->val + jinwei) % 10;
		ListNode* temp = new ListNode(num);
		jinwei = (l1->val + l2->val + jinwei) / 10;
		if (head == NULL) {
			head = temp;
			cur = temp;
		}
		else {
			cur->next = temp;
			cur = temp;
		}
		l1 = l1->next;
		l2 = l2->next;
	}
	if (l1 != NULL||l2 !=NULL) {
		ListNode* unFinished;
		if (l1 != NULL)
			unFinished = l1;
		else
			unFinished = l2;
		while (unFinished != NULL && jinwei) {
			int num = (unFinished->val + jinwei) % 10;
			ListNode* temp = new ListNode(num);
			jinwei = (unFinished->val + jinwei) / 10;
			cur->next = temp;
			cur = temp;
			unFinished = unFinished->next;
		}
		if (unFinished == NULL && jinwei) {
			ListNode* temp = new ListNode(1);
			cur->next = temp;
		}
		else
			cur->next = unFinished;
	}
	else {
		if (jinwei == 1) {
			ListNode* temp = new ListNode(1);
			cur->next = temp;
		}
	}
	return head;
}
```
# 3. 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

输入: "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
示例 2:

输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
示例 3:

输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

解法：利用滑动窗口进行求解。基本思路为如果[i,j)为无重复子串，则考虑 [i,j]是否为无重复子串，如果j与[i,j)中的J重复了，则将窗口滑动至J+1的位置，每次窗口滑动都记录是否为最大值，判断是否为重复可以采用哈希表或是红黑树。

    ```cpp
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> mUnRepeated;
        int iMax = 0, iLeft = 0, iCurrentMax = 0;
        for (auto i = 0; i < s.size(); i++) {
            if (mUnRepeated.count(s.at(i)) && mUnRepeated[s.at(i)] >= iLeft) {
                iLeft = mUnRepeated[s.at(i)] + 1;
            }
            iMax = max(iMax, i - iLeft + 1);
            mUnRepeated[s.at(i)] = i;
        }
        return iMax;
    }
    ```

# 4. 寻找两个有序数组的中位数

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

示例 1:

nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
示例 2:

nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5

在不考虑时间复杂度的情况下的解法(见笑)：

```cpp
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
    int iSize = nums1.size() + nums2.size();
    double iAns = 0;
    bool isOdd = false;
    if (iSize % 2 == 0)
        isOdd = false;
    else
        isOdd = true;
    if (isOdd) {
        int targetIndex = iSize / 2 + 1, iIndex = 0, iIndex1 = 0, iIndex2 = 0;
        while (1){
            iIndex++;
            if (iIndex == targetIndex) {
                if (iIndex1 == nums1.size()) {
                    iAns = nums2.at(iIndex2);
                    break;
                }
                if (iIndex2 == nums2.size()) {
                    iAns = nums1.at(iIndex1);
                    break;
                }
                if (nums1.at(iIndex1) < nums2.at(iIndex2)) {
                    iAns = nums1.at(iIndex1);
                    break;
                }
                else{
                    iAns = nums2.at(iIndex2);
                    break;
                }
            }
            else {
                if (iIndex1 == nums1.size()) {
                    iIndex2++;
                    continue;
                }
                if (iIndex2 == nums2.size()) {
                    iIndex1++;
                    continue;
                }
                if (nums1.at(iIndex1) < nums2.at(iIndex2))
                    iIndex1++;
                else
                    iIndex2++;
            }
        }
    }
    else {
        int targetIndex = iSize / 2, iIndex = 0, iIndex1 = 0, iIndex2 = 0;
        while (1){
            iIndex++;
            if (iIndex == targetIndex) {
                if (iIndex1 == nums1.size()) {
                    iAns = nums2.at(iIndex2)+ nums2.at(iIndex2 + 1);
                    iAns /= 2;
                    break;
                }
                if (iIndex2 == nums2.size()) {
                    iAns = nums1.at(iIndex1) + nums1.at(iIndex1 + 1);
                    iAns /= 2;
                    break;
                }
                if (nums1.at(iIndex1) < nums2.at(iIndex2)) {
                    iAns += nums1.at(iIndex1);
                    iIndex1++;
                }
                else {
                    iAns += nums2.at(iIndex2);
                    iIndex2++;
                }
                if (iIndex1 == nums1.size()) {
                    iAns += nums2.at(iIndex2 );
                    iAns /= 2;
                    break;
                }
                if (iIndex2 == nums2.size()) {
                    iAns +=  nums1.at(iIndex1 );
                    iAns /= 2;
                    break;
                }
                if (nums1.at(iIndex1) < nums2.at(iIndex2 )) {
                    iAns += nums1.at(iIndex1);
                }
                else {
                    iAns += nums2.at(iIndex2);
                }
                iAns /= 2;
                break;
            }
            else {
                if (iIndex1 == nums1.size()) {
                    iIndex2++;
                    continue;
                }
                if (iIndex2 == nums2.size()) {
                    iIndex1++;
                    continue;
                }
                if (nums1.at(iIndex1) < nums2.at(iIndex2))
                    iIndex1++;
                else
                    iIndex2++;
            }
        }
    }
    return iAns;
}
```

# 5. 最长回文子串

给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例 1：

输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
示例 2：

输入: "cbbd"
输出: "bb"

```cpp
string longestPalindrome(string s) {
    int iCurrent = 0, iLeft = 0, iRight = 0, iMax = 0, iPos = 0;
    string ans;
    for (; iCurrent < s.size(); iCurrent++) {
        int iSLeft = 0, iSRight = 0;
        iSLeft = iSRight = iLeft = iRight = iCurrent;
        if (iRight < s.size() && iLeft >= 0){
            while (iRight < s.size() && iLeft >= 0 && s.at(iRight) == s.at(iCurrent)) {
                iSRight = iRight;
                iRight++;
            }
            while (iRight < s.size() && iLeft >= 0 &&  s.at(iLeft) == s.at(iCurrent)) {
                iSLeft = iLeft;
                iLeft--;
            }
            while (iRight < s.size() && iLeft >= 0 &&  s.at(iLeft) == s.at(iRight)) {
                iSLeft = iLeft;
                iSRight = iRight;
                iLeft--;
                iRight++;
            }
            if (iSRight - iSLeft + 1> iMax) {
                iMax = iSRight - iSLeft + 1;
                iPos = iSLeft;
            }
        }
    }
    return s.substr(iPos, iMax);
}
```
# 10. 正则表达式匹配

给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素
所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

说明:

s 可能为空，且只包含从 a-z 的小写字母。
p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
示例 1:

输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
示例 2:

输入:
s = "aa"
p = "a*"
输出: true
解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
示例 3:

输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
示例 4:

输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
示例 5:

输入:
s = "mississippi"
p = "mis*is*p*."
输出: false

分析：动态规划问题，因为有最优子结构（复杂的串可由前一状态推导）。分析可以知道有以下几种情况：
（1）`p[j]==s[i]` 此时相当于只要两者的前序正确则本项就正确，即`dp[i][j]=dp[i-1][j-1]`
(2)`p[j]=='.'` `,`是相当于任何一个字符，所以和上边的情况是完全一致的。`dp[i][j]=dp[i-1][j-1]`
(3)`p[j]=='*'` '*'可以表示一个或者多个前一位的字符，这意味着他必须和前一位捆绑分析。有以下几种情况：
    1. `p[j-1]!=s[i]` 相当于是*cover了0次`s[i]`.`dp[i][j] = dp[i][j-2]`
    2. `p[j-1]==s[i] || p[j-1]=='.'`此时的情况有以下几种
        (1) `*` cover了多次`s[i]` 此时`dp[i][j]=dp[i-1][j]`(不考虑cover了几次，逐渐向前推进)
        (2) `*` cover了一次 's[i]` ,此时 `dp[i][j]=dp[i][j-1]`(相当于是和没有`*`是一样的）
        (3) '*' cover了0次`s[i]`,此时`dp[i][j]=dp[i][j-2]`（相当于是两个符号都没用，这种情况可能出现在类似`a*a*`这样的组合当中）

```cpp
bool isMatch(string s, string p) {
	vector<vector<bool>> dp(s.size() + 1, vector<bool>(p.size() + 1, false));
	dp[0][0] = true;
	for (int i = 0; i <= s.size(); i++) {
		for (int j = 1; j <= p.size(); j++) {
			if (p[j-1] == '*') {
				dp[i][j] = dp[i][j - 2] || (i && dp[i - 1][j] && (s[i - 1] == p[j - 2] || p[j - 2] == '.'));
			}
			else {
				dp[i][j] = i && dp[i - 1][j - 1] && (s[i - 1] == p[j - 1] || p[j - 1] == '.');
			}
		}
	}
	return dp[s.size()][p.size()];
}
```


# 11. 盛最多水的容器

给定 n 个非负整数 a1，a2，...，an，
每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

示例:

输入: [1,8,6,2,5,4,8,3,7]
输出: 49

解法1：暴力法。通过双重循环逐项取值，计算面积值并进行比对，保留最大面积。时间复杂度O(n2)。事实上会超时。

```cpp
int maxArea(vector<int>& height) {
    int iMaxArea = 0;
    for (auto i = 0; i < height.size(); i++) {
        for (auto j = i + 1; j < height.size(); j++) {
            int iHeight = min(height.at(i), height.at(j));
            if (iHeight*(j - i) > iMaxArea)
                iMaxArea = iHeight * (j - i);
        }
    }
    return iMaxArea;
}
```

解法2：双指针法。这种解法利用的是本题的特性，即在左右两边中，决定水桶盛水量的是较短的一边。因此，可以考虑如下情况，左右两边左高右低，此时要将一边向内移动，只能选择短的一边，因为即使长的变得更长，也只能带来面积的减少。时间复杂度O(n)。

```cpp
int maxArea(vector<int>& height) {
    int iMaxArea = 0, iLeft = 0, iRight = height.size() - 1;
    while (iLeft < iRight) {
        int iHeight = min(height.at(iLeft), height.at(iRight));
        if (iHeight*(iRight - iLeft) > iMaxArea)
            iMaxArea = iHeight * (iRight - iLeft);
        if (height.at(iLeft) < height.at(iRight))
            iLeft++;
        else
            iRight--;
    }
    return iMaxArea;
}
```

# 15. 三数之和

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。
例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]

解法1： 最简单的思路是三重循环，在此基础上引入哈希表可以去除一重循环。理论上时间复杂度为O(n2)，但是其中加入了find的操作，也就是实际上时间复杂度是超过O(n2)。事实上会在289用例处超时。

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>>vTotalNums;
    unordered_map<int, int> hmNums;
    for (int i = 0; i < nums.size(); i++) {
        if (hmNums.count(nums.at(i)))
            hmNums[nums.at(i)]++;
        hmNums[nums.at(i)] = 1;
    }
    auto iter1 = nums.begin();
    while (iter1 != nums.end()) {
        auto iter2 = iter1;
        iter2++;
        while (iter2 != nums.end()) {
            int target = 0 - *iter1 - *iter2;
            if (hmNums.count(target) && find(nums.begin(),nums.end(),target) != iter1 && find(nums.begin(), nums.end(), target) != iter2) {
                vector<int> vTemp;
                vTemp.push_back(*iter1);
                vTemp.push_back(*iter2);
                vTemp.push_back(target);
                sort(vTemp.begin(), vTemp.end());
                auto iter3 = find(vTotalNums.begin(), vTotalNums.end(), vTemp);
                if (iter3 == vTotalNums.end())
                    vTotalNums.push_back(vTemp);
            }
            iter2++;
        }
        iter1++;
    }
    return vTotalNums;
}
```

解法2：对撞指针法。对整个数组排序，固定没轮的target = 0 - nums[i],然后用左右两边指针向内逼近，对重复项进行跳过处理。时间复杂度为O(n2).

```cpp
vector<vector<int>>vTotalNums;
sort(nums.begin(), nums.end());
if (nums.empty() || nums.front()>0 || nums.back()<0)
    return vTotalNums;
for (int i = 0; i < nums.size(); i++) {
    if (i>0 && nums.at(i) == nums.at(i-1))continue;
    int iLeft = i + 1, iRight = nums.size() - 1, target = nums.at(i)*-1;
    while (iLeft < iRight) {
        if (iLeft>i+1 &&  nums.at(iLeft) == nums.at(iLeft - 1)) {
            iLeft++;
            continue;
        }
        if (iRight < nums.size() - 1 &&  nums.at(iRight) == nums.at(iRight + 1)) {
            iRight--;
            continue;
        }
        if (nums.at(iLeft) + nums.at(iRight) == target) {
            vTotalNums.push_back(vector<int>{nums[i], nums[iLeft], nums[iRight]});
            iLeft++;
            iRight--;
        }
        else {
            if (nums.at(iLeft)+nums.at(iRight) < target)
                iLeft++;
            else
                iRight--;
        }
    }
}

return vTotalNums;
```

# 17. 电话号码的字母组合

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![phoneNumber](https://pinkpills-1259674611.cos.ap-shanghai.myqcloud.com/pinkpill/images/leecode17phonenumber.png)

示例:

输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
说明:
尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

解法1：用hash表存放按键关系，循环迭代的方式逐项向后添加字母。时间复杂度是O(4n).

```cpp
vector<string> letterCombinations(string digits) {
    unordered_map<char, vector<string>> hmLetters;
    hmLetters['2'] = vector<string>{ "a","b","c" };
    hmLetters['3'] = vector<string>{ "d","e","f" };
    hmLetters['4'] = vector<string>{ "g","h","i" };
    hmLetters['5'] = vector<string>{ "j","k","l" };
    hmLetters['6'] = vector<string>{ "m","n","o" };
    hmLetters['7'] = vector<string>{ "p","q","r","s" };
    hmLetters['8'] = vector<string>{ "t","u","v" };
    hmLetters['9'] = vector<string>{ "w","x","y","z" };
    vector<string> vFront, vCurrnt, vAns;
    for (int i = 0; i < digits.size(); i++) {
        if (i == 0) {
            vCurrnt = hmLetters[digits.front()];
            continue;
        }
        swap(vFront, vCurrnt);
        vCurrnt.clear();
        for (int j = 0; j < vFront.size(); j++) {
            for (int k = 0; k < hmLetters[digits.at(i)].size(); k++) {
                string sTemp = vFront.at(j) + hmLetters[digits.at(i)].at(k);
                vCurrnt.push_back(sTemp);
            }
        }
    }
    return vCurrnt;
}
```

解法2：用hash表存放按键关系，用递归的方式逐项添加字符，直到串长度用完。

```cpp
unordered_map<char, vector<string>> hmLetters;
vector<string> vAns;
void letterCombinations(string digits, int index, string temp) {
    if (index == digits.size()) {
        vAns.push_back(temp);
        return;
    }
    for (int i = 0; i < hmLetters[digits.at(index)].size(); i++) {
        letterCombinations(digits, index + 1, temp + hmLetters[digits.at(index)].at(i));
    }
}
vector<string> letterCombinations(string digits) {
    hmLetters['2'] = vector<string>{ "a","b","c" };
    hmLetters['3'] = vector<string>{ "d","e","f" };
    hmLetters['4'] = vector<string>{ "g","h","i" };
    hmLetters['5'] = vector<string>{ "j","k","l" };
    hmLetters['6'] = vector<string>{ "m","n","o" };
    hmLetters['7'] = vector<string>{ "p","q","r","s" };
    hmLetters['8'] = vector<string>{ "t","u","v" };
    hmLetters['9'] = vector<string>{ "w","x","y","z" };
    if (digits.size() == 0)
        return vAns;
    letterCombinations(digits, 0, "");
    return vAns;
}
```

# 19. 删除链表的倒数第N个节点

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例：

给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
说明：

给定的 n 保证是有效的。

进阶：

你能尝试使用一趟扫描实现吗？

解法1.通过哈希表记录节点序号和对应的指针，就可以用顺序访问的方式随机取到链表的任意节点。同样是一次遍历，时间复杂度O(n)

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
ListNode* removeNthFromEnd(ListNode* head, int n) {
    unordered_map<int, ListNode*> hmListNode;
    int size = 1;
    ListNode* temp = head;

    while (temp!=NULL) {
        hmListNode[size] = temp;
        size++;
        temp = temp->next;
    }
    int  iDelete = size - n, iFront = iDelete - 1;
    if (n == size - 1) {
        head = head->next;
    }
    else{
        hmListNode[iFront]->next = hmListNode[iDelete]->next;
    }
    hmListNode[iDelete]->next = NULL;
    return head;
}
```

解法2：双指针法。设定前后两个指针，前指针持续向前，后指针在等了n次之后再出发，等到前指针到达尾节点时，后指针就是目标节点。也是一次遍历，时间复杂度O(n)。

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode* pBack = head;
    ListNode* pFront = head;
    bool flag = false;
    while (pFront != NULL) {
        if (flag == false) {
            if (n == 0)
                flag = true;
            n--;
        }
        else
            pBack = pBack->next;
        pFront = pFront->next;
    }
    if (flag == false && pBack == head)
        head = head->next;
    else {
        pBack->next = pBack->next->next;
    }
    return head;
}
```
# 20. 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。可以通过预先建立hashmap的方式来确定左右括号的关系。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

示例 1:

输入: "()"
输出: true
示例 2:

输入: "()[]{}"
输出: true
示例 3:

输入: "(]"
输出: false
示例 4:

输入: "([)]"
输出: false
示例 5:

输入: "{[]}"
输出: true

解法：利用栈，最近的正反括号必须匹配，正反括号数量必须相等。

```cpp
bool isValid(string s) {
	stack<char> st;
	for (char c : s) {
		if (c == '(' || c == '[' || c == '{')
			st.push(c);
		else {
			if (st.empty())
				return false;
			char cc = st.top();
			if ((cc == '(' && c == ')') || (cc == '[' && c == ']') || (cc == '{' && c == '}'))
				st.pop();
			else
				return false;
		}
	}
	if (st.empty())
		return true;
	else
		return false;
}
```

# 21. 合并两个有序链表

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

示例：

输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4

解法1：比较法。时间复杂度O(n)。

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode *head,*current,*temp1, *temp2;
    if (l1 == NULL)return l2;
    if (l2 == NULL)return l1;
    if (l1->val < l2->val) {
        head = l1;
        temp1 = head->next;
        temp2 = l2;
    }
    else {
        head = l2;
        temp1 = l1;
        temp2 = head->next;
    }
    current = head;
    while (temp1 != NULL || temp2 != NULL) {
        if (temp1 == NULL) {
            current->next = temp2;
            break;
        }
        if (temp2 == NULL) {
            current->next = temp1;
            break;
        }
        if (temp1->val>temp2->val) {
            current->next = temp2;
            current = temp2;
            temp2 = temp2->next;
            continue;
        }
        if (temp1->val <= temp2->val) {
            current->next = temp1;
            current = temp1;
            temp1 = temp1->next;
            continue;
        }
    }
    return head;
}
```

解法2：递归。感受一下，我是没想到的。

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    if (l1 == NULL)return l2;
    if (l2 == NULL)return l1;
    if (l1->val < l2->val) {
        l1->next = mergeTwoLists(l1->next, l2);
        return l1;
    }
    else {
        l2->next = mergeTwoLists(l1, l2->next);
        return l2;
    }
}
```

# 22. 括号生成

给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。

例如，给出 n = 3，生成结果为：

[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]

解法：分析可知，第一个总是"(",在逐个生成字符串的过程中，遵循"("的数量大于等于")"的数量的规则添加新的符号。不过事实上超过10就会超时了。

```cpp
vector<string> vTotal;
void generateParenthesis(int n, string s, int iLeft, int iRight) {
    if (iLeft == n && iRight == n) {
        vTotal.push_back(s);
        return;
    }
    if (iLeft != n)
        generateParenthesis(n, s+"(", iLeft + 1, iRight);
    if (iRight < iLeft)
        generateParenthesis(n, s+")", iLeft, iRight + 1);
}
vector<string> generateParenthesis(int n) {
    if (n == 0)return vTotal;
    else
        generateParenthesis(n, "(", 1, 0);
    return vTotal;
}
```

# 23. 合并K个排序链表

合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

示例:

输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6

解法1：通过map进行链表节点的存储，对于相同的val的节点进行链表的串联，利用map的底层为红黑树的特性进行存储可以得到有序的序列（不过和堆很类似）。相当于整个链表都被拆散了再进行组合。时间复杂度取决于链表的节点总数。

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
ListNode* mergeKLists(vector<ListNode*>& lists) {
    if (lists.size() == 0) {
        return NULL;
    }
    if (lists.size() == 1) {
        return lists.front();
    }
    map<int, ListNode*> hmTotalNums;
    for (int i = 0; i < lists.size(); i++) {
        ListNode* temp = lists.at(i);
        while (temp != NULL) {
            ListNode* nextNode = temp->next;
            temp->next = NULL;
            if (hmTotalNums.count(temp->val)) {
                temp->next = hmTotalNums[temp->val];
                hmTotalNums[temp->val] = temp;
            }
            else
                hmTotalNums[temp->val] = temp;
            temp = nextNode;
        }
    }
    if (hmTotalNums.size() == 0)
        return NULL;
    auto iter = hmTotalNums.begin();
    while (iter != hmTotalNums.end()) {
        ListNode* temp=iter->second;
        while (temp->next != NULL)
            temp = temp->next;
        iter++;
        if (iter != hmTotalNums.end())
            temp->next = iter->second;
    }
    return hmTotalNums.begin()->second;
}
```

# 31. 下一个排列

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1

题解：首先解释一下字典。根据wiki的蜜汁分析(设想一本英语字典里的单词，哪个在前哪个在后？)，字典序可以认为是是这样一种表述：首先定义元素的大小关系，如1<2,2<3,字典序的比较方式为：假设比较大小的是A,B,从第一位顺序进行比较，相同则比较下一位，如果在某一位上A>B,则字典序上A>B（如12354>12345),因此每一位都比后一位小的顺序是最小的字典序（12345），每一位都比后一位大的顺序是最大的字典序（54321）。
找到下个字典序我的思路是

1. 从后向前找到第一个后>前的位置，定义此时前的位置为front，串的结尾即end
2. 找到[front,end]中比front处值大的最小值，其位置为target
3. 交换target和front处的值
4. 对[front+1,end]的进行排序。

```cpp
bool cmp(int a, int b) {
    if (a > b)return true;
    return false;
}
void nextPermutation(vector<int>& nums) {
    if (nums.size() <= 1)
        return;
    auto endIter = nums.end() - 1;
    while (*endIter <= *(endIter - 1)) { 
        endIter--;
        if (endIter == nums.begin()) {
            //逆序
            sort(nums.begin(), nums.end());
            return;
        }
    }
    endIter = endIter - 1;
    auto minIter = endIter + 1,targetIter= minIter;
    int iMin = *minIter-*endIter;
    while (minIter!=nums.end()){
        if (*minIter - *endIter > 0 && (*minIter - *endIter) < iMin) {
            iMin = *minIter - *endIter;
            targetIter = minIter;
        }
        minIter++;
    }
    int temp = 0;
    temp = *endIter;
    *endIter = *targetIter;
    *targetIter = temp;

    sort(endIter + 1, nums.end());
    return;
}
```

# 32. 最长有效括号

给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

示例 1:

输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
示例 2:

输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"

解法1：括号的合法性可以设定为，'('为1，')'为-1，一个合法的括号串的和是0，同时从头计算串的值，在任意一步都不小于0。基于这样的0和思想，可以遍历整个串，从当前字符向后逐步试探是否合法。当查找到合法串后可以直接跳过对这部分的重复计算。时间复杂度其实是O(n).

```cpp
int longestValidParentheses(string s) {
    if (s.size() <= 1)return 0;
    int iIndex = 0, iMax = 0;
    for (; iIndex < s.size(); iIndex++) {
        int iTarget = iIndex,iRight = iIndex;
        if (s.at(iIndex) == ')') {
            continue;
        }
        else{
            int iCurrent = 0;
            while (iRight != s.size()) {
                if (s.at(iRight) == '(') {
                    iCurrent++;
                    iRight++;
                }
                else {
                    iCurrent--;
                    if (iCurrent == 0) {
                        if (iRight - iIndex > iMax) {
                            iMax = iRight - iIndex + 1;
                            iTarget = iRight;
                        }
                    }
                    if (iCurrent < 0)
                        break;
                    iRight++;
                }
            }
        }
        iIndex = iTarget;
    }
    return iMax;
}
```

解法2：利用堆栈。基本思想是当遇到'('时进行入栈，遇到')'进行出栈，并维护一个当前计数位置，当出现了多余的')'时认为计数应重新开始。其中的关键在栈是否为空会对最大值计算有影响，当栈为空时从start到当前位置都是合法的，当栈不为空时栈顶元素到当前位置是合法的。这个算法只需要进行一次遍历，时间复杂度为O(n).

```cpp
int longestValidParentheses(string s) {
    stack<int> sKuohao;
    int iMax = 0;
    int iStart= 0;
    for (int i = 0; i < s.size(); i++) {
        if (s.at(i) == '(') {
            sKuohao.push(i);
            continue;
        }
        else {
            if (!sKuohao.empty()) {
                sKuohao.pop();
                if (sKuohao.empty())
                    iMax = max(i - iStart + 1, iMax);
                else
                    iMax = max(i - sKuohao.top(), iMax);
            }
            else
                iStart = i + 1;
        }
    }
    return iMax;
}
```

# 33. 搜索旋转排序数组

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

示例 1:

输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
示例 2:

输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1

题解：常见的排序算法有遍历、二分、二叉搜索树、哈希表，遍历的时间复杂度为O(n)，没有其他要求,二分的时间复杂度为O(log n)，要求有序,二叉搜索树的时间复杂度为O(log n)，要求有二叉搜索树的结构,哈希表的时间复杂度为O(1),要求有哈希表的结构。所以我们只能选择二分。
由于发生了选择，需要找到旋转的位置，对该位置两段的两个数组分布进行二叉查找即可（其实也可以通过比较最大最小值进行预先判断减少二分次数）。

```cpp
int binarySearch(vector<int> &nums, int target) {
    int iLeft = 0, iRight = nums.size() - 1, iMid = 0;
    while (iLeft <= iRight) {
        iMid = (iLeft + iRight) / 2;
        if (nums.at(iMid) == target)
            return iMid;
        if (nums.at(iMid) < target) {
            iLeft = iMid + 1;
            continue;
        }
        else {
            iRight = iMid - 1;
            continue;
        }
    }
    return -1;
}
int search(vector<int>& nums, int target) {
    if (nums.empty())
        return -1;
    if (nums.size() == 1) {
        if (nums.front() == target)
            return 0;
        else
            return -1;
    }
    if (nums.back() > nums.front())
        return binarySearch(nums, target);
    int iRight = nums.size() - 1, iLeft = 0, iMid = (iRight + iLeft) / 2;
    while (1) {
        iMid = (iLeft + iRight) / 2;
        if (nums.at(iMid) == target)
            return iMid;
        if (iMid == 0 || iMid == nums.size() - 1 || nums.at(iMid) < nums.at(iMid - 1)) {
            break;
        }
        else {
            if (nums.at(iMid) > nums.at(iRight)) {
                iLeft = iMid + 1;
                continue;
            }
            else {
                iRight = iMid - 1;
                continue;
            }
        }
    }
    if (iMid == 0)iMid = 1;
    vector<int>temp(nums.begin(), nums.begin() + iMid);
    vector<int>temp1(nums.begin() + iMid, nums.end());
    int index = binarySearch(temp, target);
    if (index != -1)
        return index;
    else {
        index = binarySearch(temp1, target);
        if (index != -1)
            return iMid + index;
        else
            return -1;
    }
    return -1;
}
```

# 34. 在排序数组中查找元素的第一个和最后一个位置

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

你的算法时间复杂度必须是 O(log n) 级别。

如果数组中不存在目标值，返回 [-1, -1]。

示例 1:

输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
示例 2:

输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]

题解：进行二分查找，如果能找到目标则向两边检索。最坏情况的时间复杂度为O(n)，一般情况为O(log n).

```cpp
int binarySearch(vector<int> &nums, int target) {
    int iLeft = 0, iRight = nums.size() - 1, iMid = 0;
    while (iLeft <= iRight) {
        iMid = (iLeft + iRight) / 2;
        if (nums.at(iMid) == target)
            return iMid;
        if (nums.at(iMid) < target) {
            iLeft = iMid + 1;
            continue;
        }
        else {
            iRight = iMid - 1;
            continue;
        }
    }
    return -1;
}
vector<int> searchRange(vector<int>& nums, int target) {
    int iIndex = binarySearch(nums, target);
    vector<int>vAns = { -1,-1 };
    if (iIndex == -1)
        return vAns;
    else {
        int iLeft = iIndex, iRight = iIndex;
        while (iLeft >= 0 && nums.at(iLeft) == target) {
            vAns.front() = iLeft;
            iLeft--;
        }
        while (iRight < nums.size() && nums.at(iRight) == target) {
            vAns.back() = iRight;
            iRight++;
        }
    }
    return vAns;
}
```

# 49. 字母异位词分组

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

示例:

输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]

解法1：本题的关键在于如何判断两个string是异位词，第一种方法是两个词如果经过排序是一样的，则是异位词。有一点有意思，就是存放key-val用的如果是哈希表反而比红黑树要更慢，可能的解释是对于string的散列计算会比较耗时，但是比较比较不耗时。

```cpp

vector<vector<string>> vAns;
unordered_map<string, vector<string>> hmAll;
for (int i = 0; i < strs.size(); i++) {
    string s = strs.at(i);
    sort(s.begin(), s.end());
    hmAll[s].push_back(strs.at(i));
}
for (auto iter = hmAll.begin(); iter != hmAll.end(); iter++) {
    vAns.push_back(iter->second);
}
return vAns;

```

解法2：第二种方法是对每一个字母赋一个素数，用这个代表这个单词的每一个字母的素数做乘积，如果乘积一样则认为是异位词。有一点也比较有趣，在这样解法下，哈希表表现比红黑树表现好很多，可以认为是对double计算散列是容易的，因此哈希表的优势较大。本解法需要注意存储乘积的变量的大小，int和long类型都不足以存放下，我的代码用的是double.

```cpp
vector<vector<string>> vAns;
unordered_map<double, vector<string>>hmAll;
map<char, int> hmNum = {
    {'a',2},{ 'b',3 },{ 'c',5 },{ 'd',7 },{ 'e',11},{ 'f',13 },{ 'g',17 },{ 'h',19 },{ 'i',23 },{ 'j',29 },{ 'k',31 },
    { 'l',37 },{ 'm',41 },{ 'n',43 },{ 'o',47 },{ 'p',53 },{ 'q',59 },{ 'r',61 },{ 's',67 },{ 't',71 },{ 'u',73 },{ 'v',79 },
    { 'w',83 },{ 'x',89 },{ 'y',97 },{ 'z',101 }
};
for (int i = 0; i < strs.size(); i++) {
    double sum = 1;
    for (int j = 0; j < strs.at(i).size(); j++) {
        sum *= hmNum[strs.at(i).at(j)];
    }
    hmAll[sum].push_back(strs.at(i));
}
for (auto iter = hmAll.begin(); iter != hmAll.end(); iter++) {
    vAns.push_back(iter->second);
}
return vAns;
```

# 53. 最大子序和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:

输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

题解：对连续子数组增长有益的只有正数，当一个子序的和已经小于0时就不再有增益的效果，抛弃该子序，进行下一子序的检索。时间复杂度为O(n).

```cpp
int maxSubArray(vector<int>& nums) {
    if (nums.size() == 1)return nums.front();
    vector<int> vMax, vTemp;
    int iMax = nums.front(), iSum = 0;
    for (int i = 0; i < nums.size(); i++) {
        iSum += nums.at(i);
        if (iSum > iMax) {
            iMax = iSum;
        }
        if (iSum < 0) {
            iSum = 0;
            continue; 
        }
    }
    return iMax;
}
```

附上我看到的leecode上的一个题解，很喜欢这种清晰的代码，学习学习。

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0;
        int res = nums[0];
        for (int num : nums) {
            sum = sum > 0 ? sum + num : num;
            if (res < sum) {
                res = sum;
            }
        }
        return res;
    }
};
```

# 55. 跳跃游戏

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

示例 1:

输入: [2,3,1,1,4]
输出: true
解释: 从位置 0 到 1 跳 1 步, 然后跳 3 步到达最后一个位置。
示例 2:

输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。

题解：动态规划问题的核心在于判定解能否由之前的解组合而成。本题就是这样的例子。动态规划有两种思路，一是自顶向下，一是自下向上，自顶向下往往使用递归的方法，在建立自顶向下的过程中对过程中的所有解进行记录，自底向上的方法则是采用递推的方法，直接由初始状态进行逐项推进。

解法1：自顶向下的递归。会超时。

```cpp
unordered_set<int> hsGood;
bool canJump(vector<int>& nums, int index) {
    if (hsGood.count(index))
        return true;
    bool flag = false;
    for (int i = 1; i <= nums[index]; i++) {
        flag = canJump(nums, index + i);
        if (flag == true) {
            hsGood.insert(index);
            return true;
        }
    }
    return false;
}
bool canJump1(vector<int>& nums) {
    int iNear = nums.size() - 1;
    if (nums.empty())
        return false;
    hsGood.insert(nums.size() - 1);
    return canJump(nums, 0);
}
```

解法2：自底向上的递推。

```cpp
unordered_set<int> hsGood;
bool canJump(vector<int>& nums) {
    int iNear = nums.size() - 1;
    if (nums.empty())
        return false;
    hsGood.insert(nums.size() - 1);
    for (int i = nums.size() - 2; i >= 0; i--) {
        if (nums[i] >= iNear - i) {
            iNear = i;
            hsGood.insert(i);
        }
    }
    if (hsGood.count(0))
        return true;
    else
        return false;
}
```

# 56. 合并区间

给出一个区间的集合，请合并所有重叠的区间。

示例 1:

输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
示例 2:

输入: [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。

题解：排序+合并。思路很简单。事件复杂度值得注意的是c++的用细要求严格弱序，即 a>b 和 b>a 不能同时为真。 stl判断等价用的是 `!(a<b) && !(b<a)`。因此自定义的cmp函数需要严格遵守遮掩工单严格弱序关系，指明当两者相等时的处理结果，否则会提示`invalid comparator`错误。

```cpp
static bool cmp(vector<int>&a, vector<int> &b) {
    if (a.front() >= b.front())
        return false;
    else
        return true;
}
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    if (intervals.size() <= 1)return intervals;
    sort(intervals.begin(), intervals.end(), cmp);
    vector<vector<int>>vAns;
    vAns.push_back(intervals.front());
    for (vector<int> vInterval : intervals) {
        if (vInterval.front() >= vAns.back().front() && vInterval.front() <= vAns.back().back()) {
            vAns.back().back() = max(vInterval.back(), vAns.back().back());
        }
        else
        {
            vAns.push_back(vInterval);
        }
    }
    return vAns;
}
```
# 64. 最小路径和
给定一个包含非负整数的 m x n 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。

示例:

输入:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 7
解释: 因为路径 1→3→1→1→1 的总和最小。

题解：最小角的最小路径值只取决于他上方的元素左侧的元素那个更小.即f(i,j)=min(f(i-1,j)+data(i-1,j),f(i,j-1)+data(i,j-1))；所以可以用动态规划的方式解决，一种是自顶向下的递归方式，一种是自底向上的递推方式。

递归方式：

```cpp
unordered_map<string, int> hmMin;
int minPathSum(vector<vector<int>>& grid, int x, int y) {
    string s = to_string(x) + "," + to_string(y);
    if (hmMin.count(s))
        return hmMin[s];
    int ans = 0;
    if (x == 0 && y == 0) {
        ans = grid[0][0];
    }
    else if (x == 0) {
        ans = minPathSum(grid, x, y - 1) + grid[x][y];
    }
    else if (y == 0) {
        ans =  minPathSum(grid, x - 1, y) + grid[x][y];
    }
    else
        ans= min(minPathSum(grid, x, y - 1) + grid[x][y], minPathSum(grid, x - 1, y) + grid[x][y]);
    hmMin[s] = ans;
    return ans;
}
int minPathSum(vector<vector<int>>& grid) {
    if (grid.empty())
        return 0;
    int y = grid.front().size()-1;
    int x = grid.size()-1;
    return minPathSum(grid, x, y);
}
```

# 70. 爬楼梯

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：

输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。

1. 1 阶 + 1 阶
2. 2 阶
示例 2：

输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。

1. 1 阶 + 1 阶 + 1 阶
2. 1 阶 + 2 阶
3. 2 阶 + 1 阶


题解：到达一个台阶有两种方式，一是从前一节台阶上来，一是从前两节台阶上来。因此到达第n个台阶的方式 = 到达n-1台阶的方式+到达n-2台阶的方式。用动态规划的思想可以解决。

```cpp
unordered_map<int, int> hmStairs;
int climbStairs(int n) {
    int ans = 0;
    if (hmStairs.count(n))
        return hmStairs[n];
    else {
        if (n > 1) {
            ans = climbStairs(n - 1) + climbStairs(n - 2);
        }
        else if (n == 1)
            ans = 1;
        else if (n == 0)
            ans = 1;
    }
    hmStairs[n] = ans;
    return ans;
}
```

# 72. 编辑距离

给定两个单词 word1 和 word2，计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符
示例 1:

输入: word1 = "horse", word2 = "ros"
输出: 3
解释: 
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
示例 2:

输入: word1 = "intention", word2 = "execution"
输出: 5
解释: 
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')

并不知道这题如何处理，官方题解让我见识了什么叫从1+1到微积分。在leetcode网站上看到了jianchao-li写的题解，感觉很棒，简单搬运和修改了一下。

题解：为应用动态规划，我们定义 `dp[i][j]` 为从 `word1[0..i)` 到`word2[0..j)`转换的的最小次数。

对于基本的情况，将一个字符串转换为一个空的字符串，所需操作的最小值就是字符串长度本身，因此很明显： `dp[i][0]=i,dp[0][j]=j`
对于一般情况，从 `word1[0..i)` 到 `word2[0..j)` ，假设我们已知了从 `word1[0..i-1)` 到 `word2[0..j-1)` 转换的次数，可以分两种情况讨论。

1. if `word1[i] == word2[j]` 
此时的的情况就不用多讲，直接`dp[i][j]=dp[i-1][j-1]`就可以了。
2. if `word1[i] != word2[j]` 
此时的情况比较复杂，有以下三种可能性。
(1) 替换。如ror和ros，此时进行替换操作，r->s，此时`dp[i][j]=dp[i-1][j-1] + 1`;
(2) 删除。如ross和ros，此时进行删除操作，delete s,此时`dp[i][j]=dp[i-1][j] + 1`
(3) 插入。如ro和ros，此时进行插入操作，insert s,此时`dp[i][j]=dp[i][j-1] + 1`

此时可以看出当`word1[i] != word2[j]` ，`dp[i][j] = min(dp[i][j]=dp[i-1][j-1] , dp[i][j]=dp[i][j-1] , dp[i][j]=dp[i-1][j]) + 1`

自底向上的解法

```cpp
int minDistance(string word1, string word2) {
    int m = word1.size(), n = word2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    for (int i = 1; i <= m; i++) {
        dp[i][0] = i;
    }
    for (int j = 1; j <= n; j++) {
        dp[0][j] = j;
    }
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1[i - 1] == word2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            }
            else {
                dp[i][j] = min(dp[i - 1][j - 1], min(dp[i][j - 1], dp[i - 1][j])) +1;
            }
        }
    }
    return dp[m][n];
}
```

# 75. 颜色分类

给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

注意:
不能使用代码库中的排序函数来解决这道题。

示例:

输入: [2,0,2,1,1,0]
输出: [0,0,1,1,2,2]

题解：我的思路是统计各个数值的量，然后在原数组的基础上重构它。这里引入了外部的一个map，不算是原地的方式。如果采用原地的，则是使用0指针和2指针来控制。

```cpp
void sortColors(vector<int>& nums) {
    map<int, int>hmNums;
    hmNums[0] = 0;
    hmNums[1] = 0;
    hmNums[2] = 0;
    for (int i = 0; i < nums.size(); i++) {
        hmNums[nums[i]]++;
    }
    int i = 0;
    for (; i < hmNums[0]; i++)
        nums[i] = 0;
    for (; i < hmNums[0]+hmNums[1]; i++)
        nums[i] = 1;
    for (; i < nums.size(); i++)
        nums[i] = 2;
}
```

# 121. 买卖股票的最佳时机

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

示例 1:

输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
示例 2:

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

题解：持续记录最大值和最小值的下标，当更新最小值的时候最大值也重置，因为不允许最小值的下标比最大值的下标大。

```cpp
int maxProfit(vector<int>& prices) {
    int minIndex = 0, maxIndex = 0, maxValue = 0;
    for (int i = 0; i < prices.size();i++) {
        if (prices[i] < prices[minIndex]) {
            minIndex = i;
            maxIndex = i;
        }
        if (prices[i] > prices[maxIndex])
            maxIndex = i;
        maxValue = max(maxValue, prices[maxIndex] - prices[minIndex]);
    }
    return maxValue;
}
```

# 78. 子集

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

示例:

输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]

题解：可以通过一种类似动态规划的思想考虑，即从 [] 开始，每次向其添加一个元素，生成一个新的集合，再在这个集合的基础上进行在增加一个元素，当然这个过程要保证不重复。还有一种基于位的考虑，比如一个 [1,2,3],可以通过对一个000的三位数二进制数进行枚举，每一个1对应选取，0对应不选，就可以判断出所有的情况。

```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> vAns;
    unordered_map<int, int> hmNums;
    for (int i = 0; i < nums.size(); i++){
        hmNums[nums[i]] = i;
    }
    int iStartIndex = 0, iEndIndex = 1,iCount=0;
    for (int i = 0; i <= nums.size(); i++) {
        if (i == 0) {
            vAns.push_back(vector<int>());
            continue;
        }
        else {
            for (int j = iStartIndex; j < iEndIndex; j++) {
                if (vAns[j].empty()) {
                    for (int k = 0; k < nums.size(); k++) {
                        vector<int> temp = vAns[j];
                        temp.push_back(nums[k]);
                        vAns.push_back(temp);
                        iCount++;
                    }
                }
                else {
                    for (int k = hmNums[vAns[j].back()] + 1; k < hmNums.size(); k++) {
                        vector<int> temp = vAns[j];
                        temp.push_back(nums[k]);
                        vAns.push_back(temp);
                        iCount++;
                    }
                }
            }
        }
        iStartIndex = iEndIndex;
        iEndIndex += iCount;
        iCount = 0;
    }
    return vAns;
}
```

# 79. 单词搜索

给定一个二维网格和一个单词，找出该单词是否存在于网格中。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

示例:

board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true.
给定 word = "SEE", 返回 true.
给定 word = "ABCB", 返回 false.

题解：通过深度优先搜索的方式进行查询，记忆历史路径防止踩到来过的点，每次判断自己四周的四个点是否是目标点，如果是则进行进一步的搜索。`my code is ugly！`

```cpp
unordered_map<char, vector<pair<int, int>>> hmBoard;
bool find(vector<vector<char>>& board,string &word, int curIndex,set<pair<int,int>> sGone,pair<int,int>p) {
    if (curIndex == word.size()-1)
        return true;
        sGone.insert(p);
        pair<int, int>p1=p, p2=p, p3=p, p4=p;
        p1.first -= 1;
        p2.second += 1;
        p3.first += 1;
        p4.second -= 1;
        if (!sGone.count(p1)&&p1.first >= 0 && p1.first < board.size() && p1.second >= 0 && p1.second < board.front().size()&&board[p1.first][p1.second]==word[curIndex+1]) {
            if( find(board, word, curIndex+1, sGone,p1))
                return true;
        }
        if (!sGone.count(p2) && p2.first >= 0 && p2.first < board.size() && p2.second >= 0 && p2.second < board.front().size() && board[p2.first][p2.second] == word[curIndex + 1]) {
            if (find(board, word, curIndex + 1, sGone, p2))
                return true;
        }
        if (!sGone.count(p3) && p3.first >= 0 && p3.first < board.size() && p3.second >= 0 && p3.second < board.front().size() && board[p3.first][p3.second] == word[curIndex + 1]) {
            if (find(board, word, curIndex + 1, sGone, p3))
                return true;
        }
        if (!sGone.count(p4) && p4.first >= 0 && p.first < board.size() && p4.second >= 0 && p4.second < board.front().size() && board[p4.first][p4.second] == word[curIndex + 1]) {
            if (find(board, word, curIndex + 1, sGone, p4))
                return true;
        }
        return false;

}
bool exist(vector<vector<char>>& board, string word) {
    for (int i = 0; i < board.size(); i++) {
        for (int j = 0; j < board[i].size(); j++) {
            hmBoard[board[i][j]].push_back(pair<int, int>(i, j));
        }
    }
    for (pair<int, int> p : hmBoard[word.front()]) {
        if (find(board, word, 0, set<pair<int, int>>(),p))
            return true;
    }
    return false;
}
```

# 94. 二叉树的中序遍历

给定一个二叉树，返回它的中序 遍历。

示例:

输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]

题解1：递归的思路很简单，按照左子树，中间，右子树的顺序进行即可。

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
vector<int> ans;
void write(TreeNode* root) {
    if (root->left != NULL)
        inorderTraversal(root->left);
    ans.push_back(root->val);
    if ((root->right != NULL))
        inorderTraversal(root->right);
}
vector<int> inorderTraversal(TreeNode* root) {
    if (root == NULL)
        return vector<int>();
    write(root);
    return ans;
}
```

题解2： 非递归的方法，通过栈的思路实现，每次将当前节点入栈，如果当前节点有左子树，就令左节点为当前节点，如果没有，就先输出，再从栈顶取元素，当做当前节点。直到当前节点和栈都为空时结束。

```cpp
vector<int> inorderTraversal(TreeNode* root) {
    if (root == NULL)
        return vector<int>();
    vector<int> ans;

    stack<TreeNode*> sT;
    TreeNode* curNode = root;
    while (curNode != NULL || !sT.empty()) {
        while (curNode != NULL) {
            sT.push(curNode);
            curNode = curNode->left;
        }
        ans.push_back(sT.top()->val);
        curNode = sT.top();
        sT.pop();
        curNode = curNode->right;
    }
    return ans;
}
```

# 96. 不同的二叉搜索树

给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

示例:

输入: 3
输出: 5
解释:
给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

题解：感觉上是可以用dp解决的。因为当根确定时，左子树和右子树的节点内容就是确定的了。可以遍历1-n来做根节点，求和就是总的种类数目。
设G(n)表示有n个节点时二叉树的种类。F(i,n)表示n个节点时，i做根节点时的种类数目。
G(n) = ∑F(i,n),i∈[1,...,n]
F(i,n) = G(i-1)*G(n-i)
因此：G(n) = ∑G(i-1)*G(n-i) , i∈[1,...,n]

```cpp
int numTrees(int n) {
    if (n <= 1)return 1;
    unordered_map<int, int>hm;
    hm[0] = 1;
    hm[1] = 1;
    int iIndex = 2;
    while (iIndex <= n) {
        for (int i = 0; i < iIndex; i++) {
            hm[iIndex] += hm[i] * hm[iIndex - 1 - i];
        }
        iIndex++;
    }
    return hm[n];
}
```

# 98. 验证二叉搜索树

    给定一个二叉树，判断其是否是一个有效的二叉搜索树。
    假设一个二叉搜索树具有如下特征：

    节点的左子树只包含小于当前节点的数。
    节点的右子树只包含大于当前节点的数。
    所有左子树和右子树自身必须也是二叉搜索树。
    示例 1:

    输入:
        2
    / \
    1   3
    输出: true
    示例 2:

    输入:
        5
    / \
    1   4
         / \
        3   6
    输出: false
    解释: 输入为: [5,1,4,null,null,3,6]。
         根节点的值为 5 ，但是其右子节点值为 4 。

题解：规则非常明确，需要判定的项是左子，右子是否满足小于和大于的规则。更进一步的条件是整个左子树小于根，整个右子树大于根，这就要求根节点的数值必须能传递到对应的判定位置，因此每个节点都会有一个范围的限制，是左节点的，根节点是最大值，同时也要大于根节点范围的最小值，是右节点的，根节点是最小值，同时也要小于根节点范围的最大值，相当于是逐步更新每一个节点的范围。（一种基于中序遍历的思路很棒，二叉搜索数在中序遍历上会是个升序排列的有序数组）。

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

bool isBST(TreeNode* cur, long max, long min) {
    if (cur == NULL)
        return true;
    if (cur->val<max && cur->val>min) {
        if (isBST(cur->left, cur->val, min))
            return isBST(cur->right, max, cur->val);
    }
    return false;
}

bool isValidBST(TreeNode* root) {
    if (root == NULL)
        return true;
    else {
        if (isBST(root->left, root->val, LONG_MIN))
            return isBST(root->right, LONG_MAX, root->val);
    }
    return false;
}
```

# 101. 对称二叉树

    给定一个二叉树，检查它是否是镜像对称的。

    例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

        1
    / \
    2   2
    / \ / \
    3  4 4  3
    但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

        1
    / \
    2   2
    \   \
    3    3
    说明:

    如果你可以运用递归和迭代两种方法解决这个问题，会很加分。

题解：对称二叉树的关键在于有两个镜像的左右子树，可以逐层判断是否符合镜像的条件，设定两个栈，一个保存当前层节点，一个保存下一层的节点，当两个栈都为空时相当于比较完成，期间发生了不对称的情况则认为不对称。

```cpp
bool isSymmetric(TreeNode* root) {
    if (root == NULL)
        return true;

    stack<TreeNode*> stLeft, stLeftTemp;
    stack<TreeNode*> stRight, stRightTemp;

    stLeft.push(root->left);
    stRight.push(root->right);

    while (!stLeft.empty() || !stLeftTemp.empty()) {
        while (!stLeft.empty()) {
            if ((stLeft.top() == NULL && stRight.top() != NULL) || (stLeft.top() != NULL && stRight.top() == NULL))
                return false;
            if ((stLeft.top() == NULL && stRight.top() == NULL) || (stLeft.top()->val == stRight.top()->val)) {
                if (stLeft.top() != NULL) {
                    stLeftTemp.push(stLeft.top()->left);
                    stLeftTemp.push(stLeft.top()->right);
                    stLeft.pop();
                    stRightTemp.push(stRight.top()->right);
                    stRightTemp.push(stRight.top()->left);
                    stRight.pop();
                }
                else {
                    stLeft.pop();
                    stRight.pop();

                }
            }
            else {
                return false;
            }
        }
        swap(stLeft, stLeftTemp);
        swap(stRight, stRightTemp);
    }
    return true;
}
```

# 102. 二叉树的层次遍历

    给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

    例如:
    给定二叉树: [3,9,20,null,null,15,7],

        3
    / \
    9  20
        /  \
    15   7
    返回其层次遍历结果：

    [
    [3],
    [9,20],
    [15,7]
    ]

题解：层序遍历的关键点就在于逐层下降，用上一层的信息来提前控制下一层的数据。在这里我是用了两个队列分别保存当前层和一下层，这样可以方便的控制每层的数据，当然也可只用一个队列，顺序放入就行，但是需要对分层进行判断。

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> ans;
    if (root == NULL)
        return ans;
    queue<TreeNode*> qTree, qTreetemp;
    qTree.push(root);
    while (!qTree.empty() || !qTreetemp.empty()) {
        vector<int> temp;
        while (!qTree.empty()) {
            temp.push_back(qTree.front()->val);
            if (qTree.front()->left != NULL)
                qTreetemp.push(qTree.front()->left);
            if (qTree.front()->right != NULL)
                qTreetemp.push(qTree.front()->right);
            qTree.pop();
        }
        ans.push_back(temp);
        swap(qTree, qTreetemp);
    }
    return ans;
}
```

# 104. 二叉树的最大深度

    给定一个二叉树，找出其最大深度。

    二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

    说明: 叶子节点是指没有子节点的节点。

    示例：
    给定二叉树 [3,9,20,null,null,15,7]，

        3
    / \
    9  20
        /  \
    15   7
    返回它的最大深度 3 。

题解1：其实求深度无非是深度优先遍历或者广度优先遍历。我们先用广度优先的方式逐层下降找一下（其实和前两道用到时一种方法），用两个队列来逐层下降。

```cpp
int maxDepth(TreeNode* root) {
    if (root == NULL)
        return 0;
    int max = 0;
    queue<TreeNode*> qTree, qTreetemp;
    qTree.push(root);
    while (!qTree.empty() || !qTreetemp.empty()) {
        vector<int> temp;
        while (!qTree.empty()) {
            temp.push_back(qTree.front()->val);
            if (qTree.front()->left != NULL)
                qTreetemp.push(qTree.front()->left);
            if (qTree.front()->right != NULL)
                qTreetemp.push(qTree.front()->right);
            qTree.pop();
        }
        max++;
        swap(qTree, qTreetemp);
    }
    return max;
}
```

题解2：深度优先搜索。我们这里用递归的方式，一个节点的深度=max（左子树深度，右子树深度）+1.

```cpp
int maxDepth(TreeNode* root) {
    if (root == NULL)
        return 0;
    return max(maxDepth(root->left), maxDepth(root->right)) + 1;
}
```

# 136. 只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

示例 1:

输入: [2,2,1]
输出: 1
示例 2:

输入: [4,1,2,1,2]
输出: 4

题解1：通过维护一个set(hash)，第一次遇见，加入，第二次遇见，滚粗。最终剩下的就是我们的唯一的崽。

```cpp
int singleNumber(vector<int>& nums) {
    unordered_set<int> s1;
    for (auto n : nums) {
        if (s1.count(n))
        {
            s1.erase(n);
        }
        else
            s1.insert(n);
    }
    return *s1.begin();
}
```

题解2：通过异或的方式。异或满足，交换律：a ^ b ^ c <=> a ^ c ^ b，任何数与0异或 0 ^ n = n，相同的数异或为0 n ^ n => 0

```cpp
int singleNumber2(vector<int>& nums) {
    int result = 0;
    for (auto i : nums)
    {
        result ^= i;
    }
    return result;
}
```