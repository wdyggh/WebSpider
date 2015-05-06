###Python爬虫入门01

* 秒扒网页
* 分析扒网页的方法
* 构造Request
* POST和GET数据传送

#### 1. 秒扒网页

怎样扒网页呢？其实就是根据URL来获取它的网页信息，虽然我们在浏览器中看到的是一幅幅优美的画面，但是其实是由浏览器解释才呈现出来的，实质它 是一段`HTML`代码，加 `JS`、`CSS`，如果把网页比作一个人，那么**HTML便是他的骨架，JS便是他的肌肉，CSS便是它的衣服**。所以最重要的部分是存在于HTML中的，下面我 们就写个例子来扒一个网页下来。

```bash
import urllib2
 
response = urllib2.urlopen("http://www.baidu.com")
print response.read()
```

运行代码

```bash
# python webspider01.py
```

#### 2. 分析扒网页的方法

```bash
response = urllib2.urlopen("http://www.baidu.com")
```

```bash
urlopen(url, data, timeout)
```

第一个参数url即为URL，第二个参数data是访问URL时要传送的数据，第三个timeout是设置超时时间。
第二三个参数是可以不传送的，data默认为空None，timeout默认为 socket._GLOBAL_DEFAULT_TIMEOUT.
第一个参数URL是必须要传送的，在这个例子里面我们传送了百度的URL，执行urlopen方法之后，返回一个response对象，返回信息便保存在这里面。

```bash
print response.read()
```

response对象有一个read方法，可以返回获取到的网页内容。
**如果不加read() 代码出错 就得不到我们要的返回值**

#### 3. 构造Request

将`urlopen(url, data, timeout)`中 `url` 参数传入一个Request请求，其实就是一个Request类的实例。

```bash
import urllib2
 
request = urllib2.Request("http://www.baidu.com")
response = urllib2.urlopen(request)
print response.read()
```

运行结果是完全一样的，只不过中间多了一个request对象，*推荐大家这么写，因为在构建请求时还需要加入好多内容，通过构建一个request，服务器响应请求得到应答，这样显得逻辑上清晰明确。*
