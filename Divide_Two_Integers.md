题目描述
========================
给定两个整数，被除数 dividend 和除数divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。
返回被除数 dividend 除以除数 divisor 得到的商。

示例 1:

输入: dividend = 10, divisor = 3
输出: 3
示例 2:

输入: dividend = 7, divisor = -3
输出: -2
说明:

被除数和除数均为 32 位有符号整数。
除数不为 0。
假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−231,  231 − 1]。本题中，如果除法结果溢出，则返回 231 − 1。

解法
============================
### C++
```cpp
class Solution {
public:
	int subDivide(int dividend, int divisor, bool minus)
	{
		int i = 0, times = 1;
		int originDivisor = divisor;
		while (dividend <= divisor && dividend <= 0)
		{
			dividend -= divisor;
 			i += times;           
            if (dividend >= divisor)
            {
                break;
            }
			divisor += divisor;
			times += times;
		}

		if (dividend > originDivisor)
		{
			if (minus)
				return -i;
			else
				return i;
		}
		else
		{
			if (minus)
				return -i + subDivide(dividend, originDivisor, minus);
			else
				return i + subDivide(dividend, originDivisor, minus);
		}
	}

	int divide(int dividend, int divisor) {
		int i = 0, times = 1, left = 0;
		bool minus = false;
		
		if (dividend == INT_MIN)
		{
			if (divisor == -1)
				return INT_MAX;
			else if (divisor == 1)
				return INT_MIN;
			else if (divisor == INT_MIN)
				return 1;
		}
		else if (divisor == INT_MIN)
			return 0;
        else if (divisor == 1)
            return dividend;
        else if (divisor == -1)
            return -dividend;

		if (dividend < 0 && divisor > 0)
		{
			minus = true;
			divisor = -divisor;
		}
		else if (dividend > 0 && divisor < 0)
		{
			minus = true;
			dividend = -dividend;
		}
        else if (dividend > 0 && divisor > 0)
        {
 			divisor = -divisor;
			dividend = -dividend;                        
        }

		return subDivide(dividend, divisor, minus);
	}
};
```
### C
```c
#define LIMIT 0x80000000

static int divide(int dividend, int divisor) {
	if (dividend == 0) {
		return 0;
	}
	if (dividend == INT_MIN && divisor == -1) {
		return INT_MAX;
	}
	bool negative;
	negative = (dividend ^ divisor) < 0;//用异或来计算是否符号相异
	unsigned int t = dividend == INT_MIN ? LIMIT : abs(dividend);
	unsigned int d = divisor == INT_MIN ? LIMIT : abs(divisor);
	unsigned int result = 0;
	for (int i = 31; i >= 0; i--) {
		if ((t >> i) >= d) {//找出足够大的数2^n*divisor
			result += ((unsigned int)1) << i;//将结果加上2^n
			t -= d << i;//将被除数减去2^n*divisor
		}
	}
	if (result == LIMIT) {//特殊数不能将unsigned int转为int
		return INT_MIN;
	}
	else {
		return negative ? -(int)result : (int)result;//符号相异取反
	}
}

void solution29() {
	ALog("%d", divide(INT_MIN, 1));
	ALog("%d", divide(INT_MIN, -1));
	ALog("%d", divide(INT_MAX, 1));
	ALog("%d", divide(INT_MAX, -1));
}
```
