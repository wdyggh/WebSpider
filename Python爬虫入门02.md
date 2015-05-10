###Python爬虫入门02 Urllib库的基本使用

`webspider01.py`

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

#### 4. POST和GET数据传送

上面的程序演示了最基本的网页抓取，不过，现在大多数网站都是`动态网页`，需要你动态地传递参数给它，它做出对应的响应。所以，在访问时，我们需要传递数据给它。最常见的情况是什么？对了，就是`登录注册`的时候呀。
数据传送分为`POST`和`GET`两种方式,最重要的区别是**GET方式是直接以链接形式访问，链接中包含了所有的参数**，当然如果包含了密码的话是一种不安全的选择，不过你可以直观地看到自己提交了什么内容。**POST则不会在网址上显示所有的参数**,不过如果你想直接查看提交了什么就不太方便了，大家可以酌情选择。

#####POST方式：

```python
import urllib
import urllib2
 
values = {"username":"XXXXX","password":"XXXXX"}
data = urllib.urlencode(values) 
url = "https://passport.csdn.net/account/login?from=http://my.csdn.net/my/mycsdn"
request = urllib2.Request(url,data)
response = urllib2.urlopen(request)
print response.read()
```

我们引入了urllib库，现在我们模拟登陆CSDN，当然上述代码可能登陆不进去，因为还要做一些设置头部header的工作，或者还有一些参数 没有设置全，还没有提及到在此就不写上去了，在此只是说明登录的原理。
在此可以看到我们定义了1个字典，名字为values，参数设置了username 和 password
然后利用`urllib.urlencode()`将字典编码，命名为data，在`urllib2.Request()`中传入 URL 和 data 参数。运行就可以实现登录。`response.read()`的储存内容就是登录后出现的页面内容。

> 如下的字典定义方法也可成功。

```python
import urllib
import urllib2
 
values = {}
values['username'] = "1016903103@qq.com"
values['password'] = "XXXX"
data = urllib.urlencode(values) 
url = "http://passport.csdn.net/account/login?from=http://my.csdn.net/my/mycsdn"
request = urllib2.Request(url,data)
response = urllib2.urlopen(request)
print response.read()
```

####GET方式：

`GET`的使用方法是：把参数写到网址上面，直接构建一个带参数的URL出来即可。

```python
import urllib
import urllib2
 
values={}
values['username'] = "1016903103@qq.com"
values['password']="XXXX"
data = urllib.urlencode(values) 
url = "http://passport.csdn.net/account/login"
geturl = url + "?"+data
request = urllib2.Request(geturl)
response = urllib2.urlopen(request)
print response.read()
```

可以用`print geturl`来查看`geturl`加编码的参数


