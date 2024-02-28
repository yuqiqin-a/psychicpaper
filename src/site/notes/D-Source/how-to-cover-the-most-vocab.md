---
{"dg-publish":true,"permalink":"/D-Source/how-to-cover-the-most-vocab/","created":"2022-06-28T16:36:33.000+08:00","updated":"2022-06-28T16:36:33.000+08:00"}
---

# 200篇高考文章选60篇，怎么选覆盖的考纲词汇最多？
## 背景
最近出版团队设想了一款新的出版产品，用一句话概括是「60篇高考文章掌握高考核心2000词」。如果这款产品真的要做出来的话，那必须要解决两个核心问题：
1. 哪2000词？
2. 哪60篇文章？

第一个问题相对比较好解决，使用之前写好的词频统计脚本就能得到一个高考真题的高频词列表，结合实际做题经验和用户测试，即可在高考3500词考纲中锁定这2000个词。

第二个问题就比较复杂了。60篇文章完全覆盖2000个单词应该不太可能，但是怎么挑选这60篇文章才能覆盖最多的考纲词呢？这个听起来应该不是教研手动统计能解决的问题，于是我开始寻找能够解决这个问题的算法。

## 第一次尝试：动态规划
我第一时间想到的是大学时了解过的动态规划（[Dynamic Programming](http://en.wikipedia.org/wiki/Dynamic_programming)），一个用来寻找最优解的算法，常用于地图应用寻求最优路径。

它最大的特点就是，能够把一个整体的问题（如何最短地从A点去到B点），拆分成若干个子问题，并且在子问题中通过寻找每一步最优解找到整体的最优解。用我们这个出版问题做个类比，就是：
1. 先选1篇文章，看看覆盖了多少个单词
2. 再选第2篇文章，看看新增覆盖了多少个单词
3. 再选第3篇文章，如果没有提升，就放弃这篇文章看第4篇；如果有提升，就记录这第3篇
4. ...

我随后了解到，一个比较近似且基础的算法问题是背包问题：
> 有一个最大容纳重量为2000g的书包（2000个考纲词），另有重量（考纲词覆盖程度）不一的许多个物品（阅读文章），怎么装能够装下尽可能多的物品。

经过一番学习之后，终于搞懂了下面这个示例算法每一步的作用。因为过于复杂，这里就不再赘述。

```Python
def dp_search(max_weight, item_weight: list):
	if max_weight is None or len(item_weight) == 0:
		return 0

	N = len(item_weight)
	bp = [[0] * (max_weight + 1) for _ in range(N + 1)]

  	for i in range(N):
		for j in range(max_weight + 1):
			if item_weight[i] > j:
				bp[i + 1][j] = bp[i][j]
			
			else:
			# 情况1：不装 = (bp[i][j])
			# 情况2：装 = 物品不大于 j-arti[i]时的重量 + 装下这个i的数量item_weight[i]
					# = (bp[i][j - item_weight[i] + item_weight[i])
				bp[i + 1][j] = max(bp[i][j], bp[i][j - item_weight[i]] + item_weight[i])
	# N-1个物品（全部物品）中，选出不超过max_weight的最大值
	print(f"最大重量：{bp[N - 1][max_weight]}")

dp_search(1000, [213, 343, 544, 213, 213, 345, 75, 456, 123, 674])

```

但是这并不能解决问题，原因有三：

第一，重量和单词覆盖度是不一样的。假设两篇文章都包含了考纲中的200个单词，虽然覆盖度都等于200，但是如果它们覆盖的是相同的200个单词，那就达不到广覆盖的目的了。

第二，覆盖度不存在局部最优解。正是因为每篇文章覆盖的单词可能重合，A+B+C和A+C+D的覆盖度可能天差地别，每一次计算都应该整体地去考虑才能拿到答案。

第三，计算数量级太大，从200篇文章里挑60篇文章，可能产生的排列组合已经是个天文数字，就算现在计算机计算的速度很快，这也不是一个英语编辑使用的电脑能够处理的数据量。

我们必须想一个比起单纯循环更优雅的解决方案。


## 第二次尝试：向量

在咨询了在爱大认识的一个计算机专业的博士朋友后，他给出了一个从数学角度的解决方案：
1. 将每一篇文章变成一个2000维的向量，对应2000个考纲词。向量的值是1和0，考纲词在这篇文章里出现即为1，没出现则为0；
2. 60本书的向量相加点除自己加一个极小值（`sum(V) / sum(V) + 极小值`）
3. 求和这2000维
4. 每次循环比较这个和，谁更接近2000，就选谁

这个方法采用的是次优解的方案，减少了很多计算量，在时间充裕的情况下可以一直跑，跑到一个满意的值为止。对于民用级别的计算来说，已经算是非常亲民的解决方案了。

下面是具体实现：
1. 读取文件
```Python
def get_text(file_name):  
    file = open(file_name)  
    raw_text = file.readlines()  
    text = "".join(raw_text)  
    file.close()  
    return text
```
2. 向量化
```Python
def vectorise(tokens, word_list):  
    return [1 if word in tokens else 0 for word in word_list]
```
3. 向量点除及求和
```Python
import numpy as np

def sum_vectors(*vectors):  
    return np.sum(sum(np.array(vectors)) / (sum(np.array(vectors)) + 0.1*(10**-8)))
```
4. 循环找最优解
```Python
import random

articles = {}		# key, value = 文章名，向量
arti_pool = []  	# 60篇文章
vectors = []  		# 查询字典得到的60篇文章向量
final_result = 0  	# 最优解的和

# 不覆盖满2000词不停止 
while final_result < 2000:  
	
	# 随机从处理好的文章中挑选60篇，暂存到arti_pool
    arti_pool.append(random.sample(list(articles), 60))  
	
    for arti_list in arti_pool:  
        for arti in arti_list:  
			# 从字典中找出这60篇文章的向量
            vectors.append(articles.get(arti)) 
		
		# 求和，如果数值有提高，就更新
        result = sum_vectors(vectors)  
        if result > final_result:  
            final_result = result  
            print(result, arti_list)  
        else:  
            continue
		
		# 清除文章和向量列表，准备下一次循环
        vectors.clear()  
        arti_list.clear()  
		
    # 清除文章和向量列表，准备下一次循环
	vectors.clear()  
    arti_pool.clear()
```

最后跑了一圈发现，200篇高考真题文章里选60篇，考纲单词覆盖率最高在1400词左右。所以到时候申报选题以及撰写说明页的时候还是得解释一下，讲清楚为什么只涵盖了1400个词，不然出版社审核可能不过，而且用户也会来问。