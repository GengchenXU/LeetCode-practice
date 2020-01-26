题目描述
==============================
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![示例](https://github.com/GengchenXU/leetcode-practice/blob/master/rainwatertrap.png?raw=true)


上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。

示例:

输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6

### 好湿好湿

若问雨水深何许，先当首寻最高峰。  
高峰两侧群山列，须将右侧倒个个。  
右山若较左山矮，右山山高累加和。  
左山不及右山高，距乘左山减加和。  
如此这般算将好，左右群山水深得。  
天帝赞叹算法妙，玄之又玄乐无穷。		



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

