题目描述
=========================
给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。
在 S 上反复执行重复项删除操作，直到无法继续删除。
在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

 示例：

输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa"   
可以执行重复项删除操作，所以最后的字符串为 "ca"。

提示：


	1 <= S.length <= 20000
	S 仅由小写英文字母组成。

思路
==================
用栈的思想，但不用实际创建栈，将原S串当成栈使用，两个下标一个write一个read指针即可。top字符为write下标的前一位，read出的字符和top一样则将  
write退1，否则压栈。

C语言解法
=======================
```
char * removeDuplicates(char * S){
    int write=0;
    int read=0;
    char top, ch;    
    while(ch = S[read++]){
        if(0 == write){ // empty string
            top = 0;
        }
        else{
            top = S[write-1];
        }
        if(ch == top){ //delete top character
           write--;//read++write--这里错过两个
        }
        else{ // write current character to top
            S[write++] = ch;                                  
        }                              
    }
    S[write] = 0;
    return S;
}
```
