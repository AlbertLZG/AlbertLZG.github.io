---
layout: page
title: lintcode "尾部的零"问题 题解：
date: 2017-2-16
categories: blog
description: lintcode
tags: [算法,数据结构,lintcode题解(python)]
---
----------
#题目 "Trailing Zeros" Problem:

Write an algorithm which computes the number of trailing zeros in n factorial.

Example
11! = 39916800, so the out should be 2

Challenge 
O(log N) time


#题目 "尾部的零"问题:

设计一个算法，计算出n阶乘中尾部零的个数

样例
11! = 39916800，因此应该返回 2

挑战 
O(logN)的时间复杂度
 

##思路：

考虑将N! = 1 × 2 ×３× 4 ×……×N中的每一个因数进行质因数分解，结果为：1 × 2 × 3 × (2 × 2) × 5 × (2 × 3) × 7 × (2 × 2 ×2) ×…… 

10进制数结尾的每一个0都表示有一个因数10存在（同理，对于一个M进制的数，让结尾多一个0就等价于乘以M）。10可以分解为2 × 5（只有质数2和5相乘能产生0，别的任何两个质数相乘都不能产生0），而每一个2和5相乘只产生一个0。

所以，分解后的整个因数式中有多少对(2, 5)，结果中就有多少个0，而分解的结果中，2的个数显然是多于5的每一个偶数都包含至少一个2），因此，有多少个5，就有多少个(2, 5)对。

n的阶乘的质因数分解式中5的个数可以用如下方法求： 

    1000的阶乘末尾0的个数:
    （下面的“／”代表“取整除法”）
    1000/5 + 1000/25 + 1000/125 + 1000/625 
    = 200 + 40 + 8 + 1 
    = 249(个)

例子：求26的阶乘的尾部有多少个0. 
26！ = 1 × 2 ×３× 4 × **5** × 6 × 7 × 8 × 9 × **10** × 11 × 12 × 13× 14 × **15** × 16 × 17 × 18 × 19 × **20** × 21 × 22 ×2３× 24 × ***25*** × 26 
其中**5**、**10**、**15**、**20**各包含一个5，***25***包含两个5，共有 26/5 + 26/25 = 6（个）5相乘 
>25其实可以分解成2个5相乘，而26/5只计算了一个5，因此还要再加26/25.

##代码：
####python：
```
class Solution:
    # @param n a integer
    # @return ans a integer
    def trailingZeros(self, n):
        zeros = 0
        while n > 0:
            zeros += n / 5
            n /= 5
        return zeros
```

####java：
```
class Solution {
    /*
     * param n: As desciption
     * return: An integer, denote the number of trailing zeros in n!
     */
    public long trailingZeros(long n) {
        long count = 0;
        for(int i = 1; Math.pow(5,i) <= n; i++) {
            count += n / (long)Math.pow(5,i);//注意强制类型转换
        }
        return count;
    }
};
```
![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/img/blog_logo.png)







