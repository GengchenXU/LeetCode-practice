题目描述
==============================
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

  左括号必须用相同类型的右括号闭合。
	左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

示例 1:
输入: "()"
输出: true


示例 2:
输入: "()[]{}"
输出: true

示例 3:
输入: "(]"
输出: false

示例 4:
输入: "([)]"
输出: false

示例 5:
输入: "{[]}"
输出: true

C语言解法
=========================
### ①栈法
```c
bool isValid(char * s){
    int i;
    int len;
    int top = 0;
    char *stack = NULL;
    len = strlen(s);
    if (len == 0) {
        return 1;
    }
    if (len == 1) {
        return 0;
    }
    stack = (char *)malloc(len);
    if (stack == NULL) {
        return 0;
    }
    for (i = 0; i < len; i++) {
        switch(s[i]) {
            case '(':
            case '[':
            case '{':
                stack[top++] = s[i];
                break;
            case ')':  
                if (top == 0 ) {
                    return 0;
                }
                top--;
                if (stack[top] != '(') {
                    return 0;
                }
                break;
            case ']':
                if (top == 0 ) {
                    return 0;
                }
                top--;
                if (stack[top] != '[') {
                    return 0;
                }
                break;
            case '}':
                if (top == 0 ) {
                    return 0;
                }
                top--;
                if (stack[top] != '{') {
                    return 0;
                }
                break;
        }
    }

    if (top != 0) {
        return 0;
    }
    return 1;
}
```
### ②计数器法
```c
bool isValid(char * ch){
    int count1=0;   //大括号{}
    int count2=0;   //中括号[] 
    int count3=0;   //小括号() 
    while(*ch!='\0'){  
        if(*ch=='{'){            //{}
            count1++;  
        }else if(*ch=='['){      //[] 
            count2++;  
        }else if(*ch==')'){      //()
            count3++;  
        } 
        if(*ch=='}'&&count1==0){     //{}{}{}}
            //printf("0");  
            return false;  
        }else if(ch==']'&&count2==0){   //[[][]]
            //printf("0");  
            return false;  
        }else if(*ch==')'&&count3==0){   //(()()())
            //printf("0");  
            return false;  
        }       

        if(*ch=='}'&&count1!=0){  
            count1--;  
        }else if(*ch==']'&&count2!=0){  
            count2--;  
        }else if(*ch==')'&&count3!=0){  
            count3--;  
        }   
        ch++;      
        /*printf("{}:%d\n",count1);*/
    }
    if(count1==0&&count2==0&&count3==0){  
        return true;        
        //printf("1");
    }else{  
        return false;
        //printf("0\n");  
        /*
        printf("{}:%d\n",count1); 
        printf("[]:%d\n",count2); 
        printf("():%d\n",count3); 
        */
    }
    //getchar(); 
    //return 0;  
}
```
