---
{"dg-publish":true,"permalink":"/D-Source/使用pandas和docx一键生成出版稿件/","created":"2022-06-16T16:16:57.000+08:00"}
---

# 使用pandas和docx一键生成出版稿件

## 背景
记得刚入职的时候要做一套刷题练习册，题量全部加起来有差不多6000多题。

在撰稿的时候，为了方便核查和编辑，我们先是在一个在线表格中，把题目、题目出处、单元号、选项、单元等信息填写好，然后再拼在一起做成一个可以交付给出版社的稿件。

如果一个一个单元格的内容全部手动复制出来，然后粘贴在一个Word文档就太费力了，而且还容易出错，于是就打算写个脚本一键生成。

## 具体实现
**第一步，导入需要用到的包**

主要是用来读取表格的pandas和操作Word文档的docx，random是用来打乱题目顺序的
```Python
import pandas as pd  
from docx import Document  
import random
```

**第二步，读取csv文件**

大部分在线表格都有导出成Excel和csv的功能
```Python
df = pd.read_csv("～.csv")
```

**第三步，添加标题**
```Python
document_ques = Document()  
document_ques.add_heading('第一册题目', 0) # 0是标题，1是一级标题
```

**第四步，设置循环取数据**

总共有30个单元，每个单元有14道题，前7题是基础题，后7题是进阶题
```Python
#   总共30个单元  
for i in range(1, 31):  
    print(f"录入第{i}个单元……")  
    document_ques.add_heading(f"Unit {i}", level=1)  
  
#   1～7题基础题  
    document_ques.add_heading("基础题", level=2)  
    for j in range(1,8):
		
        que = j  
        ques_num += 1  
        word_counter += 1  
        word = wordlist[word_counter]  
        question = df.loc[df.单词 == word, '基础题 - 题目'].values[0]  
        answer = df.loc[df.单词 == word, '基础题 - 答案'].values[0]  
        document_ques.add_paragraph(f'{que}. ' + question)  
        document_ques.add_paragraph(f' {answer}')  
  
#   8～14题进阶题  
    document_ques.add_heading("进阶题", level=2)
    ques_num -= 7  
    word_counter -= 7  
  
    for j in range(8,15):  
        que = j  
        ques_num += 1  
        word_counter += 1  
        word = wordlist[word_counter]  
        question = df.loc[df.单词 == word, '进阶题 - 题目'].values[0]  
        answer = df.loc[df.单词 == word, '进阶题 - 答案'].values[0]  
        document_ques.add_paragraph(f'{que}. ' + question)  
        document_ques.add_paragraph(f' {answer}')  
  
    document_ques.add_page_break()
```
因为在原始表格中，每一道题都已经提前安排好单元号和题号了，所以我们只需要按顺序遍历就可以提取到内容。

使用`df.loc[df.col1 == key, 'col2'].values[0]`就可以用col1单元格的值，取到同一行col2的值。

另一种拿到同一行数据的方法是用列表计数`for i in range(表格行数)`，使用`df['colname'][i]`也可以定向选到同一行另一列的值。

**第五步，导出文件**
```Python
document_ques.save("～.docx")
```

## 结语
这个项目算是我入职以后，第一次尝试使用编程来解决实际业务问题，效果也很好，还挺有纪念意义的，从此也激发了我不断学习，想要探索更多使用编程解决英语编辑业务问题的场景，一直坚持到了现在，于是你就看到了现在的这篇博客文章。