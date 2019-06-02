
# 第139周周赛

## 5076. 字符串的最大公因子

题目难度 Easy
对于字符串 S 和 T，只有在 S = T + ... + T（T 与自身连接 1 次或多次）时，我们才认定 “T 能除尽 S”。

返回字符串 X，要求满足 X 能除尽 str1 且 X 能除尽 str2。

示例 1：

输入：str1 = "ABCABC", str2 = "ABC"
输出："ABC"
示例 2：

输入：str1 = "ABABAB", str2 = "ABAB"
输出："AB"
示例 3：

输入：str1 = "LEET", str2 = "CODE"
输出：""
 
提示：

1 <= str1.length <= 1000
1 <= str2.length <= 1000
str1[i] 和 str2[i] 为大写英文字母

```cpp
class Solution {
public:
    vector<string> sonOfString(string s) {
    vector<string> sons;
    if (s.size() == 0)return sons;
    int iFront = 0, iBack = 0;
    while (iBack - iFront < s.size()) {
        if (s.size() % (iBack+1) == 0) {
            string temp = s.substr(0, iBack + 1);
            int index = 0;
            while (index < s.size()&& temp == s.substr(index, iBack+1)) {
                index += iBack + 1;
            }
            if (index == s.size())
                sons.push_back(s.substr(0, iBack + 1));
        }
        iBack++;
    }
    return sons;
}
    string gcdOfStrings(string str1, string str2) {
        vector<string>v1 = sonOfString(str1);
    vector<string>v2 = sonOfString(str2);
    string sMax;
    for (int i = 0; i < v1.size(); i++) {
        if (find(v2.begin(), v2.end(), v1.at(i))!=v2.end()&&v1.at(i).size()>sMax.size()) {
            sMax = v1.at(i);
        }
    }
    return sMax;
    }
};
```

## 5078. 负二进制数相加

题目难度 Medium
给出基数为 -2 的两个数 arr1 和 arr2，返回两数相加的结果。

数字以 数组形式 给出：数组由若干 0 和 1 组成，按最高有效位到最低有效位的顺序排列。例如，arr = [1,1,0,1] 表示数字 (-2)^3 + (-2)^2 + (-2)^0 = -3。数组形式 的数字也同样不含前导零：以 arr 为例，这意味着要么 arr == [0]，要么 arr[0] == 1。

返回相同表示形式的 arr1 和 arr2 相加的结果。两数的表示形式为：不含前导零、由若干 0 和 1 组成的数组。

示例：

输入：arr1 = [1,1,1,1,1], arr2 = [1,0,1]
输出：[1,0,0,0,0]
解释：arr1 表示 11，arr2 表示 5，输出表示 16 。
 
提示：

1 <= arr1.length <= 1000
1 <= arr2.length <= 1000
arr1 和 arr2 都不含前导零
arr1[i] 为 0 或 1
arr2[i] 为 0 或 1

```cpp
class Solution {
public:
    vector<int> addNegabinary(vector<int>& arr1, vector<int>& arr2) {
            vector<int> vAns;
    int iJin = 0,iJie=0;
    if (arr1.size() == 0)return arr2;
    if (arr2.size() == 0)return arr1;
    if (arr1.size() > arr2.size()) {
        vector<int>temp(arr1.size() - arr2.size(),0);
        temp.insert(temp.end(),arr2.begin(),arr2.end());
        temp.swap(arr2);
    }
    if (arr2.size() > arr1.size()) {
        vector<int>temp(arr2.size() - arr1.size(),0);
        temp.insert(temp.end(), arr1.begin(), arr1.end());
        temp.swap(arr1);
    }
    for (int i = arr1.size() - 1; i >= 0||(i<0&&(iJin!=0||iJie!=0)); i--) {
        int iLeft, iRight;
        if (i < 0) 
            iLeft = iRight = 0;
        else {
            iLeft = arr1.at(i);
            iRight = arr2.at(i);
        }
        int temp = iLeft + iRight + iJie - iJin;
        if (temp == 0 || temp == 1) {
            vAns.push_back(temp);
            iJie = 0;
            iJin = 0;
            continue;
        }
        if (temp == 2) {
            vAns.push_back(0);
            iJie = 0;
            iJin = 1;
            continue;
        }
        if (temp == -1) {
            vAns.push_back(1);
            iJie = 1;
            iJin = 0;
            continue;
        }
        if (temp == 3) {
            vAns.push_back(1);
            iJie = 0;
            iJin = 1;
            continue;
        }
    }
    reverse(vAns.begin(), vAns.end());
    for (int i = 0; i < vAns.size() - 1; i++) {
        if (vAns.at(i) != 0)
            break;
        vAns.erase(vAns.begin() + i);
        i--;
    }
    return vAns;
    }
};
```