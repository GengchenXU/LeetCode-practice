问题描述
==========================
给定一个非负整数数组，你最初位于数组的第一个位置。
数组中的每个元素代表你在该位置可以跳跃的最大长度。
判断你是否能够到达最后一个位置。

示例 1:

输入: [2,3,1,1,4]
输出: true
解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。


示例 2:

输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。

C语言解法
=======================
### ①
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) 
{int n = nums.size();
        if(n==0) return false;
        if(n==1) return true;
        int zeroCount = 0;
        for(int i=n-2; i>=0; i--){
            if(nums[i]==0) zeroCount++;
            else{
                if(nums[i]>zeroCount) zeroCount=0;
                else zeroCount++;
            }
        }
        if(zeroCount>0) return false;
        else return true;

}
};
```
### ②
思路：如果存在跳不过去的点的话，那么数组中必然至少存在一个点的值为0，并且0之前的所有点都无法跳过这个点。  
所以这里遍历这个数组，找到值为0的点。找到值为0的点后，向前依次查找能跳过这个点的点，如果查找不到，那就  
证明这里无法跳过去。如果找到了，那么就继续向下查找下一个0。(最后一个点的值可以忽略)
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        for(int i = 0; i < nums.size() - 1; ++i){
            if(nums[i] == 0){
                int max = 1, j = i - 1;
                for(; j >= 0 && nums[j] <= max; --j){
                    ++max;
                }
                if(j == -1) return false;
            }
        }
        return true;
    }
};
```
### ③
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) 
{
	int k = 0;
	for (int i = 0; i < nums.size(); i++)
	{
		if (i > k) return false;
		k = max(k, i + nums[i]);
	}
	return true;
}
};
```
