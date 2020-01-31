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
