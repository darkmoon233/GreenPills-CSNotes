
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