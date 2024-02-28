---
{"dg-publish":true,"permalink":"/D-Source/from-shimo-to-excel-to-shimo/","created":"2022-10-12T18:02:53.000+08:00","updated":"2022-10-12T18:02:53.000+08:00"}
---

# 从石墨表格到Excel，再到石墨表格

## 背景
这一个半月因为业务需要，我承担起了数据和内容管理的责任，时常要把内容从后台或是数据库导出来，做成一张表格并粘贴到石墨表格供多人协作，或是将石墨表格的内容拆成不同样式的子表给编辑实习生和教研操作，或是根据字表的内容再次批量上传到后台。

在这个过程中，我需要频繁地对表格进行处理，算是理解了「一切to-B软件的尽头是Excel表格」这句话，今天就来记录一下这个过程中学到的各种操作。


## 经验
### 1. 字典大法好
假设我有一张大而全的表A，我有一些单词，我需要根据这些单词在表A里找到他们的音标和释义，整理到表B。

以往做这个操作时，我会实时地去读取表A的内容，然后写入到新的DataFrame，如`df['音标'].loc[df['释义'] == 单词].values[0]`，但是`loc`如果找不到内容，或是内容有问题，会直接报错中断循环。就算写了`Exception`去处理这样的情况，也很难同时截断所有的问题。

我发现使用字典先存起来是个更好的操作，完全不用担心脆弱的`loc`报错中断循环：
```Python
dict_ = {}  
for i in range(len(df['单词'].to_list())):  
    try:  
        dict_[df['单词'][i]] = [df['音标'][i], df['释义'][i]]
    except AttributeError:  
        pass
```

当表格内容是为空时，异常就会被`AttributeError`抓住，字典就不会有这个Key，后续处理就方便很多了，在字典的都是有数据的，不在的都是没有数据的，而且还可以提前打印字典预检内容。

在读取内容的时候，可以直接写`dict_[word][0]`拿到这个单词的音标，或是`dict_[word][1]`拿到这个单词的释义，非常简便。


### 2. 清除空行
当把本地csv和xlsx格式的文件内容复制到石墨表格的时候，不知为什么石墨表格总是非常“贴心地”在单元格内容前后加两个换行符。

更离谱的是，从石墨表格下载到本地的xlsx文件，单元格里总是有很多莫名其妙的空行。当你尝试读取这个单元格并用换行符做分隔时，往往看到的是`result = ['', '', '', 'adj. 好的'， '', '']`这样的内容，于是处理空行成了每天的家常便饭。

我觉得用得最顺手的改法是用`filter`过滤`None`
```Python
result = list(filter(None, df['释义'][i].split('\n')))
```

当然，使用`df.apply()`或是其他一些方法提前过滤，然后再读取也是可以的，如：
```Python
meanings = list(map(lambda x: x.replace('\n', ''), df['释义'].to_list()))
```
但是这种方法如果碰上空单元格就会报错，所以不如遇到一个处理一个。


### 3. 导出
以往导出成Excel表格，我都是把要存入的数据整理成等长的列表，然后写到DataFrame里：
```Python
word_list = []
meaning_list = []
phon_list = []

new_df = pd.DataFrame()
new_df['单词'] = word_list
new_df['释义'] = meaning_list
new_df['音标'] = phon_list 

new_df.to_csv('')
```
但是当数据一多起来，特别是涉及到网络请求，或是从一个表找东西到另一个表，一旦出现报错就全部中断了，因为导出是最后一步才操作的。

后来想了一个断点续传的办法——按行写成csv文件：
```Python
word_list = []
meaning_list = []

for i in range(len(word_list)):
	with open('~.csv', 'a') as a:
		a.write(f"{word_list[i]}\t{meaning_list[i]}\n")
```
不同的column可以用`\t`隔开，因为是append，所以断了的话直接修改range就能实现断点续传了。


### 4. 断点重试
还有一种情况是需要处理中断的，就是需要使用`requests`发送网络请求的，当连接中断抛出异常之后，下一次循环需要能够再发送一次请求，直到正常处理。

其实也没有想象中的难，在循环内i-1就可以了。
```Python
word_list = []

with open('/Users/qinyuqi/Desktop/output.txt', 'a') as a:  
    counter = 2593  
  
    for i in range(counter, 3001):  
        print(f"i = {i}")  
        try:  
            print(f"Working on: {word_list[i]}, {i}/{len(word_list)}")  
            derivs = get_data(word_list[i])  
            a.write(derivs)  
            sleeptime = random.randint(15, 25)  
            time.sleep(sleeptime)  
  
        except requests.exceptions.ProxyError:  
            print(f"i before error = {i}")  
            i -= 1  
            print(f"i after error = {i}")  
            print('Ah-oh! Proxy Error occurred!')
```