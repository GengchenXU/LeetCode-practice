题目描述
======================
有台奇怪的打印机有以下两个特殊要求：

打印机每次只能打印同一个字符序列。
每次可以在任意起始和结束位置打印新字符，并且会覆盖掉原来已有的字符。
给定一个只包含小写英文字母的字符串，你的任务是计算这个打印机打印它需要的最少次数。

示例 1:

输入: "aaabbb"
输出: 2
解释: 首先打印 "aaa" 然后打印 "bbb"。
示例 2:

输入: "aba"
输出: 2
解释: 首先打印 "aaa" 然后在第二个位置打印 "b" 覆盖掉原来的字符 'a'。
提示: 输入字符串的长度不会超过 100。

思路分析：
========================================
使用动态规划法。dp[i][j]代表的是s[i, j]段打印的最少次数
状态转移方程：打印s[i, j]这段共有三种情况

第一种：dp[i][j] = 1 + dp[i + 1][j];//i单独打印， s[i + 1, j]段另外打印
第二种：dp[i][j] = min(dp[i][j], dp[i + 1][k] + dp[k + 1][j]);//dp[i + 1][k]代表将i放到[i+ 1, k]一起打印，dp[k + 1][j]代表[k + 1, j]另外打印，（s[i] == s[k])
第三种：dp[i][j] = min(dp[i][j], dp[i + 1][j]);//dp[i + 1][j]代表将i放入[j + 1, i]一起打印(s[i] == s[j])


### Ⅰ
```cpp
class Solution {
public:
    int strangePrinter(string s) {
        int strSize = s.size();
        if (strSize == 0){
            return 0;
        }
        vector<vector<int>> dp(strSize, vector<int>(strSize, INT_MAX));//dp[i][j]代表的是s[i, j]段打印的最少次数
        //只打印一个元素，只需要一次即可
        for (int i = 0; i < strSize; ++i){
            dp[i][i] = 1;
        }
        for (int i = strSize - 2; i >= 0; --i){
            for (int j = i + 1; j < strSize; ++j){
                //两层for循环穷举区间段[i, j]
                dp[i][j] = 1 + dp[i + 1][j];//i单独打印， s[i + 1, j]段另外打印
                //尝试将s[i, j]分成s[i, k]和[k + 1, j]
                for (int k = i + 1; k < j; ++k){
                    if (s[i] == s[k]){
                        //dp[i + 1][k]代表将i放到[i+ 1, k]一起打印，dp[k + 1][j]代表[k + 1, j]另外打印
                        dp[i][j] = min(dp[i][j], dp[i + 1][k] + dp[k + 1][j]);
                    }
                }
                if (s[i] == s[j]){
                    //dp[i + 1][j]代表将i放入[j + 1, i]一起打印
                    dp[i][j] = min(dp[i][j], dp[i + 1][j]);
                }
            }
        }
        return dp[0][strSize - 1];
    }
};
```
###Ⅱ
```cpp
class Solution {
public:
    int strangePrinter(string s) {
        if(s.size()==0) return 0;
        string pres="";
        pres+=s[0];    //除去重复相邻元素
        for(int i=1;i<s.size();i++)
        {
            if(s[i]!=pres[pres.size()-1])
                pres+=s[i];
        }
        vector<vector<int>> dp(pres.size(),vector<int>(pres.size(),INT_MAX));
        for(int i=0;i<pres.size();i++)
        {
            dp[i][i]=1;
        }
        for(int i=pres.size()-2;i>=0;i--)
        {
            for(int j=i+1;j<pres.size();j++)
            {
                dp[i][j]=1+dp[i+1][j];
                for(int k=i+1;k<j;k++)
                {
                    if(pres[i]==pres[k])
                    {
                        dp[i][j]=min(dp[i][j],dp[i+1][k]+dp[k+1][j]);
                    }
                }
                if(pres[i]==pres[j])
                    dp[i][j]=min(dp[i][j],dp[i+1][j]);
            }
        }
        return dp[0][pres.size()-1];
    }
};
```
