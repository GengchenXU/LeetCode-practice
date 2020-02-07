题目描述
==============================
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![示例](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)


上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。

示例:

输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6

思路
=============================
### 好湿好湿

若问雨水深何许，先当首寻最高峰。  
高峰两侧群山列，须将右侧倒个个。  
右山若较左山矮，右山山高累加和。  
左山不及右山高，距乘左山减加和。  
如此这般算将好，左右群山水深得。  
天帝赞叹算法妙，玄之又玄乐无穷。

### 一、核心思路
我第一次看到这个问题，无计可施，完全没有思路，相信很多朋友跟我一样。所以对于这种问题，我们不要想整体，而应该去想局部；就像之前的文章处理字符串问题，不要考虑如何处理整个字符串，而是去思考应该如何处理每一个字符。

这么一想，可以发现这道题的思路其实很简单。具体来说，仅仅对于位置 i，能装下多少水呢？
!(https://pic.leetcode-cn.com/7c5c8f0dc7c32c07fbe1dbcfff9e4d5a69450b1817621f5975e28270ef11f739-file_1577197165936)

能装 2 格水。为什么恰好是两格水呢？因为 height[i] 的高度为 0，而这里最多能盛 2 格水，2-0=2。
为什么位置 i 最多能盛 2 格水呢？因为，位置 i 能达到的水柱高度和其左边的最高柱子、右边的最高柱子有
关，我们分别称这两个柱子高度为 l_max 和 r_max；位置 i 最大的水柱高度就是 min(l_max, r_max)。
更进一步，对于位置 i，能够装的水为：
```
water[i] = min(
               # 左边最高的柱子
               max(height[0..i]),  
               # 右边最高的柱子
               max(height[i..end]) 
            ) - height[i]
```    
!(https://pic.leetcode-cn.com/50707a5def56a85c91855ea75e60291a6b73c4df8873dc3e63c554a6bdd0b8c1-file_1577197165937)
!(https://pic.leetcode-cn.com/64624dd22776dcc337a4733144089bf53a6e2a62426a680f606bb44633f471be-file_1577197165938)

这就是本问题的核心思路，我们可以简单写一个暴力算法：
```cpp
int trap(vector<int>& height) {
    int n = height.size();
    int ans = 0;
    for (int i = 1; i < n - 1; i++) {
        int l_max = 0, r_max = 0;
        // 找右边最高的柱子
        for (int j = i; j < n; j++)
            r_max = max(r_max, height[j]);
        // 找左边最高的柱子
        for (int j = i; j >= 0; j--)
            l_max = max(l_max, height[j]);
        // 如果自己就是最高的话，
        // l_max == r_max == height[i]
        ans += min(l_max, r_max) - height[i];
    }
    return ans;
}
```
有之前的思路，这个解法应该是很直接粗暴的，时间复杂度 O(N^2)，空间复杂度 O(1)。但是很明显这种计算 r_max 和 l_max 的方式非常笨拙，一般的优化方法就是备忘录。

### 二、备忘录优化
之前的暴力解法，不是在每个位置 i 都要计算 r_max 和 l_max 吗？我们直接把结果都缓存下来，别傻不拉
几的每次都遍历，这时间复杂度不就降下来了嘛。

我们开两个数组 r_max 和 l_max 充当备忘录，l_max[i] 表示位置 i 左边最高的柱子高度，r_max[i] 表示
位置 i 右边最高的柱子高度。预先把这两个数组计算好，避免重复计算：
```cpp
int trap(vector<int>& height) {
    if (height.empty()) return 0;
    int n = height.size();
    int ans = 0;
    // 数组充当备忘录
    vector<int> l_max(n), r_max(n);
    // 初始化 base case
    l_max[0] = height[0];
    r_max[n - 1] = height[n - 1];
    // 从左向右计算 l_max
    for (int i = 1; i < n; i++)
        l_max[i] = max(height[i], l_max[i - 1]);
    // 从右向左计算 r_max
    for (int i = n - 2; i >= 0; i--) 
        r_max[i] = max(height[i], r_max[i + 1]);
    // 计算答案
    for (int i = 1; i < n - 1; i++) 
        ans += min(l_max[i], r_max[i]) - height[i];
    return ans;
}
```
这个优化其实和暴力解法差不多，就是避免了重复计算，把时间复杂度降低为 O(N)，已经是最优了，但是空间复杂度是 O(N)。下面来看一个精妙一些的解法，能够把空间复杂度降低到 O(1)。

### 三、双指针解法
这种解法的思路是完全相同的，但在实现手法上非常巧妙，我们这次也不要用备忘录提前计算了，而是用双指
针边走边算，节省下空间复杂度。

首先，看一部分代码：
```cpp
int trap(vector<int>& height) {
    int n = height.size();
    int left = 0, right = n - 1;
    
    int l_max = height[0];
    int r_max = height[n - 1];
    
    while (left <= right) {
        l_max = max(l_max, height[left]);
        r_max = max(r_max, height[right]);
        left++; right--;
    }
}
```
对于这部分代码，请问 l_max 和 r_max 分别表示什么意义呢？

很容易理解，l_max 是 height[0..left] 中最高柱子的高度，r_max 是 height[right..end] 的最高柱子
的高度。

明白了这一点，直接看解法：
```cpp
int trap(vector<int>& height) {
    if (height.empty()) return 0;
    int n = height.size();
    int left = 0, right = n - 1;
    int ans = 0;
    
    int l_max = height[0];
    int r_max = height[n - 1];
    
    while (left <= right) {
        l_max = max(l_max, height[left]);
        r_max = max(r_max, height[right]);
        
        // ans += min(l_max, r_max) - height[i]
        if (l_max < r_max) {
            ans += l_max - height[left];
            left++; 
        } else {
            ans += r_max - height[right];
            right--;
        }
    }
    return ans;
}
```
你看，其中的核心思想和之前一模一样，换汤不换药。但是细心的读者可能会发现次解法还是有点细节差异：
之前的备忘录解法，l_max[i] 和 r_max[i] 代表的是 height[0..i] 和 height[i..end] 的最高柱子高
度。
```
ans += min(l_max[i], r_max[i]) - height[i];
```
!(https://pic.leetcode-cn.com/2f8e64f19c265e51ed0a2311d43ec2dc32cb7a8c0b0882a22a9a4d0fdb478ea6-file_1577197165939)
但是双指针解法中，l_max 和 r_max 代表的是 height[0..left] 和 height[right..end] 的最高柱子高
度。比如这段代码：
```
if (l_max < r_max) {
    ans += l_max - height[left];
    left++; 
} 
```
!(https://pic.leetcode-cn.com/6ce2aa8a1324b0821a10eb90d9c28505c79d2da755c010e0eed2faf741f6dbcc-file_1577197165941)
此时的 l_max 是 left 指针左边的最高柱子，但是 r_max 并不一定是 left 指针右边最高的柱子，这真的
可以得到正确答案吗？

其实这个问题要这么思考，我们只在乎 min(l_max, r_max)。对于上图的情况，我们已经知道 l_max < r_max 了，至于这个 r_max 是不是右边最大的，不重要，重要的是 height[i] 能够装的水只和 l_max 有关。
!(https://pic.leetcode-cn.com/621e73dc73aa3fade606be25feb16c4886e3f35797906fdb3ca683702a187a82-file_1577197165942)

解法
=============================
### C++解法
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
       if (height.size()== 0)
		return 0;
    int ans = 0;
    int size = height.size();
    vector<int> left_max(size), right_max(size);
    left_max[0] = height[0];
    for (int i = 1; i < size; i++) {
        left_max[i] = max(height[i], left_max[i - 1]);
    }
    right_max[size - 1] = height[size - 1];
    for (int i = size - 2; i >= 0; i--) {
        right_max[i] = max(height[i], right_max[i + 1]);
    }
    for (int i = 1; i < size - 1; i++) {
        ans += min(left_max[i], right_max[i]) - height[i];
    }
    return ans;
    }
};
````
Java思路
--------------------------
水是向两边流的，最边上的柱子一定存不住水，但会限制一个高度，往中间走低的地方就能存住水了

遍历找到最高点
初始化容量数组，cap[i] = max - h[i] 假设都有水
从两边往中间扫，记录一个当前最大值 lim, cap[i] = lim - h[i] 比最大值高的水都流走了
对 cap 数组求和即可


### 别人的JAVA做法
```Java
class Solution {
    public int trap(int[] height) {
           int max = 0;
        int index = 0;
        for(int i = 0; i < height.length; i++){
            if(max < height[i]){
                max = height[i];
                index = i;
            }
        }
        int[] cap = new int[height.length];
        for(int i = 0; i < height.length; i++){
            cap[i] = max - height[i];
        }
        int lim = 0;

        for(int i = 0; i < index; i++){
            if(height[i] > lim){
                lim = height[i];
                cap[i] = 0;
            }else{
                cap[i] = lim - height[i];
            }
        }
        lim = 0;
        for(int i = height.length - 1; i > index; i--){
            if(height[i] > lim){
                lim = height[i];
                cap[i] = 0;
            }else{
                cap[i] = lim - height[i];
            }
        }
        int ans = 0;
        for(int i = 0; i < cap.length; i++){
            ans += cap[i];
        }
        return ans;
        
    }
}
```
### 根据Java的思路写的C 但是结果却不对
```c
int trap(int* height, int heightSize){
    if(heightSize==0)
    return 0;
    int max=0;
    int index = 0;
    int cap[heightSize];
        for(int i=0;i<heightSize;i++){
            if(height[i]>max)
            max=height[i];
            index=i;
        }
       for(int i = 0; i < heightSize; i++){
            cap[i] = max - height[i];
        }
        int lim = 0;
        for(int i = 0; i < index; i++){
            if(height[i] > lim){
                lim = height[i];
                cap[i] = 0;
            }else{
                cap[i] = lim - height[i];
            }
        }
        lim = 0;
        for(int i = heightSize - 1; i > index; i--){
            if(height[i] > lim){
                lim = height[i];
                cap[i] = 0;
            }else{
                cap[i] = lim - height[i];
            }
        }
        int ans = 0;
        for(int i = 0; i <heightSize; i++){
            ans += cap[i];
        }
    return ans;
    }
```

