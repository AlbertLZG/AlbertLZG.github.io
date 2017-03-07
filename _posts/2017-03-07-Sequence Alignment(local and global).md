---
layout: post
title: Sequence Alignment(local and global)
date: 2017-03-07
categories: blog
tags: [算法]
---
注：内容主要来自清华大学陈挺老师的《高级算法分析与设计》课中所做的笔记和作业。

# Sequence Alignment

### Problem：

Given two sequences U and V, how to measure the similarity between U and V?

	——We need to make sequence alignment between U and V.

### Definition：

Given two sequences U and V (from a large database), the alignment of U and V is to insert space (“-“) into the sequences to make them the same length n. Note that the alignment of “-“ and “-“ is forbidden.

**Problem：**

How many alignments there are between two sequences?

>Hint:
>Remember this picture in my post 《算法与数据结构基础知识》?（You can find it in the post of February 28, 2017）
>How many paths can be taken from the upper left to the lower right？（You can move to the right/down/lower right element each step）

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-02-28-algorithm-base/2017-02-28 21.35.41.png)


### Alignment Score

**Problem：**

There are many alignments between U and V, How do we measure how good an alignment is?

	——To measure how good an alignment is, you need to compute the score of it. Then, how to compute the score when given an alignment?

#### **Similarity Scoring Function**

Use w\(match\), w\(mismatch\) and w\(indel\) to represent the scoring function. Then, the score of an alignment is defined by sum the score of each column. For example:

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-07-sequence-alignment/2017-03-07%2021.24.07.png)

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-07-sequence-alignment/2017-03-07%2021.24.31.png)

Clearly, how good an alignment is depends on the scoring function you define!

Sometimes the distance score is used : example d\(match\)=0, d\(mismatch\)=1, d\(indel\)=2. In this case, a lower score means a better alignment.

# Optimal global alignment

Problem Definition: 
Given two sequences U, V, and a scoring function w\( \), asks to find the optimal global alignment that has the maximum score.
>Note: w\( \) is to be defined by your model. “global” means every base is considered.

### Method：Dynamic Programming

* Define Scoring Function

As an example, the scoring functions are defined as:
d\(match\) = 0
d\(mismatch\) = 1
d\(indel\) = 1

* The Recursive Function

Define S\(u1…ui,v1…vj\) to be the score of the optimal local alignment ended at ui and vj.

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-07-sequence-alignment/2017-03-07%2022.34.04.png)

* Boundaries:

S\(u1…ui,-\) = i \* w(indel)
S\(-,v1…vj\) = j \* w(indel)
Then, The number of S scores is \(m+1\)\*\(n+1\), polynomial to m and n.

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-07-sequence-alignment/2017-03-07%2021.38.47.png)

* DP Algorithm:

pseudocode：
```
#U = (u1……um), V = (v1……vn)
Input: U, V, w() 
Output: R: optimal local alignment score, T: optimal alignment 

#Initiate first row and first column
Initiate s[0][j] and s[i][0] 
#Compute S scores (|U|、|V| is the length of sequence U、V)
For i=1 to |U| 
    For i=j to |V| 
        Compute S[i][j] 
R <— S[|U|][|V|] 
T <= trace back to S(−, −) according from S[|U|][|V|] 
```

* Time & Space Complex

O(M × N) & O(M × N)

### Code \( python \)
```
#! usr/bin/python
#coding=utf-8

import time
time_start = time.clock()

#Read original data
seq_num1 = 1
seq_num2 = 2
file_object1 = open('sequence'+str(seq_num1)+'.txt')
file_object2 = open('sequence'+str(seq_num2)+'.txt')
#Delete nonalpha characters and save them in upper case using list.
data1 = list(file_object1.read( ).upper().replace("\n","").replace("\r",""))
data2 = list(file_object2.read( ).upper().replace("\n","").replace("\r",""))
file_object1.close( )
file_object2.close( )

#Define variants, including distance scoring function and scoring table.
d_match = 0
d_dismatch = 1
d_indel = 1
_len1 = len(data1)
_len2 = len(data2)
score_table = [([0] * (_len2+1)) for i in range(_len1+1)]
flag_table = [(['end'] * (_len2+1)) for i in range(_len1+1)]
for i in range(_len1+1):
	score_table[i][0] = i*d_indel
for i in range(1, _len2+1):
	score_table[0][i] = i*d_indel

#calculate scores
for i in range(_len1):
	for j in range(_len2):
		score_temp = {
		    'up': score_table[i][j+1]+d_indel,
		    'left': score_table[i+1][j]+d_indel,
		    'up&left':score_table[i][j]+(d_match if data1[i] == data2[j] else d_dismatch)
		}
		# using zip to exchange keys and values，then use max to get the highest score
		result = min(zip(score_temp.values(), score_temp.keys()))
		score_table[i+1][j+1] = result[0]
		flag_table[i+1][j+1] = result[1]

#trace back to find the optimal global sequence		
i = _len1
j = _len2
seq1 = []
seq2 = []
while i != 0 or j != 0:
	if flag_table[i][j] == 'up':
		if i > 0:
			seq1.insert(0, data1[i-1])
			seq2.insert(0, '_')
			i -= 1
		else:
			for k in range(j,0,-1):
				seq1.insert(0, '_')
				seq2.insert(0, data2[k-1])
			break
			
	elif flag_table[i][j] == 'left':
		if j > 0:
			seq1.insert(0, '_')
			seq2.insert(0, data2[j-1])
			j -= 1
		else:
			for k in range(i,0,-1):
				seq1.insert(0, data1[k-1])
				seq2.insert(0,'_')
			break
	else:
		if i > 0 and j > 0:
			seq1.insert(0, data1[i-1])
			seq2.insert(0, data2[j-1])
			i -= 1
			j -= 1
		elif i == 0:
			for k in range(j,0,-1):
				seq1.insert(0,'_')
				seq2.insert(0, data2[k-1])
			break
		else:
			for k in range(i,0,-1):
				seq1.insert(0, data1[k-1])
				seq2.insert(0,'_')
			break

time_end = time.clock()

#record the results.
f1 = open('result'+str(seq_num1)+str(seq_num2)+'.txt','w')
f1.write('The best distance score between sequence'+str(seq_num1)+' and sequence'+str(seq_num2)+': '+str(score_table[_len1][_len2])+'\n')
f1.write('The length of sequence'+str(seq_num1)+' and sequence'+str(seq_num2)+': '+str(len(seq1))+'  '+str(len(seq2))+'\n')
f1.write('The running time is: '+str(time_end-time_start)+'\n\n')
f1.write('The sequence'+str(seq_num1)+' and the sequence'+str(seq_num2)+':\n\n')

i = 1
len_row = 70
while i*len_row < len(seq1):
	f1.write("".join(seq1[(i-1)*len_row:i*len_row])+'\n')
	f1.write("".join(seq2[(i-1)*len_row:i*len_row])+'\n\n')
	i += 1

f1.write("".join(seq1[i*len_row:])+'\n')
f1.write("".join(seq2[i*len_row:])+'\n\n')
f1.close()

```
### Result

The best distance score between sequence1 and sequence2: 277
The length of sequence1 and sequence2: 1526  1526
The running time is: 4.625369
The sequence1 and the sequence2:

	AGAGTTTGATCCTGGCTCAGGATGAACGCTAGCTACAGGCTTAACACATGCAAGTCGAGGGGCATCAGGG
	AGAGTTTGATCCTGGCTCAGGATGAACGCTAGCTACAGGCTTAACACATGCAAGTCGAGGGGCAGCATGA

	GAGTAGCTT_GCTA_TTCCCGCTGGCGACCGGCGCACGGGTGAGTAACACGTATCCAACCTGCCC_TCAT 
	GAGGA_CTTCGGTCCTTTC_GATGGCGACCGGCGAATGGGTGAGTAACACGTATCCAACCTGCCCCTGA_

	CCCGGGGATAGCCCTCTGAAAGGAGGATTAATACCCGATGCG_GTCATTTCGGGACATC_CT_GTT_ATG 
	CCGGGGGATAGCCCTCCGAAAGGAGAATTAACACCCCATGGGTGTCCGTGCGG__CATCGCGCGGCCATG

	ACTAAAGATTCATCGGATGAGG_ATGGGGATGCGTCTGATTAGCTTGTTGGCGGGGTAACGGCCCACCAA 
	A__AAGG_TT_A_CGG_TCAGGGATGGGGATGCGTCCGATTAGCTTGCTGGCGGGGTAACGGCCCACCAG

	GGCATCGATCAGTAGGGGTTCTGAGAGGAAGGTCCCCCACA_TAGGAACTGAGACACGGTCCTA_ACTCC 
	GGCGTCGATCGGTAGGGGTTCTGAGAGGAAGGTCCCCCACACT_GGAACTGAGACACGGTCC_AGACTCC

	TACGGGAGGCAGCAGTGAGGAATATTGGTCAATGGGCGTGA_GCCTGAACCAGCCAAGTAGCGTGAGGGA 
	TACGGGAGGCAGCAGTGAGGAATATTGGTCAATGGGCG_GAAGCCTGAACCAGCCAAGTAGCGTGAAGGA

	CGACTGCCCTATGGGTTGTAAACCTCTTTTATGCGGGAATAACGGT_CGCTACGTGTAGCGGCGTGCATG 
	TGACGGCCCTACGGGTTGTAAACTTCTTTTATGCGGGAACAAAG_TGCGCCACGCGTGGCGTTTTGCGCG

	TACCGCATGAATAAGCATCGGCTAATTCCGTGCCAGCAGCCGCGGTAATACGGAAGATGCGAGCGTTATC 
	TACCGCAGGAAAAAGCACCGGCTAATTCCGTGCCAGCAGCCGCGGTAATACGGAAGGTGCGAGCGTTATC

	CGGATTTATTGGGTTTAAAGGGAGCGTAGGCGGA_CGCTTAAGCCTGCGGTAAAATGTCAGCGCC_CAAC 
	CGGATTCATTGGGTTTAAAGGGAGCGTTGGCGGAGCGCC_AAGTCAGCTGTGAAATCCC_GCGGCTCAAC

	GC_TGGCCC_GCCGTGGGAACTGGGTGTC_TTGAATGTGCACA__AGGAAGGTGGAATTCGTGGTGTAGC 
	_CGTGGAACTGCAGTTGAAACTGGC_GCCCTTG__TGTGCACATGAGGATGGTGGAATTCGTGGTGTAGC

	GGTGAAATGCTTAGATATCACGAAGAACTCCGATTGCGAAGGCAGCCT_TCTGGGGCATG__ATTGACGC 
	GGTGAAATGCTTAGATATCACGAAGAACTCCGATTGCGAAGGCAGC_TGTCTGGGG__TGCCACTGACGC

	TGAGGCTCGAAAGTGCGGGTATCGAACAGGATTAGATACCCTGGTAGTCCGCACAGTAAACGATGAG_TG 
	TGAGGCTCGAAAGTGCGGGTATCAAACAGGATTAGATACCCTGGTAGTCCGCACAGTAAACGATG_GATA

	CCCGCTC_TCGGCGACAGACAGTCGGGGGCCAAGCGAAAGC_ATTAAGCACTCC_ACCTGGGGAGTACGC 
	CTCG_TGGTCGGCGACACACTGCCGGTCACCAAGCGAAAGCGAT_AAGTA_TCCCACCTGGGGAGTACGC

	CGGCAACGGTGAAACTCAAAGGAATTGACGGGGGCCCGCACAAGCGGAGGAACATGTGGTTTAATTCGAT 
	CGGCAACGGTGAAACTCAAAGGAATTGACGGGGGCCCGCACAAGCGGAGGAACATGTGGTTTAATTCGAT

	GATACGCGAGGAACCTTACCCGGGCTTGAACTGGACATGAC_GTTGGGCAGAGA_CGCCCGATCT_CCCG 
	GTTACGCGAGGAACCTTACCCGGGCTTGAACTGCA_AGGACCGTCCG__AGAGATCG___G_TCTTCCCT

	_CAAGGGCAT_GCTCGGAGGTGCTGCATGGTTGTCGTCAGCTCGTGCCGTGAGGTGTCGGCTTAAGTGCC 
	TCG_GGGCCTTGG_CGGAGGTGCTGCATGGTTGTCGTCAGCTCGTGCCGTGAGGTGTCGGCTTAAGTGCC

	ATAACGAGCGCAACCCCCCTGTGTA_G_TTGCCATCAGGTAATGCTGGGCACTCTGCACATACTGCCATC
	ATAACGAGCGCAACCCC__TGTCTTCGGTTGCCATCGGGTAATGCCGGGCACTCCGGAGATACTGCCATC

	GCAAGATGTGAGGAAGGTGGGGATGACGTCAAATCAGCACGGCCCTTACGTCCGGGGCTACACACGTGTT 
	GCAAGATGTGAGGAAGGTGGGGATGACGTCAAATCAGCACGGCCCTTACGTCCGGGGCTACACACGTGTT

	ACAATGGGGGGCACAGAGAG_CTGCTGCATGGCGACATGCGGCGAATCTTGAAATCCCCTCTCAGTTCGG 
	ACAATGGGAGGTACAGA_AGGCTGCTACCCGGCGACGGGATGCCAATCCCCAAATCCTCTCTCAGTTCGG

	ACT_GGAGTCTGCAACCCGACTCCACGAAGCTGGATTCGCTAGTAATCGCGCATCAGCCATGGCGCGGTG 
	A_TCGGAGTCTGCAACCCGACCCCGTGAAGCTGGATTCGCTAGTAATCGCGCATCAGCCATGGCGCGGTG

	AATACGTTCCCGGGCCTTGTACACACCGCCCGTCAAGCCATGAAAGCCGGGGGCACCTGAAGGCTGCGGC 
	AATACGTTCCCGGGCCTTG__CAC________TCA__CC__G____CC_____C____G_____T_C___

There are 1526 characters in total in each sequence. 
The two sequences show 1249 matched characters, 112 mismatched and 165 indels.

The score of optimal alignment is 277, which equals to 0*1249+1*112+1*165.

Under this algorithm, the number of indels is approximate to the number of mismatched characters(i.e. 112 vs 165), different from the results of solution 1. It is also reasonable because the scoring functions have been changed, the score of indel is equal to mismatched character. Thus the results do verify my reasoning mentioned above, i.e. scoring functions have a huge effect on the sequence alignment results. To this sense, we can never be too careful to choose the scoring functions.


# Optimal local alignment

Given two sequences U, V, and a scoring function w\( \), the optimal local alignment is to find a substring p of A and a substring q of B, such that S\(p,q\) has the maximum alignment score.

>Note: w\( \) is to be defined by your model. “global” means every base is considered.

### Method：Dynamic Programming**

* Define Scoring Function

As an example, the scoring functions are defined as:
w\(match\) = 3
W\(mismatch\) = -1
w\(indel\) = -3

* The Recursive Function

Define S\(u1…ui,v1…vj\) to be the score of the optimal local alignment ended at ui and vj.

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-07-sequence-alignment/2017-03-07%2021.55.19.png)

* Boundaries:

S\(u1…ui,-\) = max\(i \* w(indel),0\)
S\(-,v1…vj\) = max\(j \* w(indel),0\)
Then, The number of S scores is \(m+1\)\*\(n+1\), polynomial to m and n.

![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/blog_img/2017-03-07-sequence-alignment/2017-03-07%2021.38.47.png)

* DP Algorithm:

pseudocode：

```
#U = (u1……um), V = (v1……vn)
Input: U, V, w() 
Output: R: optimal local alignment score, T: optimal alignment 

#Initiate first row and first column
Initiate s[0][j] and s[i][0] 
#Compute S scores (|U|、|V| is the length of sequence U、V)
For i=1 to |U| 
    For i=j to |V| 
        Compute S[i][j] 
R <— Find the optimal score at S(u1…um,v1…vn)  
T <= trace back to S(−, −) according to the optimal score at
S(u1…um,v1…vn)
```

* Time & Space Complex
O(M × N) & O(M × N)

### Code \( python \)
```

#! usr/bin/python
#coding=utf-8

import time
time_start = time.clock()

#Read original data
seq_num1 = 1
seq_num2 = 2
file_object1 = open('sequence'+str(seq_num1)+'.txt')
file_object2 = open('sequence'+str(seq_num2)+'.txt')
#Delete nonalpha characters and save them in upper case using list.
data1 = list(file_object1.read( ).upper().replace("\n","").replace("\r",""))
data2 = list(file_object2.read( ).upper().replace("\n","").replace("\r",""))
file_object1.close( )
file_object2.close( )

#Define variants, including scoring function and scoring table.
w_match = 3
w_dismatch = -1
w_indel = -3
_len1 = len(data1)
_len2 = len(data2)
score_table = [([0] * (_len2+1)) for i in range(_len1+1)]
flag_table = [(['end'] * (_len2+1)) for i in range(_len1+1)]
for i in range(_len1+1):
	score_table[i][0] = max(i*w_indel,0)
for i in range(1, _len2+1):
	score_table[0][i] = max(i*w_indel,0)

#calculate scores
max_score = 0
max_score_row = 0
max_score_coloum = 0
for i in range(_len1):
	for j in range(_len2):
		score_temp = {
		    'up': score_table[i][j+1]+w_indel,
		    'left': score_table[i+1][j]+w_indel,
		    'up&left':score_table[i][j]+(w_match if data1[i] == data2[j] else w_dismatch),
		    'end':0
		}
		# using zip to exchange keys and values，then use max to get the highest score
		result = max(zip(score_temp.values(), score_temp.keys()))
		score_table[i+1][j+1] = result[0]
		flag_table[i+1][j+1] = result[1]
		#update max_score and record its position
		if result[0] > max_score:
			max_score = result[0]
			max_score_row = i+1
			max_score_coloum = j+1

#trace back to find the optimal local sequence	
i = max_score_row 
j = max_score_coloum
seq1 = []
seq2 = []
while i != 0 or j != 0:
	if flag_table[i][j] == 'up':
		if i > 0:
			seq1.insert(0, data1[i-1])
			seq2.insert(0, '_')
			i -= 1
		else:
			break		
	elif flag_table[i][j] == 'left':
		if j > 0:
			seq1.insert(0, '_')
			seq2.insert(0, data2[j-1])
			j -= 1
		else:
			break
	elif flag_table[i][j] == 'up&left':
		if i > 0 and j >0:
			seq1.insert(0, data1[i-1])
			seq2.insert(0, data2[j-1])
			i -= 1
			j -= 1
		else:
			break
	else:
		break

time_end = time.clock()

#record the results.
f1 = open('result'+str(seq_num1)+str(seq_num2)+'.txt','w')
f1.write('The best distance score between sequence'+str(seq_num1)+' and sequence'+str(seq_num2)+': '+str(score_table[_len1][_len2])+'\n')
f1.write('The length of sequence'+str(seq_num1)+' and sequence'+str(seq_num2)+': '+str(len(seq1))+'  '+str(len(seq2))+'\n')
f1.write('The running time is: '+str(time_end-time_start)+'\n\n')
f1.write('The sequence'+str(seq_num1)+' and the sequence'+str(seq_num2)+':\n\n')

i = 1
len_row = 70
while i*len_row < len(seq1):
	f1.write("".join(seq1[(i-1)*len_row:i*len_row])+'\n')
	f1.write("".join(seq2[(i-1)*len_row:i*len_row])+'\n\n')
	i += 1

f1.write("".join(seq1[i*len_row:])+'\n')
f1.write("".join(seq2[i*len_row:])+'\n\n')
f1.close()

```
### Result 

The best distance score between sequence1 and sequence2: 3165
The length of sequence1 and sequence2: 1412  1412
The running time is: 5.353489
The sequence1 and the sequence2:

	AGAGTTTGATCCTGGCTCAGGATGAACGCTAGCTACAGGCTTAACACATGCAAGTCGAGGGGCATCAGGG 
	AGAGTTTGATCCTGGCTCAGGATGAACGCTAGCTACAGGCTTAACACATGCAAGTCGAGGGGCAGCATGA

	GAGTAGCTT_GCTATTCCCGCTGGCGACCGGCGCACGGGTGAGTAACACGTATCCAACCTG_CCCTCATC 
	GAGGA_CTTCGGTCCTTTCGATGGCGACCGGCGAATGGGTGAGTAACACGTATCCAACCTGCCCCTGA_C

	CCGGGGATAGCCCTCTGAAAGGAGGATTAATACCCGATGCG_GTCATTTCGGGACATCCTGTTATGACTA 
	CGGGGGATAGCCCTCCGAAAGGAGAATTAACACCCCATGGGTGTCCGTGC_GG_CATCGCGCGGCCATGA

	AAGATTCATCGGATGA_GGATGGGGATGCGTCTGATTAGCTTGTTGGCGGGGTAACGGCCCACCAAGGCA 
	AAGGTT_A_CGG_TCAGGGATGGGGATGCGTCCGATTAGCTTGCTGGCGGGGTAACGGCCCACCAGGGCG

	TCGATCAGTAGGGGTTCTGAGAGGAAGGTCCCCCACATAGGAACTGAGACACGGTCCTAACTCCTACGGG 
	TCGATCGGTAGGGGTTCTGAGAGGAAGGTCCCCCACACTGGAACTGAGACACGGTCCAGACTCCTACGGG

	AGGCAGCAGTGAGGAATATTGGTCAATGGGCGTGAGCCTGAACCAGCCAAGTAGCGTGAGGGACGACTGC 
	AGGCAGCAGTGAGGAATATTGGTCAATGGGCGGAAGCCTGAACCAGCCAAGTAGCGTGAAGGATGACGGC

	CCTATGGGTTGTAAACCTCTTTTATGCGGGAATAACGGTCGCTACGTGTAGCGGCGTGCATGTACCGCAT 
	CCTACGGGTTGTAAACTTCTTTTATGCGGGAACAAAGTGCGCCACGCGTGGCGTTTTGCGCGTACCGCAG

	GAATAAGCATCGGCTAATTCCGTGCCAGCAGCCGCGGTAATACGGAAGATGCGAGCGTTATCCGGATTTA 
	GAAAAAGCACCGGCTAATTCCGTGCCAGCAGCCGCGGTAATACGGAAGGTGCGAGCGTTATCCGGATTCA

	TTGGGTTTAAAGGGAGCGTAGGCGGA_CGCTTAAGCCTGCGGTAAAATGTCAGC_GCCCAACGCTGG_CC 
	TTGGGTTTAAAGGGAGCGTTGGCGGAGCGC_CAAGTCAGCTGTGAAAT_CCCGCGGCTCAACCGTGGAAC

	CGCCGTGGGAACTGGGTGTCTTGAATGTGCACA__AGGAAGGTGGAATTCGTGGTGTAGCGGTGAAATGC 
	TGCAGTTGAAACTGGCGCCCTTG__TGTGCACATGAGGATGGTGGAATTCGTGGTGTAGCGGTGAAATGC

	TTAGATATCACGAAGAACTCCGATTGCGAAGGCAGCCTTCTGGGGCATGATTGACGCTGAGGCTCGAAAG 
	TTAGATATCACGAAGAACTCCGATTGCGAAGGCAGCTGTCTGGGGTGCCACTGACGCTGAGGCTCGAAAG

	TGCGGGTATCGAACAGGATTAGATACCCTGGTAGTCCGCACAGTAAACGATGAGTGCCCGCTCTCGGCGA 
	TGCGGGTATCAAACAGGATTAGATACCCTGGTAGTCCGCACAGTAAACGATGGATACTCGTGGTCGGCGA

	CAGACAGTCGGGGGCCAAGCGAAAGCATTAAGCACTCCACCTGGGGAGTACGCCGGCAACGGTGAAACTC 
	CACACTGCCGGTCACCAAGCGAAAGCGATAAGTATCCCACCTGGGGAGTACGCCGGCAACGGTGAAACTC

	AAAGGAATTGACGGGGGCCCGCACAAGCGGAGGAACATGTGGTTTAATTCGATGATACGCGAGGAACCTT 
	AAAGGAATTGACGGGGGCCCGCACAAGCGGAGGAACATGTGGTTTAATTCGATGTTACGCGAGGAACCTT

	ACCCGGGCTTGAACTGGACATGACGTTGGGCAGAGA_CGCCCGATCTCCCGCAAGGGCATGCTCGGAGGT 
	ACCCGGGCTTGAACTGCA_AGGACCGTCCG_AGAGATCGGTC__T_TCCCTTCGGGGCCTTGGCGGAGGT

	GCTGCATGGTTGTCGTCAGCTCGTGCCGTGAGGTGTCGGCTTAAGTGCCATAACGAGCGCAACCCCCCTG 
	GCTGCATGGTTGTCGTCAGCTCGTGCCGTGAGGTGTCGGCTTAAGTGCCATAACGAGCGCAACCCCTGTC

	TGTAGTTGCCATCAGGTAATGCTGGGCACTCTGCACATACTGCCATCGCAAGATGTGAGGAAGGTGGGGA 
	TTCGGTTGCCATCGGGTAATGCCGGGCACTCCGGAGATACTGCCATCGCAAGATGTGAGGAAGGTGGGGA

	TGACGTCAAATCAGCACGGCCCTTACGTCCGGGGCTACACACGTGTTACAATGGGGGGCACAGAGAGCTG 
	TGACGTCAAATCAGCACGGCCCTTACGTCCGGGGCTACACACGTGTTACAATGGGAGGTACAGAAGGCTG

	CTGCATGGCGACATGCGGCGAATCTTGAAATCCCCTCTCAGTTCGGACTGGAGTCTGCAACCCGACTCCA 
	CTACCCGGCGACGGGATGCCAATCCCCAAATCCTCTCTCAGTTCGGATCGGAGTCTGCAACCCGACCCCG

	CGAAGCTGGATTCGCTAGTAATCGCGCATCAGCCATGGCGCGGTGAATACGTTCCCGGGCCTTGTACACA 
	TGAAGCTGGATTCGCTAGTAATCGCGCATCAGCCATGGCGCGGTGAATACGTTCCCGGGCCTTGCACTCA

The two sequences, each of total 1412 characters, show 1222 matched characters, 164 mismatched and 26 indels.

The score of optimal alignment is 3424, which equals to 3*1222-1*164- 3*26.

Noting that the number of indels is much less than the number of mismatched characters, it is reasonable because the score of indel is minus three, much lower than that of mismatched, minus one. Thus, the optimal alignment of two sequences is not only determined by the sequences themselves, but also affected by the scoring functions we defined. Considering this relatively random initial definition, results of sequence alignment result is actually somewhat subjective.


![](https:#raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/img/blog_logo.png)
