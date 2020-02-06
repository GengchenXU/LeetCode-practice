题目描述
=================================
Ⅰ
-----------------------------------
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。
给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

示例 1:

输入: [1,2,3,1]
输出: 4
解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
示例 2:

输入: [2,7,9,3,1]
输出: 12
解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
Ⅱ
------------------------------------------
你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。
给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

示例 1:

输入: [2,3,2]
输出: 3
解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
示例 2:

输入: [1,2,3,1]
输出: 4
解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。

思路
============================
题目很容易理解，而且动态规划的特征很明显。我们前文「动态规划详解」做过总结，解决动态规划问题就是找「状态」和「选择」，仅此而已。

假想你就是这个专业强盗，从左到右走过这一排房子，在每间房子前都有两种选择：抢或者不抢。

如果你抢了这间房子，那么你肯定不能抢相邻的下一间房子了，只能从下下间房子开始做选择。

如果你不抢这件房子，那么你可以走到下一间房子前，继续做选择。

当你走过了最后一间房子后，你就没得抢了，能抢到的钱显然是 0（base case）。

以上的逻辑很简单吧，其实已经明确了「状态」和「选择」：你面前房子的索引就是状态，抢和不抢就是选择。
![](https://pic.leetcode-cn.com/92c830194e9ce50fafd5fcce29eab57b915e642ec29f42e07b27683d61ddf0b5-file_1578115994454)

在两个选择中，每次都选更大的结果，最后得到的就是最多能抢到的 money：
```java
// 主函数
public int rob(int[] nums) {
    return dp(nums, 0);
}
// 返回 nums[start..] 能抢到的最大值
private int dp(int[] nums, int start) {
    if (start >= nums.length) {
        return 0;
    }
    
    int res = Math.max(
            // 不抢，去下家
            dp(nums, start + 1), 
            // 抢，去下下家
            nums[start] + dp(nums, start + 2)
        );
    return res;
}
```
明确了状态转移，就可以发现对于同一 start 位置，是存在重叠子问题的，比如下图：

![](https://pic.leetcode-cn.com/89e5ceb51ffc7639506789b3aff9157d434fb4618a0ce59e7bed2028d5985b78-file_1578115994471)
盗贼有多种选择可以走到这个位置，如果每次到这都进入递归，岂不是浪费时间？所以说存在重叠子问题，可以用备忘录进行优化：
```java
private int[] memo;
// 主函数
public int rob(int[] nums) {
    // 初始化备忘录
    memo = new int[nums.length];
    Arrays.fill(memo, -1);
    // 强盗从第 0 间房子开始抢劫
    return dp(nums, 0);
}

// 返回 dp[start..] 能抢到的最大值
private int dp(int[] nums, int start) {
    if (start >= nums.length) {
        return 0;
    }
    // 避免重复计算
    if (memo[start] != -1) return memo[start];
    
    int res = Math.max(dp(nums, start + 1), 
                    nums[start] + dp(nums, start + 2));
    // 记入备忘录
    memo[start] = res;
    return res;
}
```
这就是自顶向下的动态规划解法，我们也可以略作修改，写出自底向上的解法：
```java
 int rob(int[] nums) {
    int n = nums.length;
    // dp[i] = x 表示：
    // 从第 i 间房子开始抢劫，最多能抢到的钱为 x
    // base case: dp[n] = 0
    int[] dp = new int[n + 2];
    for (int i = n - 1; i >= 0; i--) {
        dp[i] = Math.max(dp[i + 1], nums[i] + dp[i + 2]);
    }
    return dp[0];
}
```
我们又发现状态转移只和 dp[i] 最近的两个状态有关，所以可以进一步优化，将空间复杂度降低到 O(1)。
```java
int rob(int[] nums) {
    int n = nums.length;
    // 记录 dp[i+1] 和 dp[i+2]
    int dp_i_1 = 0, dp_i_2 = 0;
    // 记录 dp[i]
    int dp_i = 0; 
    for (int i = n - 1; i >= 0; i--) {
        dp_i = Math.max(dp_i_1, nums[i] + dp_i_2);
        dp_i_2 = dp_i_1;
        dp_i_1 = dp_i;
    }
    return dp_i;
}
```
以上的流程，在我们「动态规划详解」中详细解释过，相信大家都能手到擒来了。我认为很有意思的是这个问题的 follow up，需要基于我们现在的思路做一些巧妙的应变。

House Robber II
这道题目和第一道描述基本一样，强盗依然不能抢劫相邻的房子，输入依然是一个数组，但是告诉你这些房子不是一排，而是围成了一个圈。

也就是说，现在第一间房子和最后一间房子也相当于是相邻的，不能同时抢。比如说输入数组 nums=[2,3,2]，算法返回的结果应该是 3 而不是 4，因为开头和结尾不能同时被抢。

这个约束条件看起来应该不难解决，我们前文「单调栈解决 Next Greater Number」说过一种解决环形数组的方案，那么在这个问题上怎么处理呢？

首先，首尾房间不能同时被抢，那么只可能有三种不同情况：要么都不被抢；要么第一间房子被抢最后一间不抢；要么最后一间房子被抢第一间不抢。
![](https://pic.leetcode-cn.com/40955db0ce08289191aca14b5143d7b370079249ad416260b5b525a918839dc3-file_1578115994473)  
那就简单了啊，这三种情况，那种的结果最大，就是最终答案呗！不过，其实我们不需要比较三种情况，只要比较情况二和情况三就行了，因为这两种情况对于房子的选择余地比情况一大呀，房子里的钱数都是非负数，所以选择余地大，最优决策结果肯定不会小。

所以只需对之前的解法稍作修改即可：
```java
public int rob(int[] nums) {
    int n = nums.length;
    if (n == 1) return nums[0];
    return Math.max(robRange(nums, 0, n - 2), 
                    robRange(nums, 1, n - 1));
}
```
// 仅计算闭区间 [start,end] 的最优结果
```java
int robRange(int[] nums, int start, int end) {
    int n = nums.length;
    int dp_i_1 = 0, dp_i_2 = 0;
    int dp_i = 0;
    for (int i = end; i >= start; i--) {
        dp_i = Math.max(dp_i_1, nums[i] + dp_i_2);
        dp_i_2 = dp_i_1;
        dp_i_1 = dp_i;
    }
    return dp_i;
}
```


解法
===========================================
Ⅰ
------------------------------------------
```java
class Solution {
     public int rob(int[] nums) {
//0 不偷 1偷
        int[][] dp = new int[nums.length][2];
        if (nums.length > 0){//第一天偷的话的金钱 不偷的话为0，默认就是0，不用处理。
            dp[0][1] = Math.max(0,nums[0]);
        }else {
            return 0;
        }
        for (int i = 1; i <nums.length ; i++) {
            dp[i][1] = Math.max(dp[i-1][0],dp[i-1][0]+nums[i]/*上次不偷+这次偷*/);
            dp[i][0] = Math.max(dp[i-1][1],dp[i-1][0]);
        }
        return Math.max(dp[nums.length-1][1],dp[nums.length-1][0]);
    }
}
```
### 优化
```java
class Solution {
     public int rob(int[] prices) {
//0 不偷 1偷
         int n = prices.length;
    int dp_i_0 = 0, dp_i_1 =0,dp_i_2=0;
    for (int i = 0; i < n; i++) {
        dp_i_2 = Math.max(dp_i_1, dp_i_0 + prices[i]);
        dp_i_0=dp_i_1;
        dp_i_1 = dp_i_2;
    }
    return dp_i_2;
    }
}
```
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int sum0 = 0, sum1 = 0;
        
        for(int i = 0; i < nums.size(); ++i)
        {
            if(i % 2 == 0)
            {
                sum0 += nums[i];
                sum0 = max(sum0, sum1);
            }
            else if(i%2==1)
            {
                sum1 += nums[i];
                sum1 = max(sum0, sum1);
            }
        }
        return max(sum0, sum1);
    }
};
```
Ⅱ
-------------------------------------------
```java
class Solution {
    public int rob(int[] nums) {
    int n = nums.length;
    if (n == 1) return nums[0];
    return Math.max(robRange(nums, 0, n - 2), 
                    robRange(nums, 1, n - 1));
}

// 仅计算闭区间 [start,end] 的最优结果
int robRange(int[] nums, int start, int end) {
    int n = nums.length;
    int dp_i_1 = 0, dp_i_2 = 0;
    int dp_i = 0;
    for (int i = end; i >= start; i--) {
        dp_i = Math.max(dp_i_1, nums[i] + dp_i_2);
        dp_i_2 = dp_i_1;
        dp_i_1 = dp_i;
    }
    return dp_i;
}
}
```
