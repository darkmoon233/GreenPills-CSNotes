
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