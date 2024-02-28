---
{"dg-publish":true,"permalink":"/D-Source/使用NLTK统计真题词频以及搜索真题句子/","created":"2022-06-15T23:20:40.000+08:00","updated":"2022-06-15T23:20:40.000+08:00"}
---

# 使用NLTK统计真题词频以及搜索真题句子
## 前言
作为一个教研和英语编辑，统计真题词频和搜题是刚需中的刚需。

虽然这个需求可以通过使用企业内部的数据库，或是自行去类似[组卷网](https://zujuan.xkw.com/gzyy/)这样的网站解决，但是有的时候还是自己做会比较好，不仅因为自己操作有很高的自由度（如绘制词云、提取关键词、一键撰稿、筛选高频词等），更是因为数据都从自己手中过能从宏观角度加深对题目的认识。

自己统计词频所需要的东西很简单：
1. 真题（为方便读取最好是txt格式的）
2. 考纲词表

下面是代码讲解和示例：

## 统计词频
首先导入需要的包。[NLTK](https://www.nltk.org)可以说是民用NLP界的最强武器了。
```Python
import nltk  
from nltk.probability import FreqDist  
from nltk.corpus import wordnet
import pandas as pd
```

**第一步，分句子**

不要着急直接把所有内容变成token，splitter.tokenize()能够把文章分成句子，这对之后标记词性以及搜索整句真题句子很有帮助。
```Python
def get_sentences(text):
	splitter = nltk.data.load('tokenizers/punkt/english.pickle')
	sentences = splitter.tokenize(text)
	return sentences
```
```
>>>['We had awakened early that winter morning, puzzled at first by the added light that filled the room.']
```

**第二步，分词**

此处使用Treebank的分词器分词
```Python
tokenizer = nltk.tokenize.TreebankWordTokenizer()

def get_token(sentences):
	return [tokenizer.tokenize(sent) for sent in sentences]
```
```
>>>[['We', 'had', 'awakened', 'early', 'that', 'winter', 'morning', ',', 'puzzled', 'at', 'first', 'by', 'the', 'added', 'light', 'that', 'filled', 'the', 'room', '.']
```

**第三步，标记词性**

这里是为了更好的[lemmatization](http://en.wikipedia.org/wiki/Lemmatisation)而做准备。

统计词频肯定是需要尽量把所有变形的单词都统计进去，例如goose和geese，play、playing、played肯定不应该分开计数。对于更精确的统计可能需要用到各式各样的神经网络模型，像我们这种民用级的小玩具只需要查询字典+还原成原型就可以了。

lemmatization需要根据词性判断才能准确将单词还原成原型，例如meeting在'We're meeting in a room."中就应该还原成meet，而在"I am attending a meeting"中就应该还原成meeting。

NLTK自带的pos_tag()就可以很好的根据token所在句子的语境标记上正确的词性。

```Python
def pos_tag(tokens):
	return [nltk.pos_tag(token) for token in tokens]
```
```
>>>[[('We', 'PRP'), ('had', 'VBD'), ('awakened', 'VBN'), ('early', 'RB'), ('that', 'IN'), ('winter', 'NN'), ('morning', 'NN'), (',', ','), ('puzzled', 'VBD'), ('at', 'IN'), ('first', 'JJ'), ('by', 'IN'), ('the', 'DT'), ('added', 'JJ'), ('light', 'NN'), ('that', 'WDT'), ('filled', 'VBD'), ('the', 'DT'), ('room', 'NN'), ('.', '.')]
```

这里的词性使用的是[Penn Treebank格式](https://www.ling.upenn.edu/courses/Fall_2003/ling001/penn_treebank_pos.html)，但是NLTK的lemmatize()所接受的词性参数只能是'a', 'v', 'n'这样的Wornet格式，所以我们还需要自己再写一个函数，把Treebank的词性改一下格式：

```Python
def get_wordnet_pos_tag(nltk_pos_tag):  
    if nltk_pos_tag.startswith('J'):  
        return wordnet.ADJ  
    elif nltk_pos_tag.startswith('V'):  
        return wordnet.VERB  
    elif nltk_pos_tag.startswith('N'):  
        return wordnet.NOUN  
    elif nltk_pos_tag.startswith('R'):  
        return wordnet.ADV  
    else:  
        return wordnet.NOUN
```

**第四步，lemmatize**
为了方便后续获取单词、词性和lemma，索性就直接打印在一个list里输出了
```Python
def lemmatize(pos_tokens):  
    pos_tokens = [[(word, lemmatizer.lemmatize(word, get_wordnet_pos_tag(pos_tag)), [pos_tag])  
                   for (word, pos_tag) in pos] for pos in pos_tokens]  
    return pos_tokens

def get_lemma(pos_tokens):
	return [lemma[1] for i in range(len(pos_tokens)) for lemma in pos_tokens[i]]
```

```
>>>[[('We', 'We', ['PRP']), ('had', 'have', ['VBD']), ('awakened', 'awaken', ['VBN']), ('early', 'early', ['RB']), ('that', 'that', ['IN']), ('winter', 'winter', ['NN']), ('morning', 'morning', ['NN']), (',', ',', [',']), ('puzzled', 'puzzle', ['VBD']), ('at', 'at', ['IN']), ('first', 'first', ['JJ']), ('by', 'by', ['IN']), ('the', 'the', ['DT']), ('added', 'added', ['JJ']), ('light', 'light', ['NN']), ('that', 'that', ['WDT']), ('filled', 'fill', ['VBD']), ('the', 'the', ['DT']), ('room', 'room', ['NN']), ('.', '.', ['.'])]

>>>['We', 'have', 'awaken', 'early', 'that', 'winter', 'morning', ',', 'puzzle', 'at', 'first', 'by', 'the', 'added', 'light', 'that', 'fill', 'the', 'room', '.']
```
**第五步，统计词频**

接下来就很简单了，直接用freqdist就可以拿到统计数字了，输出就是一个字典，然后你可以用你准备好的词表word_list去查询就可以获得想要单词的频率了
```Python
def get_freq_dict(lemma):  
    return FreqDict(lemma)

def get_freq(freq_dict, word_list):  
    return [freq_dict[word.strip('\n')] for word in word_list]
```

**第六步，做成csv分享给你的同事**
```Python
df = pd.DataFrame(columns=["单词", "频率"])  
df['单词'] = word_list  
df["频率"] = freq_list  
df.to_csv("output.csv")
```

## 搜题
有了统计词频和lemmatize的代码之后，我们去搜真题句子就方便很多了。理想情况是我们把所有真题的txt文件都装在一个文件夹里，然后它们的标题就是题目年份，例如'2016年高考英语全国卷1B.txt', '2021英语高考全国甲卷D篇.txt'

我们可以用os遍历整个文件夹，每次循环查看一个文件，分词并lemmatize里面的内容，然后用词表匹配lemma，如果匹配到了考纲词汇，就可以直接输出上述第一步中的一整句话，最后再用document做成Word文档分享给同事即可。

下面是代码示例：
```Python
for word in word_list:
    found = []  #  每一个单词专用一个存真题句子的list，每次循环重置
	
	# 每个词都遍历一遍所有真题
    for dirpath, dirnames, filenames in os.walk(paper_path):  
        for file in filenames: 
			# 过滤掉烦人的DS.store
            if file.endswith('.txt'):  
				
				# 第一步～第三步
                text = t.get_text(f"{dirpath}/{file}")  
                sentences = splitter.tokenize(text)  
                tokens = [tokenizer.tokenize(sent) for sent in sentences]  
                lemma_pos_token = lemmatize(tokens)  
  
				# 第四步
                lemmatized_sent = []  
                for i in range(len(lemma_pos_token)):  
                    lemmatized_words = []  
                    for lemmas in lemma_pos_token[i]:  
                        lemmatized_words.append(lemmas[1])  
                    lemmatized_sent.append(lemmatized_words)  
  
				# 开始匹配
                for token_sent in lemmatized_sent:  
                    if word in token_sent:  
                        target_sent = " ".join(token_sent)  
                        found.append(file.strip('.txt') + "  " + target_sent)  
    
	# 添加到document中，导出成Word文档
	document.add_paragraph(word)
	for f in found:  
   		document.add_paragraph(f)
    document.add_paragraph('')  
  
document.save('~.docx')
```