题目描述
============================
亚历克斯和李用几堆石子在做游戏。偶数堆石子排成一行，每堆都有正整数颗石子 piles[i] 。
游戏以谁手中的石子最多来决出胜负。石子的总数是奇数，所以没有平局。
亚历克斯和李轮流进行，亚历克斯先开始。 每回合，玩家从行的开始或结束处取走整堆石头。这种情况一直持续到没有更多的石子堆为止，此时手中石子最多的玩家获胜。
假设亚历克斯和李都发挥出最佳水平，当亚历克斯赢得比赛时返回 true ，当李赢得比赛时返回 false 。

示例：

输入：[5,3,4,5]
输出：true
解释：
亚历克斯先开始，只能拿前 5 颗或后 5 颗石子 。
假设他取了前 5 颗，这一行就变成了 [3,4,5] 。
如果李拿走前 3 颗，那么剩下的是 [4,5]，亚历克斯拿走后 5 颗赢得 10 分。
如果李拿走后 5 颗，那么剩下的是 [3,4]，亚历克斯拿走后 4 颗赢得 9 分。
这表明，取前 5 颗石子对亚历克斯来说是一个胜利的举动，所以我们返回 true 。

提示：


	2 <= piles.length <= 500
	piles.length 是偶数。
	1 <= piles[i] <= 500
	sum(piles) 是奇数。


思路
===========================================
以下是对 dp 数组含义的解释：
```Python
dp[i][j].fir 表示，对于 piles[i...j] 这部分石头堆，先手能获得的最高分数。
dp[i][j].sec 表示，对于 piles[i...j] 这部分石头堆，后手能获得的最高分数。

举例理解一下，假设 piles = [3, 9, 1, 2]，索引从 0 开始
dp[0][1].fir = 9 意味着：面对石头堆 [3, 9]，先手最终能够获得 9 分。
dp[1][3].sec = 2 意味着：面对石头堆 [9, 1, 2]，后手最终能够获得 2 分。
```
我们想求的答案是先手和后手最终分数之差，按照这个定义也就是 dp[0][n−1].fir−dp[0][n−1].sec，即面对整个 piles，先手的最优得分和后手的最优得分之差。


对于这个问题的每个状态，可以做的选择有两个：选择最左边的那堆石头，或者选择最右边的那堆石头。 我们可以这样穷举所有状态：
```Python
n = piles.length
for 0 <= i < n:
    for j <= i < n:
        for who in {fir, sec}:
            dp[i][j][who] = max(left, right)
```

上面的伪码是动态规划的一个大致的框架，股票系列问题中也有类似的伪码。这道题的难点在于，两人是交替进行选择的，也就是说先手的选择会对后手有影响，这怎么表达出来呢？
根据我们对 dp数组的定义，很容易解决这个难点，写出状态转移方程：
```Python
dp[i][j].fir = max(piles[i] + dp[i+1][j].sec, piles[j] + dp[i][j-1].sec)
dp[i][j].fir = max(    选择最左边的石头堆     ,     选择最右边的石头堆     )
# 解释：我作为先手，面对 piles[i...j] 时，有两种选择：
# 要么我选择最左边的那一堆石头，然后面对 piles[i+1...j]
# 但是此时轮到对方，相当于我变成了后手；
# 要么我选择最右边的那一堆石头，然后面对 piles[i...j-1]
# 但是此时轮到对方，相当于我变成了后手。

if 先手选择左边:
    dp[i][j].sec = dp[i+1][j].fir
if 先手选择右边:
    dp[i][j].sec = dp[i][j-1].fir
# 解释：我作为后手，要等先手先选择，有两种情况：
# 如果先手选择了最左边那堆，给我剩下了 piles[i+1...j]
# 此时轮到我，我变成了先手；
# 如果先手选择了最右边那堆，给我剩下了 piles[i...j-1]
# 此时轮到我，我变成了先手。
```
根据 dp 数组的定义，我们也可以找出 base case，也就是最简单的情况：
```Python
dp[i][j].fir = piles[i]
dp[i][j].sec = 0
其中 0 <= i == j < n
# 解释：i 和 j 相等就是说面前只有一堆石头 piles[i]
# 那么显然先手的得分为 piles[i]
# 后手没有石头拿了，得分为 0
``` 

![1](https://pic.leetcode-cn.com/4130c179b1539ca8f79cf11d060dcece59d6da8d1199330f0c4086f14e4b55c2-file_1564377204078)  
这里需要注意一点，我们发现 basecase是斜着的，而且我们推算 dp[i][j] 时需要用到 dp[i+1][j] 和 dp[i][j-1]  
![2](https://pic.leetcode-cn.com/f59bdc232e1be821fbcabc6640e9775bef5b4cbd0b2c653edc6e0db2c3d7440b-file_1564377204079)   
所以说算法不能简单的一行一行遍历 dp 数组，而要斜着遍历数组：  
![3](https://pic.leetcode-cn.com/3b1adc86ba35fb78111ee6e1b6c38d7adac2f7da4169fbb076672d0351150c50-file_1564377204083)

解法
==========================
```c
bool stoneGame(int* piles, int N){
        int dp[N+2][N+2];
        memset(dp, 0, sizeof(dp));
        for (int size = 1; size <= N; ++size)
            for (int i = 0, j = size - 1; j < N; ++i, ++j) {
/*当剩下的堆的石子数是 piles[i], piles[i+1], ..., piles[j] 时，轮到的玩家最多有 2 种行为。可以通过比较 j-i和 N modulo 2 来找出轮到
的人。如果玩家是亚历克斯，那么她将取走 piles[i] 或 piles[j] 颗石子，增加她的分数。之后，总分为 piles[i] + dp(i+1, j) 或 piles[j] + dp(i, j-
1)；我们想要其中的最大可能得分。如果玩家是李，那么他将取走 piles[i] 或 piles[j] 颗石子，减少亚历克斯这一数量的分数。
之后，总分为 -piles[i] + dp(i+1, j) 或 -piles[j] + dp(i, j-1)；我们想要其中的最小可能得分。*/

                int parity = (j + i + N) % 2;  // j - i - N; but +x = -x (mod 2)
                if (parity == 1)
                    dp[i+1][j+1] = max(piles[i] + dp[i+2][j+1], piles[j] + dp[i+1][j]);
                else
                    dp[i+1][j+1] = min(-piles[i] + dp[i+2][j+1], -piles[j] + dp[i+1][j]);
            }

        return dp[1][N] > 0;
  }
```
```java
class Solution {
    public boolean stoneGame(int[] piles) {
        //dp[i][j]为i开始到j结束alex能赢li多少分 alex先拿的话
        //dp[i][j] = max(piles[i]-dp[i+1][j],piles[j]-dp[i][j-1])
        int[][] dp = new int[piles.length][piles.length];
        for(int i = 0; i < piles.length; i++){
            dp[i][i] = piles[i];
        }
	/**当集合中元素大于2时，先选的人从序列两头拿，可以分成两种情况
        *以ABC为例，可以拿A，剩余BC，后手会选择BC的最佳拿取方式，
        *所以先手得分为A-BC得分，即：results[i][j]=piles[i]-results[i+1][j]；
        *也可以拿C，剩余AB，同理有results[i][j]=piles[j]-results[i][j-1]；
        *选择分值较大的那个即可。上面两个式子都要求我们在求results[i][j]的时候知道
        *它的下面和左边一个格子的值，所以我们从下到上，从左到右计算填表。
        */
        for(int i = 1; i < piles.length; i++){
            for(int j = 0; j < piles.length - i; j++){
                dp[j][j + i] = Math.max(piles[j] - dp[j + 1][j + i], piles[j + i] - dp[j][j + i - 1]);
            }
        }
        return dp[0][piles.length - 1] > 0;
    }
}
```
### 优化
```c
bool stoneGame(int* piles, int n){
   
        int dp[n];
        
        for(int i=0;i<n;i++)
            dp[i]=piles[i];
       
          for(int l=2;l<=n;l++)
        for(int i=0;i<n-l+1;i++)
            dp[i] = fmax(piles[i]-dp[i+1],piles[i+l-1]-dp[i]);
        return dp[0]>0;
}
```
### 当然这道题出的很傻逼，先手肯定赢。
```c
bool stoneGame(int* piles, int pilesSize){
            return true;
}
```
