---
{"dg-publish":true,"permalink":"/D-Source/如何购买和绑定自定义域名5aaC/"}
---

# 如何购买和绑定自定义域名

成功使用Obsidian搭建免费的个人博客之后，下一步可能就会想给自己的博客添加一个自定义的域名，让博客更加个性化一些。

## 购买域名
购买域名我选择去[Domcomp](https://www.domcomp.com)，可以在搜索框里搜索你的前缀，然后网站就会告诉你这个前缀下，可使用的域名有哪一些。

建议在看价格的时候点击上方的「Renew」，因为一般新购入域名会有各种折扣，第一年非常便宜，然后续费的时候就贵死人。

另外像.me，.io，.com这样的域名会比较贵，如果不介意的话，你完全可以挑一些比较冷门的后缀做域名，如.porn，.website，.xyz或者各式各样的国家缩写后缀。

我现在使用的这个[yuqiqin.me](https://yuqiqin.me)是在老牌域名提供商[Godaddy](https://dcc.godaddy.com/)购买的，3年总价是331元，算是1年100元的价格也算是比较实惠了，毕竟一个月才9元钱，当作话费支付就行了。

购买的时候根据需求选择自己想要的服务就行，没有什么特别有坑的部分，我什么都没有选择。


## 绑定和配置
此处仅介绍Obsidian + Netlify这一解决方案的配置方法，其实整套流程看下来，我这个计算机小白看起来也晕乎乎的。

**第一步，添加自定义域名**

在Netlify - Domains处，点击「Add or register domain」，然后把刚刚购买的域名添加进去。


**第二步，配置DNS**

添加完之后会Netlify会问你是否要把DNS delegate在Netlify，选择Yes，然后点击「add DNS records」。

走完流程之后就可以在Domains - DNS settings - Name servers找到几个链接。

然后回到你的域名提供商，找到My domains - Domain Settings - DNS Management，把Nameservers从购买时默认赠送的换成刚才在Netlify生成的那几个链接。

域名提供商会在这个时候反复向你确认，是否把DNS放在其他地方解析，选择确定之后就可以等待生效了。

**第三步，没有了**

在Netlify - Site settings - Domain management - Domains - Custom domains，应该就可以看到你的自定义域名后面有Netlify DNS的标志。

此时在地址栏输入你的自定义域名，你就可以正常访问自己的博客了。
