---
{"dg-publish":true,"permalink":"/D-Source/我是怎么使用Obsidian的-5oiR/","created":"2023-01-04T15:59:36.000+08:00","updated":"2023-01-04T15:59:36.000+08:00"}
---

# 我是怎么使用Obsidian的
## 笔记管理
### 流派
笔记管理一般有几种流派：传统的文件夹派、标签派、[Zettelkasten](https://en.wikipedia.org/wiki/Zettelkasten)派、以及各种混合流派。

经过一段时间的折腾，我觉得最适合我自己的还是**低配版Zettelkasten**比较适合我，通过建立一个类似README的文件——MOC（Map of Content），给笔记建立一个索引，通过点击索引的方式快速找到对应主题的笔记。说是低配版Zettelkasten是因为，这种方式同样会选择把所有的笔记都丢到一个文件夹里，不做管理，仅通过索引和搜索定位笔记。但是我不会给笔记加上唯一的时间戳标识，也不会特别严格地把每一篇笔记都[原子化](https://neuron.zettel.page/atomic)。

### 文件夹
我总共有4个一级文件夹：

A-Template 用于存放所有的笔记模板。

我最常用的场景是在学习Java的时候，每次都要提前输入好一长串的public void和前后括号都很麻烦，直接做成文件名为java.md的模板，输入command+ shift + I呼出模板窗口，输入j即可快速输入。我还会保存换行符`&nbsp;`或是`System.out.println()`这样的snippet，用于快速输入。

B-Attachment 用于存放所有的图片。

所有复制进入笔记的图片都会自动保存在这个文件夹。

C-Indice 是存放索引的地方。

总共有4个二级文件夹：
- a. Professions —— 所有和工作相关的学习MOC都在这，如计算机、出版、写作、法律合规等。
	- 01-Computer Science
	- 02-Publication
	- 03-Writing
	- 07-Law
	- ...
- b. Life —— 跟生活相关的学习MOC都在这，如立直麻将、料理、旅行攻略等。
	- 01-リーチ麻雀
	- 02-Cooking
	- 04-Travel
	- ...
- c. Input —— 用来存放从文章、Newsletter、书本获取的碎片知识MOC，比如一些新奇的概念或观点等。
	- 01-Reading Notes
	- 03-Terms
	- ...
- d. Output ——自己写的心得感悟、视频稿、以及博客文章都在这

最后一个一级文件夹是D-Source，所有的非MOC笔记都会被存放在这，每次通过双链或者command+N创建的新笔记都会被自动保存在这个文件夹里。

只通过双链引用，减去了需要整理标签、整理文件夹等繁琐的维护工作。双链管理起来几乎不费精力，只需打上两个前括号就可以精确地通过搜索找到自己的笔记。需要调整笔记分类的时候只需要找到对应的MOC删除文本内容即可，极大减轻了只打扫、不产出的症状。

### MOC
某个特定领域的知识体系还是碎片知识，我都习惯使用二级标题+无序列表的方式组织MOC。

拿C.Indice/a.Professions/01-Computer Science.md这个MOC举例，我会对这个领域需要学习的知识进行一个大致的分类，例如：
-  Languages
	- Python
		- Python Syntax
		- Python Packages
	- Java
	- SQL
		- MySQL
	- html
	- css
	- ...
- Algorithms
	- Big O
	- Sort
	- Search
		- Bubble Sort
		- Merge Sort
	- ...

这些列表项目有的是MOC，有的是笔记正文，比如Languages - Python - Python Packages点进去是另外一个列表：
1. nltk
2. requests
3. pydash
4. ...

再点进去就是我对这些Python模块的详细笔记了。

同理，下方 Algorithms - Search - Bubble Sort 点进去就是冒泡算法的具体笔记，因为这个笔记已经足够原子化不可再分了。

对于碎片知识，我同样会像二级MOC一样使用有序列表，因为这些碎片知识本身就不可再分，我只是需要一个地方把它们存起来，最好的方式就是把它们摊平在桌面上，而不是找一个地方放起来再也不看。只有每次需要创建笔记的时候都能再看到它们，这样才不会成为松鼠，只收集、不消化，记了就忘。

下面是我的碎片知识01-Concept中的列表示例：
1. CETA Model
2. 技术负债
3. Margin of Safety
4. 汉隆剃刀
5. 巨人主义
6. ...

再给一个02-Viewpoints的列表示例，四处看来的有趣观点都会被收集在这：
1. 为什么有些人明明当不好老板，却当了老板？
2. 为什么蓝色iPhone12有一种廉价感
3. 悲观者正确，乐观者成功
4. [记笔记不是为了记住，而是为了遗忘](https://pmthinking.com/post/1127)
5. ...

当然，这个列表总有一天会变得非常长，等长到需要维护的时候再说吧，才写了几条笔记就开始想这么久远的事情，这又是效率洁癖的症状了。

### 工作流
最后我想分享的就是我的工作流了，有了上面的配置之后，每次创建新笔记要么是直接command+N，记完之后晚点再去对应的MOC建立索引；要么是去到对应的MOC的具体分区创建双链。

这个工作流已经非常可靠地运行了2年多了，至少已经调教到一个我可以非常舒服地记录的程度了，并且能够输出一个像这样的经验分享。当然，每个人使用Obsidian的方法非常不同。

如果你还没有找到适合自己的那个方法，不妨每个都试试看，我的这套方法不失为一个起点。

## 社区插件
**Advanced Table** 
- 输入 \| 再 输入 tab 即可快速创建表格
- 输入回车新建行
- 同时右侧工具栏支持对齐、排序、移动单元格、新建行和列、数学公式、导出CSV等功能

**Copy button for code blocks**

在代码块右上角会多一个复制按钮，一键复制以前收藏过的代码

**Daily Stats**

右侧工具栏会多一个类似Github提交记录的棋盘图，显示你的活跃数据

**Digital Garden**

用来发布博客用的，具体可以参考[[D-Source/使用Obsidian搭建免费的个人博客\|使用Obsidian搭建免费的个人博客]]

**Editor Syntax Highlight**

代码块显示代码高亮，支持各种语言。

**Kanban**

把笔记变成看板，体验还不错的，我用来管理我的视频制作进度。

**Mind Map**

自动将md文件的各级标题转变成思维导图，快捷键默认是command+shift+I，按得挺顺手。因为我暂时没有在Obsidian绘制思维导图的需求，我只安装了基础的查看插件。

**Natural Language Dates**

输入 \@  + 自然语言即可转换成数字的日期，用的最多的还是 \@  now，因为我不会用Obsidian来做日程管理。

**Obsidian Tabs**

支持像浏览器那样打开笔记，对于像我这样喜欢用双链做MOC简直是福音，不用每个笔记都手动添加一个回到首页的按钮。

**Recent Files**

在左侧工具栏File Explorer、Search之外，会多出一列用于浏览最近打开的文件。

## 外观主题

我现在使用的是**暗色模式 + Things**，因为这个主题：
1. 一级标题和二三级标题样式不同，可以当作文章标题使用
2. 二级标题自带分割线
3. 二三级主题之间的行间距看得很舒服
4. 高亮不是晃眼睛的黄色，而是长得像标签的灰色方块
5. 加粗是显眼的粉色，不像某些主题加了等于没加

在用过的主题中，California Coast和Yin and Yang都比较不错的，只是最后综合表格样式和暗色模式表现最后才选了Things。

强烈推荐大家把主题都下回来自己试试看，百闻不如一试。

## 同步
数据同步我之前用的是[坚果云](https://www.jianguoyun.com/)，无论是墙内墙外速度都很稳定，从来没有出现过文件重复或是丢失的情况，基本上多端都是秒同步。

因此无论是家里的机器还是公司的机器，只要同时下载坚果云和Obsidian，都能随时访问和编辑自己的所有笔记。

使用方法也很简单，直接把笔记文件夹放进坚果云的同步文件夹即可。

但是近期听说坚果云有和谐内容的事迹，因此现在将笔记迁移至[Dropbox](https://www.dropbox.com/)，目前体验下来和坚果云一样流畅。打算之后用社区插件[Obsidian-Git](https://github.com/denolehov/obsidian-git)在自己的Github上再做一份备份，定期保存。