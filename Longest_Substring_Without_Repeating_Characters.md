问题描述
===============================
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

子序列与子列区别
----------------------------
`给定 "pwwkew" ，
子串是pww,wwk等很多个子串 是连在一起的
子序列是 pwk,pke等很多个子序列 ，但是子序列中的字符在字符串中不一定是连在一起的。`

C语言解法
==================================
### ①
```c
int lengthOfLongestSubstring(char * s){
    int arr[128]={0},start=1,maxlen=0,i,end=1;//之所以start和end赋初值为1是为了防止空字符串，空字符串的最长子列为1
    for(i=0;s[i];i++){
        if(arr[s[i]]<start)
            arr[s[i]]=end++;//遇到不同则拓展窗口
        else{
            if(end-start>maxlen)
                maxlen=end-start;
            start=arr[s[i]]+1;
            arr[s[i]]=end++;//遇到相同则把之前相同的移到右边，左边收缩
        }
    }
    if(end-start>maxlen) maxlen=end-start;
    return maxlen;
}
```
### ②
```c
int lengthOfLongestSubstring(char * s){
    int i,j,n=0,m=0,k=0;
     for(i=0;s[i]!='\0';i++)
    {
        for(j=k;j<i;j++)
        {
            if(s[i]==s[j])
            {
              if(n>m)
              m=n;
              n=i-j;
              k=j+1;
              break;
            }

        }
        if(i==j)
        n++; 
        if(m<n)
        m=n;  
    }
    return m;
    }
```
