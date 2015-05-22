###Python爬虫实战01 爬取糗事百科段子

`webspider101.py`

* 确定URL并抓取页面代码
* 提取某一页的所有段子
* 完善交互，设计面向对象模式


前面入门已经说了那么多基础知识了，下面我们做几个实战项目来挑战一下吧。那么这次为大家带来，Python爬取糗事百科的小段子的例子。

首先，糗事百科大家都听说过吧？糗友们发的搞笑的段子一抓一大把，这次我们尝试一下用爬虫把他们抓取下来。

#### 1. 确定URL并抓取页面代码

首先我们确定好页面的URL是 `http://www.qiushibaike.com/hot/page/1`，其中最后一个数字1代表页数，我们可以传入不同的值来获得某一页的段子内容。

我们初步构建如下的代码来打印页面代码内容试试看，先构造**最基本的页面抓取方式**，看看会不会成功

```python
# -*- coding:utf-8 -*-
import urllib
import urllib2
 
page = 1
url = 'http://www.qiushibaike.com/hot/page/' + str(page)
try:
    request = urllib2.Request(url)
    response = urllib2.urlopen(request)
    print response.read()
except urllib2.URLError, e:
    if hasattr(e,"code"):
        print e.code
    if hasattr(e,"reason"):
        print e.reason
```

结果运行有错

让我们加上一个headers验证试试看吧：

```python
# -*- coding:utf-8 -*-
import urllib
import urllib2
 
page = 1
url = 'http://www.qiushibaike.com/hot/page/' + str(page)
user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'
headers = { 'User-Agent' : user_agent }
try:
    request = urllib2.Request(url,headers = headers)
    response = urllib2.urlopen(request)
    print response.read()
except urllib2.URLError, e:
    if hasattr(e,"code"):
        print e.code
    if hasattr(e,"reason"):
        print e.reason
```

结果是 打印出了第一页的HTML代码，大家可以试试

#### 2. 提取某一页的所有段子

好，获取了HTML代码之后，我们开始分析怎样获取某一页的所有段子。

首先我们审查元素看一下，按浏览器的F12，截图如下

![Python爬虫实战01_01.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战01_01.jpg)

我们可以看到，每一个段子都是 `<div class=”article block untagged mb15″ id=”…”>…</div>` 包裹的内容。

现在我们想 **获取发布人，发布日期，段子内容，以及点赞的个数**。 不过另外注意的是，段子有些是带图片的，如果我们想在控制台显示图片是不现实的，所以我们直接把 **带有图片的段子给它剔除掉，只保存仅含文本的段子**。

所以我们加入如下 **正则表达式来匹配一下，用到的方法是 re.findall** 是找寻所有匹配的内容。方法的用法详情可以看前面说的正则表达式的介绍。

正则表达式匹配语句书写如下，在原来的基础上追加如下代码:

```python
content = response.read().decode('utf-8')
pattern = re.compile('<div.*?class="author.*?>.*?<a.*?</a>.*?<a.*?>(.*?)</a>.*?<div.*?class="content">(.*?)</div>.*?<span class="stats-vote.*?class="number">(.*?)</i>.*?<span class="stats-comments.*?class="number">(.*?)</i>',re.S)
items = re.findall(pattern,content)
for item in items:
    print item[0]
    print item[1]
    print item[2]
    print item[3]
```

现在正则表达式在这里稍作说明   
1）.\*? 是一个固定的搭配，.和\*代表可以匹配任意无限多个字符，加上？表示使用非贪婪模式进行匹配，也就是我们会尽可能短地做匹配，以后我们还会大量用到 .\*? 的搭配。

2）(.\*?)代表一个分组，在这个正则表达式中我们匹配了五个分组，在后面的遍历item中，item[0]就代表第一个(.\*?)所指代的内容，item[1]就代表第二个(.\*?)所指代的内容，以此类推。

3）re.S 标志代表在匹配时为点任意匹配模式，点 . 也可以代表换行符。

![Python爬虫实战01_02.JPG](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战01_02.JPG)

分别打印了发布人，发布内容，点赞数，回复数。

现在，整体的代码如下:

```python
# -*- coding:utf-8 -*-
import urllib
import urllib2
import re
 
page = 1
url = 'http://www.qiushibaike.com/hot/page/' + str(page)
user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'
headers = { 'User-Agent' : user_agent }
try:
    request = urllib2.Request(url,headers = headers)
    response = urllib2.urlopen(request)
    content = response.read().decode('utf-8')
    pattern = re.compile('<div.*?class="author.*?>.*?<a.*?</a>.*?<a.*?>(.*?)</a>.*?<div.*?class="content">(.*?)</div>.*?<span class="stats-vote.*?class="number">(.*?)</i>.*?<span class="stats-comments.*?class="number">(.*?)</i>',re.S)
    items = re.findall(pattern,content)
    for item in items:
        print item[0]   #作者
        print item[1]   #内容
        print item[2]   #赞
        print item[3]   #回复
except urllib2.URLError, e:
    if hasattr(e,"code"):
        print e.code
    if hasattr(e,"reason"):
        print e.reason
```

#### 3. 完善交互，设计面向对象模式

好啦，现在最核心的部分我们已经完成啦，剩下的就是修一下边边角角的东西，我们想达到的目的是：

* 按下回车，读取一个段子
* 显示出段子的发布人
* 发布日期
* 内容
* 点赞个数

另外我们需要设计**面向对象模式**，引入**类和方法**，将代码做一下优化和封装，最后，我们的代码如下所示:

```python
__author__ = 'CQC'
# -*- coding:utf-8 -*-


import urllib
import urllib2
import re
 
#百度贴吧爬虫类
class QSBK:
 
    #初始化，传入基地址，是否只看楼主的参数
    def __init__(self):
        self.pageIndex = 1
        self.user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'
        #初始化headers
        self.headers = { 'User-Agent' : self.user_agent }
        self.stories = []   #存放段子的变量，每一个元素是每一页的段子们
        #存放程序是否继续运行的变量
        #self.enable = True
        self.enable = False
 
    #传入页码，获取该页帖子的代码
    def getPage(self,pageIndex):
        try:
            url = 'http://www.qiushibaike.com/hot/page/' + str(pageIndex)
            #构建请求的request
            request = urllib2.Request(url,headers = self.headers)
            #利用urlopen获取页面代码
            response = urllib2.urlopen(request)
            #将页面转化为UTF-8编码
            pageCode = response.read().decode('utf-8')
            return pageCode
        except urllib2.URLError, e:
            if hasattr(e,"reason"):
                print u"连接糗事百科失败,错误原因",e.reason
                return None
 
    def getPageItems(self,pageIndex):
        pageCode = self.getPage(pageIndex)
        if not pageCode:
            print u"页面加载错误"
            return None
        pattern = re.compile('<div.*?class="author.*?>.*?<a.*?</a>.*?<a.*?>(.*?)</a>.*?<div.*?class="content">(.*?)</div>.*?<span class="stats-vote.*?class="number">(.*?)</i>.*?<span class="stats-comments.*?class="number">(.*?)</i>',re.S)
        items = re.findall(pattern,pageCode)
        pageStories = []
        # for item in items:
        #     print item[0]
        #     print item[1]
        #     print item[2]
        #     print item[3]
        for item in items:
            #print item[0],item[1],item[2],item[3],item[4]
            pageStories.append([item[0].strip(),item[1].strip(),item[2].strip(),item[3].strip()])
        return pageStories
 
    def loadPage(self):
        if self.enable == True:
            if len(self.stories) < 2:
                pageStories = self.getPageItems(self.pageIndex)
                if pageStories:
                    self.stories.append(pageStories)
                    self.pageIndex +=1
                    # print u"已经加载新一页"
 
    def getOneStory(self,pageStories,page):
        for story in pageStories:
            input = raw_input()
            self.loadPage()
            # print u"def loadPage is ok"
            if input == "Q":
                self.enable = False
                return
            print u"第%d页\t发布人:%s\t\n内容:%s\n赞%s\t回复数:%s\n" %(page,story[0],story[1],story[2],story[3])
 
    #开始方法
    def start(self):
        print u"正在读取糗事百科,按回车查看新段子，Q退出"
        #使变量为True，程序可以正常运行
        self.enable = True
        #先加载一页内容
        self.loadPage()
        print u"def loadPage is ok"
        #局部变量，控制当前读到了第几页
        nowPage = 0
        while self.enable:
            if len(self.stories)>0:
                #从全局list中获取一页的段子
                pageStories = self.stories[0]
                #当前读到的页数加一
                nowPage += 1
                #将全局list中第一个元素删除，因为已经取出
                del self.stories[0]
                #输出该页的段子
                self.getOneStory(pageStories,nowPage)
    
spider = QSBK()
#spider.loadPage()
#spider.getPageItems(2)
spider.start()
```

![Python爬虫实战01_03.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战01_03.jpg)

点一下回车会输出一个段子，包括发布人，发布时间，段子内容以及点赞数，是不是感觉爽爆了！(有时会出有时不会出)TT

