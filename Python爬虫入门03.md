###Python爬虫入门03

* 设置Headers
* Proxy（代理）的设置
* Timeout 设置
* 使用 HTTP 的 PUT 和 DELETE 方法
* 使用DebugLog

#### 1.设置Headers

有些**网站不会同意程序直接用上面的方式进行访问**，如果识别有问题，那么站点根本不会响应，所以**为了完全模拟浏览器的工作，我们需要设置一些Headers 的属性**。

首先，打开我们的浏览器，调试浏览器F12，我用的是Chrome，打开网络监听，示意如下，比如知乎，点登录之后，我们会发现登陆之后界面都变化了，出现一个新的界面，实质上这个页面包含了许许多多的内容，这些内容也不是一次性就加载完成的，实质上是执行了好多次请求，一般是**首先请求HTML文 件，然后加载JS，CSS 等等**，经过多次请求之后，网页的骨架和肌肉全了，整个网页的效果也就出来了。

![Python爬虫入门03_01.png](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫入门03_01.png)

拆分这些请求，我们只看一**第一个请求**，你可以看到，有个**Request URL**，还有**headers**，下面便是**response**，图片显示得不全，小伙伴们可以亲身实验一下。那么这个头中包含了许许多多是信息，有文件编码啦，压缩方式啦，请求的agent啦等等。

其中，**agent**就是请求的身份，如果没有写入请求身份，那么服务器不一定会响应，所以可以在headers中设置agent,例如下面的例子，这个例子只是说明了怎样设置的headers，小伙伴们看一下设置格式就好。

```python
import urllib  
import urllib2  
 
url = 'http://www.server.com/login'
user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)' 
values = {'username' : 'cqc',  'password' : 'XXXX' }  
headers = { 'User-Agent' : user_agent }  
data = urllib.urlencode(values)  
request = urllib2.Request(url, data, headers)  
response = urllib2.urlopen(request)  
page = response.read()
```

