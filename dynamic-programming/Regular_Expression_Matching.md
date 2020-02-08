题目描述
=======================================
给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

	>'.' 匹配任意单个字符
	>'*' 匹配零个或多个前面的那一个元素

所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

说明:

  s 可能为空，且只包含从 a-z 的小写字母。
	p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。

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
解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。

示例 3:
输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。

示例 4:
输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。

示例 5:
输入:
s = "mississippi"
p = "mis*is*p*."
输出: false

## 递归
首先是递归基：p如果为空 如果s是空返回true 否则返回false  
接着分情况讨论：
1.p长度大于1 且p的第二个字母是`*`, 代表0次或更多前面的字母，首先考虑0次，即可以将p的前面两个字母抹去不考虑，即递归匹配`isMatch(s, p.substr(2))` 
然后考虑多次，此时要考虑s的第一个字母是否等于p的第一个字母，或者p的第一个字母为`. `即可以匹配任何字母，如果相等就可以继续向下个字母推进，
匹配` isMatch(s.substr(1),p)`，但是p不能推进因为可以匹配无穷多个
2.剩下的情况归为一类即p长度为1 或者p长度大于1 但是p的第二个字母不是`* `处理方式：考虑s的第一个字母是否等于p的第一个字母，或者p的第一个字母为`.`
即可以匹配任何字母，如果相等就可以继续向下个字母推进，匹配`isMatch(s.substr(1),p.substr(1))`
```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        if(p.empty()) return s.empty();
        if(p.size() > 1 && p[1] == '*'){
            return isMatch(s, p.substr(2)) || 
                !s.empty() && (s[0] == p[0] || p[0] == '.') && isMatch(s.substr(1),p);
        } else {
            return !s.empty() && (s[0] == p[0] || p[0] == '.') && isMatch(s.substr(1),p.substr(1));
        }
    }
};
```
### java：
注意java是isEmpty()不是empty()
```java
class Solution {
    public boolean isMatch(String s, String p) {
        if(p.isEmpty()) return s.isEmpty();
        if(p.length() > 1 && p.charAt(1) == '*'){
            return isMatch(s, p.substring(2)) || 
                !s.isEmpty() && (s.charAt(0) == p.charAt(0) || p.charAt(0) == '.') && isMatch(s.substring(1),p);
        } else {
            return !s.isEmpty() && (s.charAt(0) == p.charAt(0) || p.charAt(0) == '.') && isMatch(s.substring(1),p.substring(1));
        }
    }
    
}
```
### Python：
not的用法python中not的用法
p[0] in {s[0], ‘.’}等价于(s[0] == p[0] || p[0] == ‘.’)
bool(s)来判断非空 在python中bool函数的取值方法
```python
class Solution(object):
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        if not p:
            return not s
        first_match = bool(s) and p[0] in {s[0], '.'}
        if len(p) > 1 and p[1] == '*':
            return (self.isMatch(s, p[2:])) or first_match and self.isMatch(s[1:], p)
        else:
            return first_match and self.isMatch(s[1:], p[1:])
 ```           
   
## 方法2：动态规划
定义一个二维的DP数组，其中dp[i][j]表示s[0,i)和p[0,j)是否match，然后有下面三种情况

dp[i][j] = dp[i - 1][j - 1], if p[j - 1] != ‘*’ && (s[i - 1] == p[j - 1] || p[j - 1] == ‘.’);  
dp[i][j] = dp[i][j - 2], if p[j - 1] == ‘*’ and the pattern repeats for 0 times;  
dp[i][j] = dp[i - 1][j] && (s[i - 1] == p[j - 2] || p[j - 2] == ‘.’), if p[j - 1] == ‘*’ and the pattern repeats for at least 1 times.  
c++:
注意&&的优先级高于|| 必要时加括号
```cpp
class Solution {
public:
    bool isMatch(string s, string p) { int m = s.size();
        int n = p.size();
        vector<vector<bool> > dp = vector(m + 1, vector<bool>(n + 1, false));
        dp[0][0] = true;
        for(int i = 0; i <= m; i++){
            for(int j = 1; j <= n; j++){
                if(j > 1 && p[j - 1] == '*'){
                    dp[i][j] = dp[i][j - 2] || (i > 0 && (s[i - 1] == p[j - 2] || p[j - 2] == '.') )&& dp[i -1][j];
                } else {
                    dp[i][j] = i > 0 && (s[i - 1] == p[j - 1] || p[j - 1] == '.') && dp[i -1][j - 1];
                }
            }
        }
        return dp[m][n];  
    }
};
```	
java:
```java
class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean [][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;
        for (int i = 0; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (j > 1 && p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i][j - 2] || (i > 0 && (s.charAt(i - 1) == p.charAt(j - 2) || p.charAt(j - 2) == '.') && dp[i - 1][j]);
                } else {
                    dp[i][j] = i > 0 && dp[i - 1][j - 1] && (s.charAt(i - 1) == p.charAt(j - 1) || p.charAt(j - 1) == '.');
                }
            }
        }
        return dp[m][n];
    }
    
}
```
### python:
注意
dp = [[False] * (n + 1) for i in range(m + 1)]
和
dp = [[False * (n + 1)]  for i in range(m + 1)] 的区别
当 n = 1 m = 2前者
[[False, False], [False, False], [False, False]]
后者
[0, 0, 0]
```python
class Solution(object):
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        m = len(s)
        n = len(p)
        dp = [[False] * (n + 1) for i in range(m + 1)]
        #print(dp[0][1])
       # print(dp)
        dp[0][0] = True
        for i in range(m + 1):
            for j in range(1, n + 1):
                print(i, j)
                if j > 1 and p[j - 1] == '*':
                    dp[i][j] = dp[i][j - 2] or (i > 0 and p[j - 2] in{'.', s[i - 1]}) and dp[i -1][j]
                else:
                    dp[i][j] = (i > 0 and p[j - 1] in{'.', s[i - 1]} and dp[i -1][j - 1])
                 return dp[m][n]
```
## 正则表达式
```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        return True if re.compile(p).fullmatch(s) else False
```	


