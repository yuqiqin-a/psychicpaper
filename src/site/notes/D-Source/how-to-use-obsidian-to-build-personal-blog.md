---
{"dg-publish":true,"permalink":"/D-Source/how-to-use-obsidian-to-build-personal-blog/","created":"2022-06-16T18:04:33.000+08:00","updated":"2022-06-16T18:04:33.000+08:00"}
---

# 使用Obsidian搭建免费的个人博客
## 具体流程
### 配置
完整的流程可以参照原作者的[示范页面](https://notes.ole.dev/set-up-your-digital-garden/)以及Obsidian社区插件内部的介绍，但是并没有中文翻译，以下为我的精简版总结：
1. 下载[Obsidian](https://obsidian.md)
2. 注册一个[Github账号](https://github.com/signup)
3. 注册一个[Netlify](https://app.netlify.com/)账号，可以使用Github账号关联，关联成功就不用继续点了
4. 打开这个[repo](https://github.com/oleeskild/digitalgarden)，点击「Deploy to netlify」，它会自动在你的Github创建一个仓库，并初始化所有的内容，记得给你的仓库起一个类似“my-digital-garden”之类的合适的名字。
5. 点击[这里](https://github.com/settings/tokens/new?scopes=repo)，将“Expiration”设置成“No expiration”，点击「Generate token」并复制token
6. 在你的Obsidian设置中，点击「社区插件」-「浏览」- Digital Garden，下载并安装。然后激活该插件，并在插件设置中依次填写：
	- 刚刚生成的Github仓库名字
	- Github的id
	- 刚刚生成的token
7. 你可以在[这里](https://app.netlify.com/sites/)找到你的网站地址

### 使用
首先需要新建一个笔记作为主页，在顶端输入以下内容即可：
```
---
dg-home: true
dg-publish: true
---
```
`dg-home: true`的意思是，当前笔记将会作为主页展示，**不要设置多个主页！**

`dg-publish: true`的意思是，当前笔记将会发布在你的数字花园中，只有打了此标签的笔记才会被部署到数字花园中。

发布笔记的具体流程是：
1. 在主页创建一个双链
2. 点击新建笔记，并加上`dg-publish: true`标签
3. 按`ctrl/cmd + P`呼出命令行，输入`publish`，可选仅发布当前笔记，或是发布仓库内所有带标签的笔记
4. 在不更改Netlify配置的前提下，Netlify会自动部署到网站上，并且在Github中留下提交记录

此处有两个坑：
1. 不要新建只包含`dg-publish: true`的空笔记，否则部署会失败
2. 打了`dg-publish: true`的笔记最好都引用在主页，否则小概率会部署失败

注意，发布的笔记需要在你的主页使用双链引用才能展示，除非有什么css配置可以做出一个像博客一样的菜单出来，我暂时还没发现这样的用法。

大多数时候，你可以像正常使用笔记软件一样使用Obsidian，然后在需要展示的笔记页面加`dg-publish: true`，就可以发布笔记了，非常的方便。


## 使用Obsidian的优势

这个解决方案的优点在于：
1. 免费
2. 简单
3. 国内访问友好
4. 便捷
5. 支持css配置
6. 支持[[如何购买和绑定域名\|自定义域名]]

Obsidian Publish功能需要一个月$16，甚至比大多数购买域名+购买博客服务器+Github的解决方案还要贵。

其次，对于一个非CS专业的人来说，这个配置真正做到了简单易懂，且无代码。使用Wordpress对于小白来说真的是灾难，就连Hexo还需要下载Node.js、npm以及使用命令行敲代码。

另外Netlify算是一个比较大的服务器提供商，不用担心跑路。就算跑路了，我们的笔记都是存在本地的，不像语雀或者Notion，炸了就没了。其次，这个服务器在开代理的情况下接近秒开，没开代理稍等几秒也能正常访问，省去了纠结服务器的过程。

最后，这是基于一个笔记软件插件的解决方案，对于将Obsidian作为主力笔记的我来说，每天都需要频繁地在这个软件中查阅以及写作。现在只需要加上标签即可发布内容，极大地减少了折腾的精力。

这个解决方案除了提供基础的Obsidian外观预设，还支持使用css自行修改样式，可以达到一定程度的自定义。

那这个解决方案的缺点是什么呢？目前只感受到了一些高级功能缺失，如统计访客人数、打赏、评论区、文章标签和分类等……更多内容我也在探索中，欢迎大家与我交流。