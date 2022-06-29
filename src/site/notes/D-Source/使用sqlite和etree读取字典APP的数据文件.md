---
{"dg-publish":true,"permalink":"/d-source/sqlite-etree-app/","dgHomeLink":true,"dgPassFrontmatter":false}
---

# 使用sqlite和etree读取字典APP的数据文件
## 背景
在之前的工作中，我学会了如何[[D-Source/使用xpath爬取字典内容|使用xpath爬取字典内容]]，但是这毕竟不是一个长期的解决方案。因为爬虫容易受到请求次数的限制，而且随着网站升级反爬虫功能，在做业务的过程中很容易受到影响。

于是开发就想办法破解了安卓的apk安装包，拿到了一个后缀为db的文件。

破解这件事情暂时不在我操心的范围内，不知道以后有没有机会学一下类似的破解如何操作，但今天这篇博客的重点是如何读取db文件中的单词数据。

## 具体操作
db文件指的是database-related file，因此需要使用数据库相关的软件和库访问。

### VSCode预览
步骤：
1. 打开VSCode，安装SQLite插件
2. 新建一个文件，点击右下角Select Language Mode将语言切换成SQL即可在文件内部写SQL语句
3. 右键点击Run Query，会弹出选择database的窗口
4. Choose database from指向db文件即可

### 使用Python导出数据
需要使用的包有两个，一个是`sqlite3`用于读取db文件，一个是`etree`通过xpath定向选择数据库的内容。

XML的xpath可以在[xpather](http://xpather.com)转换，随便复制一段xml的代码，它就能帮你解析各个节点的xpath。当你确认好你想要的内容对应的是哪一段xpath之后，你就可以使用下面的代码导出内容了。

```Python
import sqlite3  
from lxml import etree

conn = sqlite3.connect('~.db')  
c = conn.cursor()  
  
cursor = c.execute("SELECT * from dict") # 把SQL语句写成字符串

# cursor相当于表格，row[3]是当前行的第4列
for row in cursor:
    text = row[3].decode()	# 如果数据是byte格式的，需要decode才能变成string
	_xml = etree.XML(text)	# 获取该“单元格”的xml
	
    for result in _xml.xpath(''): # 获取对应xpath地址的内容
		print(result.text)
```