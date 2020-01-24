 题目描述
 =============================
给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。
请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

示例 1:

nums1 = [1, 3]
nums2 = [2]
则中位数是 2.0


示例 2:

nums1 = [1, 2]
nums2 = [3, 4]
则中位数是 (2 + 3)/2 = 2.5

### 思路：先将两个数组合并求新数组的中位数

 c++解法
================================================
```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        double result = 0;
        int len1 = nums1.size();
        int len2 = nums2.size();
        int len = len1+len2;
        int p1 = 0,p2 = 0,i = 0;
        int mid,num1,num2;
        if(len%2){
            mid = len/2+1;
            while(i < mid && p1 < len1 && p2 < len2){
                num1 = nums1[p1];
                num2 = nums2[p2];
                if(num1 < num2){ result = num1; p1++; i++; }
                else{ result = num2; p2++; i++; }
            }
            while(i < mid && p1 < len1){ result = nums1[p1]; p1++;i++; }
            while(i < mid && p2 < len2){ result = nums2[p2]; p2++;i++; }
        }
        else{
            int result2 = 0;
            mid = len/2;
            while(i < mid && p1 < len1 && p2 < len2){
                num1 = nums1[p1];
                num2 = nums2[p2];
                if(num1 < num2){ result = num1; p1++; i++; }
                else{ result = num2; p2++; i++; }
            }
            while(i < mid && p1 < len1){ result = nums1[p1]; p1++;i++; }
            while(i < mid && p2 < len2){ result = nums2[p2]; p2++;i++; }
            if(p1 < len1 && p2 < len2){
                if(nums1[p1] < nums2[p2]){ result2 = nums1[p1];}
                else{ result2 = nums2[p2];}
            }else if(p2 == len2) result2 = nums1[p1];
            else if(p1 == len1) result2 = nums2[p2];
            result = (result + result2)/2.0;
        }
        return result;
    }
};
```
C语言解法
=========================================
### ①
```c
#include<stdio.h>
int main()
{
    int nums1[100] = { 0 }, nums2[100] = { 0 }, s[200] = { 0 };
    int m, n;
    float x = 0;
    scanf_s("%d %d", &m, &n);
    for (int i = 0; i < m; i++) {
        scanf_s("%d", &nums1[i]);
        s[i] = nums1[i];
    }
    for (int i = 0; i < n; i++) {
        scanf_s("%d", &nums2[i]);
        s[m + i] = nums2[i];
    }
    for (int i = 0; i < (m + n); i++) {
        if (s[i] > s[i + 1]) {
            int temp = s[i];
            s[i] = s[i + 1];
            s[i + 1] = temp;
        }
    }
    if ((m + n) % 2 == 0)
        x = (float)(s[(m + n) / 2] + s[(m + n - 2) / 2]) / 2;
    else
        x = s[(m + n - 1) / 2];
    printf("%.1f\n", x);
    return 0;
}
```
### ②
```c
double findMedianSortedArrays(int* nums1, int nums1Size, int* nums2, int nums2Size){
    int num[nums1Size + nums2Size];
int i = 0, j = 0, k = 0;
int sum;
double a;
while(i < nums1Size && j < nums2Size){
    if(nums1[i] < nums2[j])
    {
        num[k] = nums1[i];
        k++;
        i++;
    }
    else
    {
        num[k] = nums2[j];
        k++;
        j++;
    }
}
    while(i < nums1Size)
    {
        num[k] = nums1[i];
        k++;
        i++;
    }
    while(j < nums2Size)
    {
        num[k] = nums2[j];
        k++;
        j++;
    }
sum = (nums1Size + nums2Size) ;
if((sum % 2) == 0)
{
    a = (num[sum / 2] + num[(sum / 2) - 1]) / 2.0;
    return(a);
}
else
{
    return (num[sum / 2]);
}
}
```

