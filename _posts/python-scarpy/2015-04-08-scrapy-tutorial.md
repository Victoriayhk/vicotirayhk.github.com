---
layout: post
title: Scrapy Doc - Turotial
category : translation
tagline: 
tags : [python, scrapy]
---

## 前言

如有违权什么的, 通知后立删.

本人不对文章的翻译质量做保障.

翻译对应的版本是0.24

--------------------------以下是正文---------------------------

在开始这章之前, 我们假设你的电脑已经安装了"Scrapy", 否则请先查阅有关安装教程, 安装完成后再来看这章内容

## 创建项目

在开始爬虫之前, 首先要创建爬虫项目, (命令行)进入你想储存代码的文件夹里, 在该文件夹创建项目, 以后你的代码都在在这个文件夹里运行, 下面为创建代码:
```
scrapy startproject tutorial
```
在上面的代码中, tutorial是你要的创建的项目的名称, 也是项目的总文件夹, 其文件结构如下:
```
tutorial/
    scrapy.cfg
    tutorial/
        __init__.py
        items.py
        pipelines.py
        settings.py
        spiders/
            __init__.py
            ...
```
这些预生成的文件有各自的功能:

+ `scrapy.cfg`: 项目配置文件
+ `tutorial/`: 项目的python模块, 之后会导入代码到这个文件夹
+ `tutorial/items.py`: 项目的item文件
+ `tutorial/pipelines.py`: 项目的pipeline文件
+ `tutorial/settings.py`: 项目设置文件[^ ?settting.py or scrapy.cfg差别]
+ `tutorial.spiders/`: 用来存放spider的文件夹


## 定义item
所谓*item*是指将会被导入爬虫数据的container, 有些类似于python中的字典型数据结构, 并且还会提供额外的针对在未申明的区域内定义数据的安全机构, 以防止typo的产生.

item通过创建`scrapy.item`类来申明, 其属性则为`scrapy.Field`类型, 就像ORM(别担心对ORM不清楚, 后面会有对ORM的解释).

我们以模块化item开始我们的项目, item将用来保存来自dmoz.org的数据. 假设我们想抓取该网站的名字, url, 以及描述, 我们针对这三项来创建属性. 要做这项工作, 我们要编辑`items.py`文件, 我们的项目类大概如下:

```
import scrapy

class DmozItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()
```
刚开始看起来可能有点复杂, 但是这样可以让你方便的使用其他的Scrapy组件, 因为那些组件需要知道item到底"长"什么样.


## 第一个spider

spider是用户自定义的类, 用来抓取某个域名(或一组域名)的信息.

spider定义一个供下载用的初始URL列表, 确定如果追踪链接, 确定怎么处理(分解)网页内容抽象到item.

spider类必须是`scrapy.Spider`的子类, 该子类必须包含最基本的三个属性(或方法):

+ `name`: 区别spider的标识, 取值必须**唯一**, 即不允许给不同的spider指定相同的name
+ `start_urls`: 定义一系列spider开始爬行的的URLs, 故第一个下载的页面就是这些链接. 随后的URLs则由从这些"start urls"获得的数据中产生.
+ `parse()`: 该方法会在`response`对象从一个个"start urls"成功下载后被调用, `response`对象则作为唯一的参数传给parse()方法

    该方法负责: 解析response的数据, 抽象出爬虫数据(Item对象), 以及解析出更多的后续工作中要用到的URL(Request对象).

以下为我们的第一个spider, 将代码保存到`tutorial/spiders`文件夹下的`dmoz_spider.py`文件中:
```
import scrapy

class DmozSpider(scrapy.Spider):
    name = 'dmoz';
    allowed_domains = ['dmoz.org'];
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/",
    ];

    def parse(self, response):
        filename = response.url.split('/')[-2];
        with open(filename, 'wb') as f:
            f.write(response.body)
```


### 开始爬虫

要进行爬虫, 先进入到项目的最顶层文件夹, 运行:

```
scrapy crawl dmoz # dmoz指的应该是写的scrapy.Spider子类中的name
```

命令`crawl dmoz`运行对dmoz.org的spider, 得到的结果类似于:

```
2015-04-05 16:37:17+0800 [scrapy] INFO: Scrapy 0.24.5 started (bot: tutorial)
2015-04-05 16:37:17+0800 [scrapy] INFO: Optional features available: ssl, http11
, django
2015-04-05 16:37:17+0800 [scrapy] INFO: Overridden settings: {'NEWSPIDER_MODULE'
: 'tutorial.spiders', 'SPIDER_MODULES': ['tutorial.spiders'], 'BOT_NAME': 'tutor
ial'}
2015-04-05 16:37:17+0800 [scrapy] INFO: Enabled extensions: LogStats, TelnetCons
ole, CloseSpider, WebService, CoreStats, SpiderState
2015-04-05 16:37:17+0800 [scrapy] INFO: Enabled downloader middlewares: HttpAuth
Middleware, DownloadTimeoutMiddleware, UserAgentMiddleware, RetryMiddleware, Def
aultHeadersMiddleware, MetaRefreshMiddleware, HttpCompressionMiddleware, Redirec
tMiddleware, CookiesMiddleware, ChunkedTransferMiddleware, DownloaderStats
2015-04-05 16:37:17+0800 [scrapy] INFO: Enabled spider middlewares: HttpErrorMid
dleware, OffsiteMiddleware, RefererMiddleware, UrlLengthMiddleware, DepthMiddlew
are
2015-04-05 16:37:17+0800 [scrapy] INFO: Enabled item pipelines:
2015-04-05 16:37:17+0800 [dmoz] INFO: Spider opened
2015-04-05 16:37:17+0800 [dmoz] INFO: Crawled 0 pages (at 0 pages/min), scraped
0 items (at 0 items/min)
2015-04-05 16:37:17+0800 [scrapy] DEBUG: Telnet console listening on 127.0.0.1:6
023
2015-04-05 16:37:17+0800 [scrapy] DEBUG: Web service listening on 127.0.0.1:6080

2015-04-05 16:37:18+0800 [dmoz] DEBUG: Crawled (200) <GET http://www.dmoz.org/Co
mputers/Programming/Languages/Python/Books/> (referer: None)
2015-04-05 16:37:21+0800 [dmoz] DEBUG: Crawled (200) <GET http://www.dmoz.org/Co
mputers/Programming/Languages/Python/Resources/> (referer: None)
2015-04-05 16:37:21+0800 [dmoz] INFO: Closing spider (finished)
2015-04-05 16:37:21+0800 [dmoz] INFO: Dumping Scrapy stats:
        {'downloader/request_bytes': 516,
        ...
         'start_time': datetime.datetime(2015, 4, 5, 8, 37, 17, 710000)}
2015-04-05 16:37:21+0800 [dmoz] INFO: Spider closed (finished)
```

注意那些包含`[dmoz]`的行, 这些行对应我们的spider, 可以对应每一个在`start_urls`定义的URL的一个log行. 因为这些URLs是开始URL, 没有referrer(引用源地址), 所以是`(referer: None)`.

更有趣的是, 在我们的`parse`方法的作用下, 有两个文件夹被创建, 分别是Books和Resources, 包含对应URL的内容.


#### 刚刚发生了什么?

Scrapy为每一个`start_urls`中的URL创建了一个`scrapy.Request`对象, 并且传给了parse方法(作为callback函数)

这些请求(Request)依次被调度, 被执行, 然后`scrapy.http.Response`对象作为反馈通过parse方法返回给spider


### 提取数据

#### 选择器(Selector)简介
从网页抓取数据的方法有多种. Scrapy使用的是一种基于XPath或CSS表达式的机构, 我们简称这个机构为Scrapy Selectors. 更多有关选择器的信息以及其他抓取机制见后续的Selector有关的章节.

以下为一些简单的Xpath表达式例子:

+ `/html/head/title`: 选择HTML文件中, `<head>`元素下的`<title>`元素
+ `/html/head/title/text()`: 选择上一条所说的元素中的文本内容
+ `//td`: 选择所有的`<td>`元素
+ `//div[@class="foobar"]`: 选择包含属性`class="foobar`的`div`元素

以上仅是几个简单XPath的例子, 当然, XPath功能远比这些要更强. 更多的有关XPath的资料见官方资料.

Scrapy提供了`Selector`类以及便利的缩写来避免每次需要进行选择时都进行选择器实例化(?).

选择器既可以看成是对象, 表示文档结构的节点. 因此, 第一个实例化的选择器是根节点, 或者说是整篇文档.

选择器有四个基本的方法:

+ `xpath()`: 返回一个选择器列表, 列表的每一项代表被参数(xpath表达式)所选中的节点.
+ `css()`: 返回一个选择器列表, 列表的每一项代表被参数(css表达式)所选中的节点.
+ `extract()`: 返回一个unicode字符串型的选中的数据
+ `re`: 返回一个unicode字符串, 字符串表示执行所给正则式参数所抓取的结果

#### 用Shell测试选择器

scrapy提供了内建的Scrapy Shell, 我们可以用它来测试选择器的使用. 在运行Scrapy Shell之前要保证系统安装了IPython(装了python就自带了).

要打开一个shell, 先进入到项目的顶层目录运行:

```
scrapy shell "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/"
```

**注意** 记住一定要用引号把url给括起来, 不然, 有些url可能不会正常被识别, 比如包含`&`的url.

shell的输出结果大概如下:

```
2015-04-08 15:51:00+0800 [scrapy] INFO: Scrapy 0.24.5 started (bot: tutorial)
2015-04-08 15:51:00+0800 [scrapy] INFO: Optional features available: ssl, http11
, django
2015-04-08 15:51:00+0800 [scrapy] INFO: Overridden settings: {'NEWSPIDER_MODULE'
: 'tutorial.spiders', 'SPIDER_MODULES': ['tutorial.spiders'], 'LOGSTATS_INTERVAL
': 0, 'BOT_NAME': 'tutorial'}
2015-04-08 15:51:00+0800 [scrapy] INFO: Enabled extensions: TelnetConsole, Close
Spider, WebService, CoreStats, SpiderState
2015-04-08 15:51:00+0800 [scrapy] INFO: Enabled downloader middlewares: HttpAuth
Middleware, DownloadTimeoutMiddleware, UserAgentMiddleware, RetryMiddleware, Def
aultHeadersMiddleware, MetaRefreshMiddleware, HttpCompressionMiddleware, Redirec
tMiddleware, CookiesMiddleware, ChunkedTransferMiddleware, DownloaderStats
2015-04-08 15:51:00+0800 [scrapy] INFO: Enabled spider middlewares: HttpErrorMid
dleware, OffsiteMiddleware, RefererMiddleware, UrlLengthMiddleware, DepthMiddlew
are
2015-04-08 15:51:00+0800 [scrapy] INFO: Enabled item pipelines:
2015-04-08 15:51:00+0800 [scrapy] DEBUG: Telnet console listening on 127.0.0.1:6
023
2015-04-08 15:51:00+0800 [scrapy] DEBUG: Web service listening on 127.0.0.1:6080

2015-04-08 15:51:00+0800 [dmoz] INFO: Spider opened
2015-04-08 15:51:02+0800 [dmoz] DEBUG: Crawled (200) <GET http://www.dmoz.org/Co
mputers/Programming/Languages/Python/Books/> (referer: None)
[s] Available Scrapy objects:
[s]   crawler    <scrapy.crawler.Crawler object at 0x00000000056262B0>
[s]   item       {}
[s]   request    <GET http://www.dmoz.org/Computers/Programming/Languages/Python
/Books/>
[s]   response   <200 http://www.dmoz.org/Computers/Programming/Languages/Python
/Books/>
[s]   settings   <scrapy.settings.Settings object at 0x0000000005244B70>
[s]   spider     <DmozSpider 'dmoz' at 0x5c388d0>
[s] Useful shortcuts:
[s]   shelp()           Shell help (print this help)
[s]   fetch(req_or_url) Fetch request (or URL) and update local objects
[s]   view(response)    View response in a browser
```

shell导出的结果会存储在局部的response变量中, 如果输入`response.body`, 可以看到body的内容, 同样输入`response.headers`可以看到headers的内容.

更重要的是, 输入`response.selector`, 可以得到一个选择器对象, 用这个对象可以对response进行查询, 查询还可以用一些简便方式, 比如`response.xpath`替代`response.selector.xpath()`, `response.css()`替代`response.selector.css()`.

现在就试试吧:

```
In [1]: response.xpath('//title')
Out[1]: [<Selector xpath='//title' data=u'<title>Open Directory - Computers: Progr'>]

In [2]: response.xpath('//title').extract()
Out[2]: [u'<title>Open Directory - Computers: Programming: Languages: Python: Books</title>']

In [3]: response.xpath('//title/text()')
Out[3]: [<Selector xpath='//title/text()' data=u'Open Directory - Computers: Programming:'>]

In [4]: response.xpath('//title/text()').extract()
Out[4]: [u'Open Directory - Computers: Programming: Languages: Python: Books']

In [5]: response.xpath('//title/text()').re('(\w+):')
Out[5]: [u'Computers', u'Programming', u'Languages', u'Python']
```

### 抓取数据

现在, 让我们从这些页面中抓取一些真正的信息

你可以在控制台中输入`response.body`, 然后检查源码, 分析用什么样的xpath语句. 然后分析纯HTML码很无聊, 推荐使用Firefox的扩展来做, 比如Firebug. 简单的, 用Chrome审查元素也行.

在分析源码后发现后, 发现该网页中主要的信息(上面的dmoz例子中搜索出来的信息是一条一条数目, 并链接到该书的网站)都在一个`<ul>`元素中, 并且是第二个`<ul>`元素.

于是, 我们可以使用以下代码选出在网页列表中的`<li>`元素:

```
response.xpath('//ul/li')
```

抓取书目的网页描述:

```
response.xpath('//u/li/text()').extract()
```

抓取数目的名称(书目名刚好都在`<a>`元素中):

```
response.xpath('//ul/li/a/text()').extract()
```

书目的链接网站url

```
response.xpath('//ul/li/a/@href').extract()
```

方法`.xpath()`的返回值仍然是一个选择器, 因此, 该方法可以嵌套:

```
for sel in response.xpath('//ul/li'):
    title = sel.xpath('a/text()').extract()
    link = sel.xpath('a/@href').extract()
    desc = sel.xpath('text()').extract()
    print title, link, desc
```

**注意** 更多关于选择器的嵌套, 可以查看后续的资料[Selectors][填坑]中的[Nesting selectors][填坑]以及[Working with relative Xpaths][填坑]部分

现在, 我们可以把这个写到spider里面:

```
import scrapy

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        for sel in response.xpath('//ul/li'):
            title = sel.xpath('a/text()').extract()
            link = sel.xpath('a/@href').extract()
            desc = sel.xpath('text()').extract()
            print title, link, desc
```

然后重新试试, 查看输出来的网页信息
```
scrapy crawl dmoz
```

### 使用Item对象

`Item`对象是用户自定义的python字典型数据结构, 可以对其使用标准的python字典语法来获取某一指定数值域:

```
>>> item = DmozItem()
>>> item['title'] = 'Example title'
>>> item['title']
'Example title'
```

一般来说, spider是需要把爬到的数据放到`Item`对象中返回. 所以, 要返回我们之前爬到的数据, 最后spider应该是这样子:

```
import scrapy

from tutorial.items import DmozItem

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        for sel in response.xpath('//ul/li'):
            item = DmozItem()
            item['title'] = sel.xpath('a/text()').extract()
            item['link'] = sel.xpath('a/@href').extract()
            item['desc'] = sel.xpath('text()').extract()
            yield item
```

**注意** 你可以在dirbot项目中找到一个全功能的变体(?): http://github.com/scrapy/dirbot

现在, 再运行爬虫后, 将捕获到`DmozItem`对象:

```
[dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
     {'desc': [u' - By David Mertz; Addison Wesley. Book in progress, full text, ASCII format. Asks for feedback. [author website, Gnosis Software, Inc.\n],
      'link': [u'http://gnosis.cx/TPiP/'],
      'title': [u'Text Processing in Python']}
[dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
     {'desc': [u' - By Sean McGrath; Prentice Hall PTR, 2000, ISBN 0130211192, has CD-ROM. Methods to build XML applications fast, Python tutorial, DOM and SAX, new Pyxie open source XML processing library. [Prentice Hall PTR]\n'],
      'link': [u'http://www.informit.com/store/product.aspx?isbn=0130211192'],
      'title': [u'XML Processing with Python']}
```


## 存储爬到的数据

存储爬虫数据最简单的方法是使用[Feed exports][填坑], 比如:

```
scrapy crawl dmoz -o items.json
```

运行上述语句, 将会生成一个`item.json`文件, 文件中包含所有的爬虫数据项.

对于小型的项目, json应该是够用了, 大的项目可以写一个[Item Pipeline][填坑].


## 下一步

目前, 该教程只cover到了简单的爬虫基础, 但仍然有很多特征没有讲. 查看[Scrapy at a glance][填坑]中的[What else][填坑]部分, 可以快速的浏览一些重要的信息.

然后, 建议继续跟着所给的简单例子进行更进一步的学习, 下一章是[Basic concepts][填坑].