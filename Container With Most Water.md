题目描述
----------------------------------------
给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

![示例](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

示例:
输入: [1,8,6,2,5,4,8,3,7]
输出: 49

### Python解法
```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        left = 0
        right = len(height) - 1
        area = 0
        
        while left < right:
            cur = min(height[left], height[right]) * (right - left)
            area = max(area, cur)
            # 较短的垂直线往中间移动
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
            
        return area
```
### C语言暴力解法
```c
int maxArea(int* height, int heightSize){
    int tspace=0;
    int space=0;
    for(int i=0;i<heightSize;i++){
        for(int j=i;j<heightSize;j++){
            if(height[i]<height[j])
            tspace=height[i]*(j-i);
            else tspace=height[j]*(j-i);
            
            if(tspace>space)
            space=tspace;
        }
    }
    return space;  
}
```
### C++解法
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        if(height.size() <= 1) return -1;
        int i = 0, j = height.size() - 1, res = 0;
        while(i < j){
            int h = min(height[i], height[j]);
            res = max(res, h * (j - i));
            if(height[i] < height[j]) ++i;
            else --j;
        }
        return res;
    }
};
```
### Python3解法
```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        i, j, res = 0, len(height) - 1, 0
        while i < j:
            if height[i] < height[j]:
                res = max(res, height[i] * (j - i))
                i += 1
            else:
                res = max(res, height[j] * (j - i))
                j -= 1
        return res
```        
