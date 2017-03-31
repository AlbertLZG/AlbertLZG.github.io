---
layout: post
title: Suffix Array (O(nlogn) Algorithm)
date: 2017-03-21
categories: blog
tags: [算法]
---
注：内容主要来自清华大学陈挺老师的《高级算法分析与设计》课中所做的笔记和作业。

# Suffix Array ( O(nlogn) Algorithm)


### Problem：


How to find all occurrences of pattern P in text T.

—— KMP algorithm?  Too slow!

    KMP Algorithm: O(|P|+|T|) time, slow for large T.

    Can we speed it up to O(|P|log(|T|)) by doing some preprocess to the T?

For large and relatively stable T and multiple searches, we can preprocess T into some data structure to speed up searches.

Data Structure: suffix arrays, suffix trees, Hash tables.

### Definition：

A suffix array is a sorted array of all suffixes of a string. 

> Usually suffixes are sorted in lexicographical order .

**Example (from wikipedia)：**

Consider the text S=banana$ to be indexed:

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2013.31.24.png)

> Note: the special sentinel letter $ is lexicographically smaller than any other character. 

The text has the following suffixes:

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2013.31.30.png)

The suffix array A contains the starting positions of these sorted suffixes. These suffixes can be sorted in ascending order:

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2013.32.55.png)

### Solution

Given text T of length N, query P of length p. 
If we have the suffix array of text T, we can find all occurrences of pattern P in text T in O(plogN) time by binary search.

Amazing!!!

**Problem：**

how long do we need to construct the suffix array of text T?

1. O(NlogN) time.
2. Linear time.

Here we introduce the O(NlogN) algorithm.


### O(NlogN) algorithm

**Procedure:**

1. Starting from H=1 
    
2. In stage H, all suffixes are sorted into buckets called H-Buckets, according to the first H characters. 
    
3. Using H-buckets to for 2H–sorting, which sorts all suffixes into 2H-buckets according to the first 2H characters. 
    
4. Repeat Steps 2-3 until H=N 

**From H-sorting to 2H-sorting**

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2013.53.50.png)

**Construction of 2H-bucket**

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2013.53.57.png)

**Time Complex**

    Sorting by first char: O(N)

    O(logN) stages of O(N) operations: O(NlogN)

Total time: O(NlogN)

### Example:

Reference:
    
> Larsson N J, Sadakane K. Faster suffix sorting[M]. Univ., 1999".
    
![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2014.01.03.png)

This example is slightly different from the original O(NlogN) algorithm in the Construction of 2H-bucket. 
It replaces 'Move Tpos[k]-h to next available place in its H-bucket' with 'Process each unsorted group in I with ternary-split Quicksort, using V[i+h] as the key for suffix i'. 

They are the same to some degree! Please think it.

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2014.01.15.png)


### Code \( python \)

```
#! usr/bin/python
# coding=utf-8
#--------------------------------------------------------------------#
#Title：Suffix Array——O(nlog(n))
#Author: Albert_LZG/李志刚
#Note：Reprint please indicate the source and the author
#--------------------------------------------------------------------#
import time
import numpy as np

time_start = time.clock()
# Read original data
seq_num1 = 1
file_object1 = open('sequence' + str(seq_num1) + '.txt')
# Delete nonalpha characters and save them in upper case using array.
data1 = np.asarray(list(file_object1.read().upper().replace("\n", "").replace("\r", "")))
file_object1.close()

_len_data = len(data1)
pos = np.arange(_len_data)
# initial variables
index_pos = np.argsort(data1)
bucket_stage = np.arange(_len_data)
bucket_pos_plus_h = np.array([-1 for i in range(_len_data)])
pos_record = np.array([-1 for i in range(_len_data)])

sequence_now = data1[np.argsort(data1)]
count = 1
bucket_stage[_len_data - 1] = _len_data - 1
for i in range(_len_data - 2, -1, -1):
    if sequence_now[i] == sequence_now[i + 1]:
        count += 1
        bucket_stage[i] = bucket_stage[i + 1]
    else:
        bucket_stage[i] = bucket_stage[i + 1] - count
        if count > 1:
            pos_record[i + 1] = count
        count = 1
if count > 1:
    pos_record[i] = count

print '    original data:', data1
print '    suffix_number:', np.arange(_len_data), '\n'
print '        index_pos:', index_pos
print '     bucket_stage:', bucket_stage
print '       pos_record:', pos_record, '\n'

flag = 1
h = 1
while flag:
    flag = 0
    # update bucket_pos_plus_h
    for i in range(_len_data):
        if pos_record[i] > 1:
            flag = 1
            #compute every bucket_pos_plus_h of each bucket
            for j in range(pos_record[i]):
                pos_plus_h_temp = index_pos[i+j]+h
                if pos_plus_h_temp >= _len_data:
                    bucket_pos_plus_h[i+j] = -1
                else:
                    index_temp = np.where(index_pos == pos_plus_h_temp)[0]
                    bucket_pos_plus_h[i+j] = bucket_stage[index_temp]
            #sort each bucked according to the result of each bucket_pos_plus_h, update index_pos
            bucket_temp = index_pos[i:(i+pos_record[i])].copy()
            sort_bucket_temp = np.argsort(bucket_pos_plus_h[i:(i+pos_record[i])])
            index_pos[i:(i+pos_record[i])] = index_pos[i+sort_bucket_temp]
            bucket_pos_plus_h[i:(i + pos_record[i])] = bucket_pos_plus_h[i+sort_bucket_temp]
            #update bucked_sign
            count = 1
            for j in range(len(sort_bucket_temp)-2,-1,-1):
                if bucket_pos_plus_h[i+j] == -1:
                    # index_pos[i+j] = bucket_temp[sort_bucket_temp[j]]
                    if count > 1:
                        pos_record[i+j+1] = count
                        pos_record[i + j] = -1
                        bucket_stage[i+j] = bucket_stage[i+j+1]-count
                        count = 1
                    else:
                        bucket_stage[i+j] = bucket_stage[i+j+1]-1
                        pos_record[i+j] = -1
                elif bucket_pos_plus_h[i+j] == bucket_pos_plus_h[i+j+1]:
                    bucket_stage[i+j] = bucket_stage[i+j+1]
                    pos_record[i+j] = -1
                    count += 1
                else:
                    bucket_stage[i+j] = bucket_stage[i+j+1] - count
                    if count > 1:
                        pos_record[i+j+1] = count
                    pos_record[i+j] = -1
                    count = 1
            if count > 1:
                pos_record[i+j] = count
    if flag:
        print '----------------------------- h = %s -----------------------------' % str(h)
        print '        index_pos:', index_pos
        print 'bucket_pos_plus_h:', bucket_pos_plus_h
        print '     bucket_stage:', bucket_stage
        print '       pos_record:', pos_record

    h *= 2

print 'final_index_pos', index_pos

time_end = time.clock()

f1 = open('result' + str(seq_num1) + '.txt', 'w')

f1.write('original sequence :'+'\n')
f1.write("".join(data1.tolist()))
f1.write('\n\n'+'The running time is: ' + str(time_end - time_start) + '\n\n')
f1.write("".join(str(index_pos.tolist()))+'\n\n')

for i in range(len(index_pos)):
    f1.write("".join(data1[index_pos[i]:])+'\n\n')

f1.close()

```

### Result

    original sequence :
    TOBEORNOTTOBE$
    
    The running time is: 0.014197
    
    [13, 11, 2, 12, 3, 6, 10, 1, 4, 7, 5, 9, 0, 8]
    
    $
    
    BE$
    
    BEORNOTTOBE$
    
    E$
    
    EORNOTTOBE$
    
    NOTTOBE$
    
    OBE$
    
    OBEORNOTTOBE$
    
    ORNOTTOBE$
    
    OTTOBE$
    
    RNOTTOBE$
    
    TOBE$
    
    TOBEORNOTTOBE$
    
    TTOBE$

the process of computation is as follows (you can see it on the screen when the code is running):

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-21-Suffix%20Array%20(%20O(nlogn)%20)/2017-03-21%2014.28.40.png)

**the meaning of variants:**

* index_pos: the suffix array.
> index_pos[i] = k means the kth suffix is the ith element of the suffix array.

* bucket_stage: buckets.
> The same number means the same bucket.

* pos_record: the record of unsettled elements’ positions.
> The value means there are how many element in this bucket. -1 means the element is settled.

* bucket_pos_plus_h:
> ‘bucket_pos_plus_h’ is used to class those elements in the same bucket. The kth value equals the bucket’s number of the (k+h)th suffix for the kth suffix in the suffix array.

It’s easy to find that the result is correct from this example. The code works pretty well!

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/img/blog_logo.png)
