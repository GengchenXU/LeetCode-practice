题目描述
========================
有 n 个气球，编号为0 到 n-1，每个气球上都标有一个数字，这些数字存在数组 nums 中。
现在要求你戳破所有的气球。每当你戳破一个气球 i 时，你可以获得 nums[left] * nums[i] * nums[right] 个硬币。 这里的 left 和 right 代表和 i 相邻的两个气球的序号。注意当你戳破了气球 i 后，气球 left 和气球 right 就变成了相邻的气球。
求所能获得硬币的最大数量。

说明:

  你可以假设 nums[-1] = nums[n] = 1，但注意它们不是真实存在的所以并不能被戳破。
	0 ≤ n ≤ 500, 0 ≤ nums[i] ≤ 100


示例:

输入: [3,1,5,8]
输出: 167 
解释: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
     coins =  3* 1 * 5      +  3* 5 * 8    +  1* 3 * 8      + 1* 8 * 1   = 167
 
C语言解法
========================
```c
int maxCoins(int* nums, int numsSize)
{
    if (nums == NULL || numsSize == 0) {
        return 0;
    }
    int tmpNums[numsSize + 2];
    memcpy(tmpNums + 1, nums, sizeof(int) * numsSize);
    tmpNums[0] = 1;
    tmpNums[numsSize + 1] = 1;
    int dp[numsSize + 2][numsSize + 2];
    memset(dp, 0, sizeof(dp));
    for (int len = 2; len < numsSize + 2; len++) {
        for (int i = 0; i < numsSize; i++) {
            int j = i + len;
            if (j > numsSize + 1) {
                continue;
            }
            int tmpMax = 0;
            for (int k = i + 1; k < j; k++) {
                int l = dp[i][k] + dp[k][j] + tmpNums[i] * tmpNums[k] * tmpNums[j];/*反正就是穷举，先是3个连续的挨个类似滑动窗口的乘积然后再len先空1个再2个。。。。。那样跳，k在中间移动来达到中间某个气球被扎。dp[i][k]+dp[k][j]就是上一步的乘积。*/
                if (l > tmpMax) {
                    tmpMax = l;
                }
            }
            dp[i][j] = tmpMax;
        }
    }
    return dp[0][numsSize + 1];
}
```
