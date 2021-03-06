---
layout: post
title: lintcode “A + B” 题解
date: 2017-02-16
categories: blog
tags: [lintcode题解(python)]
---

# 题目 A + B Problem：

Write a function that add two numbers A and B. You should not use + or any arithmetic operators.

Notice:
There is no need to read data from standard input stream. Both parameters are given in function aplusb, you job is to calculate the sum and return it.

Clarification:

    Are a and b both 32-bit integers?    
    ——Yes.
    Can I use bit operation?    
    ——Sure you can.

Example:
Given a=1 and b=2 return 3

# 题目 A + B 问题：

给出两个整数a和b, 求他们的和, 但不能使用 + 等数学运算符。

注意事项
你不需要从输入流读入数据，只需要根据aplusb的两个参数a和b，计算他们的和并返回就行。

说明：

    a和b都是 32位 整数么？
    ——是的
    我可以使用位运算符么？
    ——当然可以

样例：
如果 a=1 并且 b=2，返回3

## 思路：

首先，题中给定，所有数均为32位大小。

当不考虑进位时，加法可以用位运算“按位异或^”代替：

    1 + 1 = 1 ^ 1 = 0
    1 + 0 = 1 ^ 0 = 1
    0 + 1 = 0 ^ 1 = 1
    0 + 0 = 0 ^ 0 = 0

而进位可以用位运算“按位与&”获取：

    0 + 0 = 0 & 0 = 0 = 不进位
    1 + 0 = 1 & 0 = 0 = 不进位
    0 + 1 = 0 & 1 = 0 = 不进位
    1 + 1 = 1 & 1 = 1 = 进位

如果进行某一位的加法时产生了进位，那么在加高一位时需要将进位1也加进去，因此进位产生的加数应表示为：(x&y)<<1
（注：需要左移一位表示每一位产生的进位是与高一位的数字相加的）
因此，有：
1. x^y //执行加法
2. \(x&y\)<<1 //进位操作
最后，我们有：x+y = x^y+\(x&y\)<<1
    
    证明：x^y是不考虑进位时加法结果。当二进制位同时为1时，才    有进位，因此(x&y)<<1进位产生的值，称为进位补偿。将两者相    加便是完整加法结果。

由于 x^y+\(x&y\)<<1 也可以表示为\(x^y\)^\(\(x&y\)<<1\)与\(x^y\)&\(\(x&y\)<<1\)<<1之和，因此可以迭代地使用1式和2式来将不考虑进位的加法结果与进位产生的加数相加，直到进位为0时，得到的不考虑进位的加法结果（即1式结果）即为最终结果。
    
    下面是两位数的加法：
    11+01 = 100  // 原始的加法
    // 位运算分别计算1）和2）
    1）11 ^ 01 = 10
    2）(11 & 01) << 1 = 10
    //用普通的加法去运算可以得到 10 + 10 = 100
    //但是由于不允许使用“+”，所以要让两个数再按前面的方法计算：
    1) 10 ^ 10 = 00
    2) (10 & 10) << 1 = 100
    再次迭代：
    1) 00 ^ 100 = 100
    2) (00 & 100）<< 1 = 0
    此时进位为0，式1)结果即为最终结果

代码：

#### python：

```
class Solution:
    """
    @param a: The first integer
    @param b: The second integer
    @return:  The sum of a and b
    """
    def aplusb(self, a, b):
        # write your code here, try to do it without arithmetic operators.
        import ctypes
        a = ctypes.c_int32(a).value
        b = ctypes.c_int32(b).value
        while b != 0:
            carry = ctypes.c_int32(a & b).value
            a = ctypes.c_int32(a ^ b).value
            b = ctypes.c_int32(carry << 1).value
        return a
```
#### c：

```
1 int bitAdd(int a,int b)
2 {
3     if(b==0)
4         return a;
5     int sum = a^b;
6     int carry =(a&b)<<1;
7     return bitAdd(sum,carry);
8 }
```

#### c++:

```
class Solution {
    /*
     * param a: The first integer
     * param b: The second integer
     * return: The sum of a and b
     */
    public int aplusb(int a, int b) {
        // Click submit, you will get Accepted!
        int i = 0;
        int res = 0;
        int carry = 0;
        for (; i<32; i++) {
            int aa = (a >> i) & 1;
            int bb = (b >> i) & 1;
            res |= (aa ^ bb ^ carry) << i;
            if (aa == 1 && bb == 1 || ((aa ==1 || bb == 1) && carry == 1)) {
                carry = 1;
            }
            else carry = 0;
        }
        return res;
    }
};
```

或者

```
class Solution {
    /*
     * param a: The first integer
     * param b: The second integer
     * return: The sum of a and b
     */
    public int aplusb(int a, int b) {
        while(b != 0){
            int carry = a & b;
            a = a ^ b;
            b = carry << 1;
        }
        return a;
    }
}

public int aplusb(int a, int b) {
        // Click submit, you will get Accepted!
        if (b == 0) return a;
        int sum = a^b;
        int carry = (a&b)<<1;
        return aplusb(sum, carry);
    }
```


![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/img/blog_logo.png)







