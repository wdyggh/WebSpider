###scrapy入门01 scrapy介绍

* 网络爬虫 & Scrapy 介绍
* Scrapy 安装

[scrapy官网](http://scrapy.org/)

#### 1. 网络爬虫 & Scrapy 介绍

引用来自[Scrapy 轻松定制网络爬虫](http://blog.pluskid.org/?p=366)的一段话

*网络爬虫(Web Crawler, spider) 就是一个在网络上乱爬的机器人。当然它通常并不是一个实体的机器人，因为网络本身也是虚拟的东西，所以这个“机器人”其实也就是一段程序，并且它也不是乱爬，而是有一定目的的，并且在爬行的时候会搜集一些信息。例如 Google 就有一大堆爬虫会在 Internet 上搜集网页内容以及它们之间的链接等信息。*   

*其实爬虫从基本原理上来讲很简单，只要能访问网络和分析 Web 页面即可，现在大部分语言都有方便的 Http 客户端库可以抓取 Web 页面，而 HTML 的分析最简单的可以直接用正则表达式来做，因此要做一个最简陋的网络爬虫实际上是一件很简单的事情。不过要实现一个高质量的 spider 却是非常难的。*   

*爬虫的两部分，一是下载 Web 页面，有许多问题需要考虑，如何最大程度地利用本地带宽，如何调度针对不同站点的 Web 请求以减轻对方服务器的负担等。一个高性能的 Web Crawler 系统里，DNS 查询也会成为急需优化的瓶颈，另外，还有一些“行规”需要遵循（例如 robots.txt）。而获取了网页之后的分析过程也是非常复杂的，Internet 上的东西千奇百怪，各种错误百出的 HTML 页面都有，要想全部分析清楚几乎是不可能的事；另外，随着 AJAX 的流行，如何获取由 Javascript 动态生成的内容成了一大难题；除此之外，Internet 上还有有各种有意或无意出现的 Spider Trap ，如果盲目的跟踪超链接的话，就会陷入 Trap 中万劫不复了，例如这个网站，据说是之前 Google 宣称 Internet 上的 Unique URL 数目已经达到了 1 trillion 个，因此这个人 is proud to announce the second trillion 。*    

*今天的主角是 Scrapy ，这是一个用 Python 写的 Crawler Framework ，简单轻巧，并且非常方便，并且官网上说已经在实际生产中在使用了，因此并不是一个玩具级别的东西。*

Scrapy 使用 Twisted 这个异步网络库来处理网络通讯，架构清晰，并且包含了各种中间件接口，可以灵活的完成各种需求。整体架构如下图所示：   

![scrapy入门01_01.png](http://7xifyp.com1.z0.glb.clouddn.com/scrapy入门01_01.png)

Scrapy主要包括了以下组件：   
1. 引擎-ScrapyEngine，用来处理整个系统的数据流处理，出发事务。  
2. 调度器-Scheduler，用来接受引擎发过来的请求，压入队列中，并在引擎再次请求的时候返回。  
3. 下载器-Downloader，用于下载网页内容，并将网页内容返回给蜘蛛。  
4. 蜘蛛-Spider，蜘蛛是主要干活的，用它来制定特定域名或网页的解析规则。  
5. 项目管道-ItemPipeline，负责处理有蜘蛛从网页中抽取的项目，它的主要任务是清晰、验证和存储数据。当页面被蜘蛛解析后，将被发送到项目管道，并经过几个特定的次序处理数据。  
6. 下载器中间件-DownloaderMiddlewares，位于Scrapy引擎和下载器之间的钩子框架，主要是处理Scrapy引擎与下载器之间的请求和响应。  
7. 蜘蛛中间件-SpiderMiddlewares，位于Scrapy引擎和蜘蛛之间的钩子框架，主要是处理蜘蛛的响应输入和请求输出。  
8. 调度中间件-SchedulerMiddlewares，位于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应。  

上图中绿色是数据流向，首先从初始URL开始，Scheduler会将其交给Downloader进行下载，下载后交给Spider分析，分析出来的结果有2种：一种是需要进一步抓取的链接，例如之前分析的“下一页”的链接，这些东西会被传回Scheduler；另一种是需要保存的数据，它们则被送到ItemPipeline那里，那是对数据进行后期处理(详细分析、过滤、储存等)的地方。

#### 2. Scrapy 安装

平台 :  
* Debian  
* Python 2.7  

```bash
sudo apt-get install libffi-dev
sudo apt-get install libxml2-dev
sudo apt-get install libxslt1-dev
sudo apt-get install python-dev
sudo pip install scrapy
```

