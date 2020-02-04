题目描述
==================================
n个人参加某项特殊考试。
为了公平，要求任何两个认识的人不能分在同一个考场。
求是少需要分几个考场才能满足条件。
输入
第一行，一个整数n(1<n<100)，表示参加考试的人数。
第二行，一个整数m，表示接下来有m行数据
以下m行每行的格式为：两个整数a，b，用空格分开 (1<=a,b<=n) 表示第a个人与第b个人认识。
输出
一行一个整数，表示最少分几个考场。
样例输入
5  
8  
1 2  
1 3  
1 4  
2 3  
2 4  
2 5  
3 4  
4 5  
5  
10  
1 2  
1 3  
1 4  
1 5  
2 3  
2 4  
2 5  
3 4  
3 5  
4 5  
样例输出  
4  
5

C语言解决
=======================
### ①
```c
#include<iostream>
#include<string>
#include<algorithm>
#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;
const int maxn = 110;
const int inf = 0x3f3f3f3f;//这是个极大的数
int gra[maxn][maxn];   //是否存在关系，存在关系就是1，不存在关系就是0
int cun[maxn][maxn];   //第一维度表示的是考场，第二维度里面放的是这个考场里面的学生
//cun[1][1] = 2，cun[1][2] = 0;表示考场1里面第一个存在的人是2，然后后面一位是0，也就是不存在人了，那么这时的cnt[1] = 1；表示的是考场里面人的数量
int cnt[maxn];         //这个考场的人数
int res = inf;
int n, m;
void solve(int id, int num) {    //id表示学生，num表示当前考场的编号
    if (num >= res) {              //当现在安排的数量已经大于了最小的教室数量的话，返回
        return;
    }
    if (id > n) {                 //安排的学生已经大于所有的学生了，就是安排完所有的学生了已经
        res = min(res, num);
        return;
    }
    for (int i = 1; i <= num; i++) {   //首先看看之前的房间里面能不能放进去
        int sz = cnt[i];
        int jishu = 0;      //得到的是和这个人不认识的人数
        for (int j = 1; j <= sz; j++) {
            if (gra[id][cun[i][j]] == 0) {
                jishu++;
            }
        }
        if (jishu == sz) {   //如果这里面的学生都和现在遍历的都不认识
            cun[i][++cnt[i]] = id; //将这个学生存到这个考场中
            solve(id + 1, num);
            cnt[i]--;//前面line33先加了，这里减去
        }
    }
    //重新开一个房间
    cun[num + 1][++cnt[num + 1]] = id;  //没有的话就把它放到下一个教室里
    solve(id + 1, num + 1);
    --cnt[num + 1];//？不知为啥这是要回溯？
}
int main() {
    scanf("%d%d", &n, &m);
    memset(gra, 0, sizeof(gra));
    memset(cnt, 0, sizeof(cnt));
    while (m--) {
        int a, b;
        scanf("%d%d", &a, &b);
        gra[a][b] = gra[b][a] = 1;//认识的位置为1，之所以要反过来一次是为了分配第二个人时也要检验
    }
    solve(1, 0);
    printf("%d\n", res);
    return 0;
}
```
### ②
```c
int main() {
    scanf("%d%d", &n, &m);
    memset(gra, 0, sizeof(gra));
    memset(cnt, 0, sizeof(cnt));
    while (m--) {
        int a, b;
        scanf("%d%d", &a, &b);
        gra[a][b] = gra[b][a] = 1;//认识的位置为1，之所以要反过来一次是为了分配第二个人时也要检验
    }
    solve(1, 0);
    printf("%d\n", res);
    return 0;
}
#include <stdio.h>
#include <string.h>
int n, min_kes, p[102][102], map[102][102];
void DFS(int x, int kes)
{
    if (kes >= min_kes)
        return;
    if (x > n)
    {
        if (kes < min_kes)
            min_kes = kes;
        return;
    }
    for (int i = 1; i <= kes; i++)
    {
        int k = 0;
        while (p[i][k] && !map[x][p[i][k]])
            k++;
        if (!p[i][k])
        {
            p[i][k] = x;
            DFS(x + 1, kes);
            p[i][k] = 0;
        }
    }
    p[kes + 1][0] = x;
    DFS(x + 1, kes + 1);
    p[kes + 1][0] = 0;
}
int main()
{
    int t, a, b;
    scanf("%d%d", &n, &t);
    min_kes = n;
    while (t--)
    {
        scanf("%d%d", &a, &b);
        map[a][b] = map[b][a] = 1;
    }
    DFS(1, 0);
    printf("%d\n", min_kes);
    return 0;
}
```
