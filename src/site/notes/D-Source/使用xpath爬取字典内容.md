---
{"dg-publish":true,"permalink":"/d-source/xpath/"}
---

# 使用xpath爬取字典内容
最近因为业务需要批量获取一些权威字典的内容，比如[韦氏字典](https://www.merriam-webster.com/)的音节划分，比如[词源字典](https://www.etymonline.com)中的词根词缀解释。

所有这些工作其实都可以提一个需求给开发的，但是提需求流程好长，耗时也好久噢。因为自己感兴趣，所以就索性自己动手研究了，当然这途中也少不了开发朋友的帮忙。

具体实现方法很简单：
1. 使用浏览器浏览页面元素，定位到对应的文本内容，复制对应的xpath
2. 使用requets发起http请求，并将返回的结果使用etree解析

## 标签下只有一个内容
```Python
import requests
from lxml import etree

def get_data(word):  
    wb_data = requests.get(f"https://www.merriam-webster.com/dictionary/{word}").text  
    html = etree.HTML(wb_data)  
    html_data = html.xpath(" /text()")  
	
	return html_data[0]  
```
此处需要在xpath的文本后面加上`' /text()'


## 标签下有多个内容
```Python
def get_data(word):  
    wb_data = requests.get(f"https://www.etymonline.com/search?q={word}").text  
    html = etree.HTML(wb_data)   
    contents = html.xpath("")  
    ret = []  
    for e in contents:  
        ret.append(e.xpath('string(.)'))
	return ret
```
xpath的string()可以获取当前子节点的所有文本