---
layout: post
title: lintcode “丑数 II” 题解
date: 2017-02-15
categories: blog
tags: [lintcode题解(python)]
---

## 题目Ugly Number II：

Ugly number is a number that only have factors 2, 3 and 5.
Design an algorithm to find the nth ugly number. The first 10 ugly numbers are 1, 2, 3, 4, 5, 6, 8, 9, 10, 12...

Notice：
Note that 1 is typically treated as an ugly number.

Example：
If n=9, return 10.

Challenge 
O(n log n) or O(n) time.


## 丑数 II

设计一个算法，找出只含素因子2，3，5 的第 n 大的数。
符合条件的数如：1, 2, 3, 4, 5, 6, 8, 9, 10, 12...

注意事项：
我们可以认为1也是一个丑数

样例：
如果n = 9， 返回 10

挑战：
O(n log n) 或者 O(n) 时间复杂度.

### 题解：

由题目要求计算复杂度为O(nlogn)或者O(n)可知，直接暴力枚举是不行的。比如下面：

```c++:
class Solution {  
public:  
	/* 
	 * @param n an integer 
	 * @return the nth prime number as description. 
	 */  
	int nthUglyNumber(int n) {  
		// write your code here  
		int countN = 0;  
		int m = 0;  
		int lastNumber = 2;  
		while(countN < n)  
		{  
			m++;  
			int number = m;  
			while(number % 2 == 0)  
				number = number / 2;  
			while(number % 3 == 0)  
				number = number / 3;  
			while(number % 5 == 0)  
				number = number / 5;  
			if(number == 1)  
			{  
				countN++;  
			}  
		}  
		return m;  
	}  
};
```
#### 思路：

由丑数的定义可知，任何一个丑数都是2^i\*3^j\*5^k这种形式的，并且任意一个丑数乘以2、3、5后均可以得到新的丑数。

* 第一个丑数ugly[0]为1，此时i = j = k = 0。

* 求解第二个丑数：
ugly[1] = min(ugly[0]\*2, ugly[0]\*3, ugly[0]\*5) = min(1\*2, 1\*3, 1\*5) = 2 = 2^1\*3^0\*5^0
即此时的i = 1。

* 求解第三个丑数：
ugly[2] = min(ugly[1]\*2, ugly[0]\*3, ugly[0]\*5) = min(2\*2, 1\*3, 1\*5) = 3 = 2^0\*3^1\*5^0
即此时的j = 1

	####思考:

1. 为什么这里要用2前面的乘数是ugly[1]而不是ugly[0]?

	因为ugly[0]\*2已经作为第二个丑数赋值给了ugly[1]。

2. 为什么这三个数的最小值就是还没有添加到丑数序列中的所有丑数的最小值？

	因为任意一个丑数乘以2、3、5后均可以得到新的丑数，同时，任意一个丑数都可以由前面的某一丑数乘以2、3或者5得到，除了第一个丑数1。因此还没有添加进丑数序列的丑数有：

		ugly[0]*2*2, ugly[0]*2*2*2, ……（ugly[0]*2作为已经赋给ugly[1]了）
		ugly[1]*2, ugly[1]*2*2,……（注意ugly[1]*2 = ugly[0]*2*2，…… ）
		ugly[2]*2, ugly[2]*2*2,……
		…… …… …… ……
		ugly[n]*2, ugly[n]*2*2,……

	上面丑数序列里最小的数是ugly[1]*2。

	同理，

		ugly[0]*3, ugly[0]*3*3,……
		ugly[1]*3,……
		……
	最小的数是ugly[0]*3

		ugly[0]*5, ugly[0]*5*5,……
		ugly[1]*5,……
		……
	里面最小的数是ugly[0]*5

	因此只需要比较ugly[1]\*2, ugly[0]\*3, ugly[0]\*5三个数，将其中的最小值作为下一个要添加的丑数即可。

3. 为什么这里要用3和5前面的乘数是ugly[0]而不是ugly[1]?
	
	因为ugly[1]>ugly[0],而ugly[0]\*3和ugly[0]\*5均是还没有添加进丑数序列的丑数，它们都分别比ugly[1]*3和ugly[1]\*5小。

4. 那2、3、5前面的乘数应该等于多少？

	初始时设置索引变量index2=index3=index5=0分别代表相应乘数前面的数字是哪个丑数，即ugly[index2],ugly[index3],ugly[index5]。每当ugly[index2]\*2被添加进丑数序列中时，令index2增加一，否则保持不变。3和5同理。下面用该方法求第四个丑数：)

* 求解第四个丑数：

	ugly[2] = min(ugly[1]\*2, ugly[1]\*3, ugly[0]\*5) = min(2\*2, 1\*3, 1\*5) = 3 = 2^0\*3^1\*5^0

	初始化令index2=index3=index5=0；
	求第二个丑数得到ugly[index2]\*2=ugly[0]\*2=2，因此index2从0增加到1；
	求第三个丑数时得ugly[index3]\*3=ugly[0]\*3=3，因此index3从0增加到1；
	求第四个丑数时index2=1，index3=1，index5=0，因此比较ugly[1]\*2, ugly[1]\*3, ugly[0]\*5三个数，得ugly[1]\*2是其中最小值，这时令index2从1增加到2。

### 代码：

	注意：代码需要考虑有重数问题，即当ugly[index2]*2, ugly[index3]*3, ugly[index5]*5三个数字有2个或者全部数字相等时应怎样？比如ugly[index2]*2 = ugly[index3]*3时，正确的做法应该是index2和index3都增加1，因为将ugly[index2]*2添加进丑数序列就意义着ugly[index3]*3也被添加进去了。下面两段代码均有考虑该情况。

##### python：
```
class Solution:
	"""
	@param {int} n an integer.
	@return {int} the nth prime number as description.
	"""
	def nthUglyNumber(self, n):
		# write your code here
		ugly = [1]
		index2 = 0
		index3 = 0
		index5 = 0
		for i in range(n):
			temp_ugly = min(ugly[index2]*2,ugly[index3]*3,ugly[index5]*5)
			ugly.append(temp_ugly)
			index2 +=1 if temp_ugly >= ugly[index2]*2 else 0
			index3 +=1 if temp_ugly >= ugly[index3]*3 else 0
			index5 +=1 if temp_ugly >= ugly[index5]*5 else 0
		return ugly[n-1]
```

####c++：
```
class Solution {  
public:  
	/* 
	 * @param n an integer 
	 * @return the nth prime number as description. 
	 */  
	int nthUglyNumber(int n) {  
		// write your code here  
		int *ugly = new int[n];  
		ugly[0] = 1;  
		int num_2 = 0;  
		int num_3 = 0;  
		int num_5 = 0;  
		for(int i = 1;i<n;i++)  
		{  
			ugly[i] = min(min(ugly[num_2]*2,ugly[num_3]*3),ugly[num_5]*5);  
			if(ugly[i] / ugly[num_2] == 2)  
				num_2 ++;  
			if(ugly[i] / ugly[num_3] == 3)  
				num_3 ++;  
			if(ugly[i] / ugly[num_5] == 5)  
				num_5 ++;  
		}  
		return ugly[n-1];  
	}  
};
```

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/img/blog_logo.png)











