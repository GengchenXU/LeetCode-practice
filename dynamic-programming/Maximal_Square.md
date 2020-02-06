题目描述
========================
在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

示例:  
输入: 

1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0

输出: 4

思路
==============
动态规划方法的题解中，都会涉及到下列形式的代码：

if (grid(i, j) == 1) {
    dp(i, j) = min(dp(i-1, j), dp(i, j-1), dp(i-1, j-1)) + 1;
}
翻译成中文

若某格子值为 1 ，则以此为右下角的正方形的、最大边长为：上面的正方形、左面的正方形或左上的正方形中，最小的那个，再加上此格。

先来阐述简单共识
----------------------
若形成正方形（非单 1），以当前为右下角的视角看，则需要：当前格、上、左、左上都是 1
可以换个角度：当前格、上、左、左上都不能受 0 的限制，才能成为正方形  
![示例](https://pic.leetcode-cn.com/8c4bf78cf6396c40291e40c25d34ef56bd524313c2aa863f3a20c1f004f32ab0-image.png)  

上面详解了 三者取最小 的含义：

图1：受限于左上的0  
图2：受限于上边的0  
图3：受限于左边的0  
数字表示：以此为正方形右下角的最大边长  
黄色表示：格子 ? 作为右下角的正方形区域  
就像木桶的短板理论 那样——附近的最小边长，才与 ? 的最长边长有关。  
此时已可得到递推公式 if (grid[i][j] == 1) f[i][j] = min(f[i-1][j-1], f[i-1][j], f[i][j-1]) + 1;

C语言解法
```c
int maximalSquare(char** matrix, int matrixSize, int* matrixColSize){
    if (matrixSize == NULL ||* matrixColSize==NULL) {
        return 0;
        }
        int dp[matrixSize+1][*matrixColSize+1];
        memset(dp, 0, sizeof(dp));
        int max=0;
        for(int i = 1; i <= matrixSize; ++i) {
            for(int j = 1; j <= *matrixColSize; ++j) {
                if(matrix[i-1][j-1] == '1') {
                    dp[i][j] = 1 + fmin(dp[i-1][j-1], fmin(dp[i-1][j], dp[i][j-1]));
                    max = fmax(max, dp[i][j]); 
                }
            }
        }
        return max*max;
}
```
