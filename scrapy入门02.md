###scrapy入门02 scrapy入门介绍

* 创建一个Scrapy项目
* 定义提取的Item
* 编写爬取网站的 spider 并提取 Item
* 编写 Item Pipeline 来存储提取到的Item(即数据)

#### 1. 创建项目

在开始爬取之前，您必须创建一个新的Scrapy项目。进入您打算存储代码的目录中，运行下列命令:

```bash
scrapy startproject tutorial
```

该命令将会创建包含下列内容的 tutorial 目录:

```bash
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

这些文件分别是:

* scrapy.cfg: 项目的配置文件
* tutorial/: 该项目的python模块。之后您将在此加入代码。
* tutorial/items.py: 项目中的item文件.
* tutorial/pipelines.py: 项目中的pipelines文件.
* tutorial/settings.py: 项目的设置文件.
* tutorial/spiders/: 放置spider代码的目录.

#### 2. 定义Item

Item是保存爬取到的数据的容器，使用方法和python字典类似，并提供了额外保护机制来避免拼写错误导致的未定义字段错误。  
类似在ORM中做的一样，可以通过穿件一个 `scrapy.Item` 类，并且定义类型为 `scrapy.Filed` 的类属性来定义一个Item。(如果不了解ORM, 不用担心，您会发现这个步骤非常简单)(我不了解。。)  

首先根据需要从dmoz.org获取到的数据对item进行建模。 我们需要从dmoz中获取名字，url，以及网站的描述。 对此，在item中定义相应的字段。编辑 `tutorial` 目录中的 `items.py` 文件:

```python
import scrapy

class DmozItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()
```

一开始这看起来可能有点复杂，但是通过定义item， 您可以很方便的使用Scrapy的其他方法。而这些方法需要知道您的item的定义。

#### 3. 编写第一个爬虫(Spider)

Spider是用户编写用于从单个网站(或者一些网站)爬取数据的类。

其包含了一个用于下载的初始URL，如何跟进网页中的链接以及如何分析页面中的内容， 提取生成 item 的方法。

为了创建一个Spider，您必须继承 scrapy.Spider 类， 且定义以下三个属性:

* name: 用于区别Spider。该名字必须是唯一的，您不可以为不同的Spider设定相同的名字。
* start_urls: 包含了Spider在启动时进行爬取的url列表。 因此，第一个被获取到的页面将是其中之一。 后续的URL则从初始的URL获取到的数据中提取。
* parse() 是spider的一个方法。 被调用时，每个初始URL完成下载后生成的 Response 对象将会作为唯一的参数传递给该函数。 该方法负责解析返回的数据(response data)，提取数据(生成item)以及生成需要进一步处理的URL的 Request 对象。

以下为我们的第一个Spider代码，保存在 tutorial/spiders 目录下的 dmoz_spider.py 文件中:

```python
# -*- coding: utf-8 -*-


import scrapy

class DmozSpider(scrapy.spider.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = ["http://www.dmoz.org/Computers/Programming/Languages/Python/Books/","http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self,response):
        filename = response.url,split("/")[-2]
        with open(filename,"wb") as f:
            f.wirte(response.body)
```

#### 4. 爬取

进入项目的根目录，执行下列命令启动spider:

```bash
scrapy crawl dmoz
```

`crawl dmoz` 启动用于爬取 `dmoz.org` 的spider，您将得到类似的输出:

![scrapy入门02_01.jpg](http://7xifyp.com1.z0.glb.clouddn.com/scrapy入门02_01.jpg)

查看包含 `[dmoz]` 的输出，可以看到输出的log中包含定义在 `start_urls` 的初始URL，并且与spider中是一一对应的。在log中可以看到其没有指向其他页面( `(referer:None)` )。

除此之外，更有趣的事情发生了。就像我们 `parse` 方法指定的那样，有两个包含url所对应的内容的文件被创建了: *Book , Resources 。*

**刚才发生了什么？**

Scrapy为Spider的 `start_urls` 属性中的每个URL创建了 `scrapy.Request `对象，并将 `parse` 方法作为回调函数(callback)赋值给了Request。

Request对象经过调度，执行生成 `scrapy.http.Response` 对象并送回给spider `parse()` 方法。

|xx|xx|xx|
|:----:|:-----:|:------:|
|dmoz_spider.py|parse()|start_urls |
|items.py|DmozItem()|title  link  desc  |

#### 5. 提取Item

##### Selectors选择器简介

从网页中提取数据有很多方法。Scrapy使用了一种基于 `XPath` 和 `CSS` 表达式机制: `Scrapy Selectors `。 关于selector和其他提取机制的信息请参考 Selector文档 。

这里给出XPath表达式的例子及对应的含义:

* /html/head/title: 选择HTML文档中 `<head>` 标签内的 `<title>` 元素
* /html/head/title/text(): 选择上面提到的 `<title>` 元素的文字
* //td: 选择所有的 `<td>` 元素
* //div[@class="mine"]: 选择所有具有 class="mine" 属性的 div 元素

上边仅仅是几个简单的XPath例子，XPath实际上要比这远远强大的多。 如果您想了解的更多，我们推荐 这篇XPath教程 。

为了配合XPath，Scrapy除了提供了 Selector 之外，还提供了方法来避免每次从response中提取数据时生成selector的麻烦。

Selector有四个基本的方法(点击相应的方法可以看到详细的API文档):  
* xpath(): 传入xpath表达式，返回该表达式所对应的所有节点的selector list列表 。  
* css(): 传入CSS表达式，返回该表达式所对应的所有节点的selector list列表.  
* extract(): 序列化该节点为unicode字符串并返回list。  
* re(): 根据传入的正则表达式对数据进行提取，返回unicode字符串list列表。  

##### 在Shell中尝试Selector选择器

为了介绍Selector的使用方法，接下来我们将要使用内置的 Scrapy shell 。Scrapy Shell需要您预装好IPython(一个扩展的Python终端)。

您需要进入项目的根目录，执行下列命令来启动shell:

```bash
scrapy shell "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/"
```

shell的输出类似:

![scrapy入门02_02.jpg](http://7xifyp.com1.z0.glb.clouddn.com/scrapy入门02_02.jpg)

当shell载入后，您将得到一个包含response数据的本地 response 变量。输入 response.body 将输出response的包体， 输出 response.headers 可以看到response的包头

更为重要的是，当输入 response.selector 时， 您将获取到一个可以用于查询返回数据的selector(选择器)， 以及映射到 response.selector.xpath() 、 response.selector.css() 的 快捷方法(shortcut): response.xpath() 和 response.css() 。

同时，shell根据response提前初始化了变量 sel 。该selector根据response的类型自动选择最合适的分析规则(XML vs HTML)。

让我们来试试:

![scrapy入门02_03.jpg](http://7xifyp.com1.z0.glb.clouddn.com/scrapy入门02_03.jpg)

##### 提取数据

现在，我们来尝试从这些页面中提取些有用的数据。

您可以在终端中输入 response.body 来观察HTML源码并确定合适的XPath表达式。不过，这任务非常无聊且不易。您可以考虑使用Firefox的Firebug扩展来使得工作更为轻松。详情请参考 使用Firebug进行爬取 和 借助Firefox来爬取 。

在查看了网页的源码后，您会发现网站的信息是被包含在 第二个 `<ul>` 元素中。

我们可以通过这段代码选择该页面中网站  
* 列表里所有 `<li>` 元素: `sel.xpath('//ul/li')`  
* 网站的描述: `sel.xpath('//ul/li/text()').extract()`  
* 网站的标题: `sel.xpath('//ul/li/a/text()').extract()`  
* 以及网站的链接: `sel.xpath('//ul/li/a/@href').extract()`  

之前提到过，每个 .xpath() 调用返回selector组成的list，因此我们可以拼接更多的 .xpath() 来进一步获取某个节点。我们将在下边使用这样的特性:

```python
for sel in response.xpath('//ul/li'):
    title = sel.xpath('a/text()').extract()
    link = sel.xpath('a/@href').extract()
    desc = sel.xpath('text()').extract()
    print title, link, desc
```

在我们的spider中加入这段代码:

```python
# -*- coding: utf-8 -*-


import scrapy

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        '''
        filename = response.url.split("/")[-2]
        with open(filename, 'wb') as f:
            f.write(response.body)
        '''
        for sel in response.xpath('//ul/li'):
            title = sel.xpath('a/text()').extract()
            link = sel.xpath('a/@href').extract()
            desc = sel.xpath('text()').extract()
            print title,link,desc
```

现在尝试再次爬取dmoz.org，您将看到爬取到的网站信息被成功输出:

```bash
scrapy crawl dmoz
```

##### 使用item

`Item` 对象是自定义的python字典。 您可以使用标准的字典语法来获取到其每个字段的值。(字段即是我们之前用Field赋值的属性):

```bash
>>> item = DmozItem()
>>> item['title'] = 'Example title'
>>> item['title']
'Example title'
```

一般来说，Spider将会将爬取到的数据以 Item 对象返回。所以为了将爬取的数据返回，我们最终的代码将是:

```python
# -*- coding: utf-8 -*-
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
        
        # filename = response.url.split("/")[-2]
        # with open(filename, 'wb') as f:
        #     f.write(response.body)
        
        # for sel in response.xpath('//ul/li'):
        #     title = sel.xpath('a/text()').extract()
        #     link = sel.xpath('a/@href').extract()
        #     desc = sel.xpath('text()').extract()
        #     print title,link,desc
        
        for sel in response.xpath('//ul/li'):
            item = DmozItem()
            item['title'] = sel.xpath('a/text()').extract()
            item['link'] = sel.xpath('a/@href').extract()
            item['desc'] = sel.xpath('text()').extract()
            yield item
```

现在对dmoz.org进行爬取将会产生 DmozItem 对象:

![scrapy入门02_04.jpg](http://7xifyp.com1.z0.glb.clouddn.com/scrapy入门02_04.jpg)

#### 6. 保存爬取到的数据

最简单存储爬取的数据的方式是使用 Feed exports:

```bash
scrapy crawl dmoz -o items.json
```

该命令将采用 JSON 格式对爬取的数据进行序列化，生成 items.json 文件。
在类似本篇教程里这样小规模的项目中，这种存储方式已经足够。 如果需要对爬取到的item做更多更为复杂的操作，您可以编写 Item Pipeline 。 类似于我们在创建项目时对Item做的，用于您编写自己的 tutorial/pipelines.py 也被创建。 不过如果您仅仅想要保存item，您不需要实现任何的pipeline。