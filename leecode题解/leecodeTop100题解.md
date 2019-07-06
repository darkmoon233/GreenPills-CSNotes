
所有题目来源:力扣（LeetCode）
链接：https://leetcode-cn.com/problems/minimum-path-sum
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

![phoneNumber](https://github.com/darkmoon233/GreenPills-CSNotes/blob/master/images/leecode17phonenumber.png)

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
1.  1 阶 + 1 阶
2.  2 阶
示例 2：

输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶


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
75. 颜色分类

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