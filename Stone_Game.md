题目描述
============================
亚历克斯和李用几堆石子在做游戏。偶数堆石子排成一行，每堆都有正整数颗石子 piles[i] 。
游戏以谁手中的石子最多来决出胜负。石子的总数是奇数，所以没有平局。
亚历克斯和李轮流进行，亚历克斯先开始。 每回合，玩家从行的开始或结束处取走整堆石头。 这种情况一直持续到没有更多的石子堆为止，此时手中石子最多的玩家获胜。
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
我们想求的答案是先手和后手最终分数之差，按照这个定义也就是 dp[0][n−1].fir−dp[0][n−1].secdp[0][n-1].fir - dp[0][n-1].secdp[0][n−1].fir−dp[0][n−1].sec，即面对整个 pilespilespiles，先手的最优得分和后手的最优得分之差。


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

C语言解法
==========================
```c
bool stoneGame(int* piles, int n){
        int dps[n][n];
        //dps[i][i]存储当前i的石子数
	 //dp其实就是存储了递归过程中的数值
        //dps[i][j]代表从i到j所能获得的最大的绝对分数
        //（比如为1就说明亚历克斯从i到j可以赢李1分）
        //如何计算dps[i][j]呢:max(piles[i]-dp[i+1][j],piles[j]-dp[i][j-1]);
        //这里减去dps数组是因为李也要找到最大的
        //最后dps=[5 2 4 1]
        //        [0 3 1 4]
        //        [0 0 4 1]
        //        [0 0 0 5]
        for(int i=0;i<n;i++)
            dps[i][i]=piles[i];
        //d=1,其实代表，先算两个子的时候
        for(int d=1;d<n;d++)
        {
            //有多少组要比较
            for(int j=0;j<n-d;j++)
            {
                //比较j到d+j
                dps[j][d+j]=fmax(piles[j]-dps[j+1][d+j],piles[d+j]-dps[j][d+j-1]);
            }
        }
        return dps[0][n-1]>0;
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
