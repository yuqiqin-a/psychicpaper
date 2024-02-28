---
{"dg-publish":true,"permalink":"/D-Source/define-high-freq-words/","created":"2022-08-11T16:36:50.000+08:00"}
---

# 什么叫做常用词？
## 背景
最近在带实习生制作派生词相关的内容。根据产品的构想，我们会给用户展示单词的派生词，但在实际操作中很快就遇到了一个问题——派生词有哪些？

这个问题一部分来自英语构词法，派生（derivative）是有延展性的（productive）。例如：在一个动词后面+er，就可以给这个动作赋予一个施事者（agent）；在一个名词后面+less，就可以变成一个表示“缺少.……”的形容词；如果想要表示“缺少……”的这种属性，我们还可以在后面再加一个ness……

这种构词法无穷无尽，每一个词都可以拥有让人意想不到的派生词，字典是没有办法做到应收尽收的。

我最后提议的解决方法还是找一个字典作为参照，无论有没有收录都以这个字典为准，这样就避免被人摘指收录不全的问题了。

但是随之而来的是另一个问题——派生词太多了。

以operate为例，字典中检索到的派生词有这些：
- operation  
- operational  
- operationalize  
- operationalism  
- operator  
- operatorship  
- operating  
- operative  
- operant  
- operable  
- operand

足足有11个，如果不加整理全部放在APP内展示，可能会增加学习的负担。于是我们就想只展示常用词。

## 常用词的定义
那什么样的词算是常用词呢？

如果让人工地去判断，那一定会陷入“公说公有理，婆说婆有理”的死循环，每个人对单词的感觉都不一样。

![Pasted image 20220811155421.png](/img/user/B-Attachment/Pasted%20image%2020220811155421.png)

上图是我和产品举例plastic的派生词，plasticity常用，而plastique和plasticine不常用之后她的回复。

然而事实上查询[Google N-gram](https://books.google.com/ngrams/)可知，plasticity一定是压倒性地比plastique和plasticine更常用的。

![Pasted image 20220811160407.png](/img/user/B-Attachment/Pasted%20image%2020220811160407.png)

Google N-gram或许可以作为一个参考标准，但是它有两个问题：
1. 唯一找到的一篇关于API调用的[博客](https://jameshfisher.com/2018/11/25/google-ngram-api/)，示例代码是用JavaScript写的，如果要研究，则需要花掉我不少时间；
2. 我们需要思考出一个周全的计算方法，量化“常用”这个概念。

我尝试过使用标准差的概念（假设API可以正常使用），例如求平均所有的N-gram值，然后标准差超过1个sigma的就弃用，但是总觉得不好用，也不是特别科学。

![Pasted image 20220811160717.png](/img/user/B-Attachment/Pasted%20image%2020220811160717.png)


## 解决方案
我们最后选择了[COCA词表](https://www.english-corpora.org/coca/)，它的frequency list能查阅到60 000个单词的语料词频，越靠前的频率越高。

据各种英文学习网站称，20 000个以前的单词一般就算高频常用，可以覆盖所有英文考试的内容，且可以认识日常生活中99%的单词。25 000-30 000为母语人士成年后达到的词汇水平，成年后仍会小幅增长。

因此我们认为，可以把COCA词频的基础上划一条线，在这条线以内的单词就算常用，目前这套线划在了35 000，比母语人士的上限稍微多一些。

为了避免误杀，我们还是会做一个人工核查，就怕有些确实很常用的单词恰好落在了35 000的范围之外。目前找到的一个例子是courtesy，它的同根词court被排在了50 214位，拿掉真的太可惜了。

虽然这是一篇分类在教研底下的文章，但是还是想放一下代码实现，毕竟查看COCA的这个操作必须不能人来做，不然效率实在太低了。

查看COCA的文件：
1. 如果单词不存在，告知不存在，单词直接弃用
2. 如果单词存在，且在35 000位以内，则保留使用
3. 如果单词存在，但是在35 000位以外，则返回单词+COCA词频，方便教研进一步判断到底离35 000的距离有多远

```Python
json_file = open("~/coca60000.json")  
data = json.load(json_file)

def get_coca(word):  
    str_ = ""  
    try:  
        if data[word] > 35000:  
            str_ += f"{word},{data[word]}"  
    except KeyError:  
        str_ += f"{word},无"  
    return str_
```