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
### Ⅰ动态规划，状态转移方程
`dp[i][j]=max(dp[i][j],dp[i][k]+dp[k][j]+nums[i]*nums[k]*num[j])`
************************************
*****************************
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
                int tmpMax = dp[i][k] + dp[k][j] + tmpNums[i] * tmpNums[k] * tmpNums[j];/*反正就是穷举，先是3个  
		连续的挨个类似滑动窗口的乘积然后再len先空1个再2个。。。。。那样跳，k在中间移动来达到第一次中间某个  
		气球被扎（依此类推）。dp[i][k]+dp[k][j]就是上一步的乘积。*/
                if (tmpMax>dp[i][j]) {
                    dp[i][j] =tmpMax ;
                }
            }
        }
    }
    return dp[0][numsSize + 1];//之所以是0是因为到最后的时候只有i=0一次循环因为tmpMAX等于上一次循环的tmpmax
}
```
### Ⅱ回溯法
```c
/**
    *   @Author Nyr
    *   @Date 2019/11/30 22:24
    *   @Param nums:气球数组，
               y：递归层级，即currentLevel,
               length：数组长度，防止每层都计算一次， 
               beforeCoins：之前所有层获得的金币和，即currentCoin
    *   @Return 
    *   @Exception 
    *   @Description  回溯解法 
    */
    public static void maxCoins2(int[] nums, int y, int length, int beforeCoins) {
        //回归条件
        if (y == length) {
            if (beforeCoins > maxCoin) {
                maxCoin = beforeCoins;
            }
            return;
        }
        for (int i = 0; i < length; i++) {
            //略过已经戳破的气球
            if (nums[i] == -1) {
                continue;
            }
            //标记已经戳破的气球
            int temp = nums[i];
            nums[i] = -1;
            //获取上一个气球的数字
            int before = i - 1;
            int beforeNum = 0;
            while (before > -1 && nums[before] == -1) {
                before--;
            }
            if (before < 0) {
                beforeNum = 1;
            } else {
                beforeNum = nums[before];
            }
            //获取下一个气球的数字
            int next = i + 1;
            int nextNum = 0;
            while (next < length && nums[next] == -1) {
                next++;
            }
            if (next > length - 1) {
                nextNum = 1;
            } else {
                nextNum = nums[next];
            }
            //计算戳破当前气球的coin
            int tempCoin = temp * nextNum * beforeNum;
            //递归进行下一戳
            maxCoins2(nums, y + 1, length, beforeCoins + 
            tempCoin);
            //回溯尝试其它戳法
            nums[i] = temp;
        }
    }
```   
