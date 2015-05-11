###Python爬虫入门05 Cookie的使用

`webspider04.py`

* Opener
* Cookielib

上一节我们研究了一下爬虫的异常处理问题，那么接下来我们一起来看一下Cookie的使用。    
Cookie，**指某些网站为了辨别用户身份、进行session跟踪而储存在用户本地终端上的数据（通常经过加密）**     

比如说有些网站需要登录后才能访问某个页面，在登录之前，你想抓取某个页面内容是不允许的。那么我们可以利用Urllib2库保存我们登录的Cookie，然后再抓取其他页面就达到目的了。   

在此之前呢，我们必须先介绍一个opener的概念。

#### 1.Opener

之前获取一个URL都是使用一个opener(一个urllib2.OpenerDirector的实例)。我们都是使用的默认的opener，也就是`urlopen`。它是一个特殊的opener，可以理解成opener的一个特殊实例，传入的参数仅仅是`url，data，timeout`。

如果我们需要用到`Cookie`，只用这个`opener(urlopen)`是不能达到目的的，所以我们需要创建更一般的opener来实现对Cookie的设置。

#### 2. Cookielib

cookielib模块的主要作用是提供可储存cookie的对象，以便于和urllib2模块配合使用来访问Internet资源。Cookielib模块非常强大，可以利用模块中CookieJar类的对象来捕获cookie并在后续连接请求时重新发送，比如可以实现模拟登录功能。     
Cookielib模块的主要对象有 CookieJar FileCookieJar MozillaCookieJar LWPCookieJar 。     

他们之间的关系：CookieJar —-派生—->FileCookieJar —-派生—–>MozillaCookieJar 和 LWPCookieJar

##### 1）获取Cookie保存到变量

首先，我们先利用CookieJar对象实现获取cookie的功能，存储到变量中，先来感受一下

```python

import urllib2
import cookielib

#声明一个CookieJar对象实例来保存cookie
cookie = cookielib.CookieJar()
#利用urllib2库的HTTPCookieProcessor对象来创建cookie处理器
handler=urllib2.HTTPCookieProcessor(cookie)
#通过handler来构建opener
opener = urllib2.build_opener(handler)
#此处的open方法同urllib2的urlopen方法，也可以传入request
response = opener.open('http://www.baidu.com')

for item in cookie:
    print 'Name = '+item.name
    print 'Value = '+item.value

```

使用以上方法将cookie保存到变量中，然后打印出了cookie中的值，运行结果如下:

![Python爬虫入门05_01.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫入门05_01.jpg)

##### 2）保存Cookie到文件

在上面的方法中，我们将cookie保存到了cookie这个变量中，如果我们想将cookie保存到文件中该怎么做呢？这时，我们就要用到`FileCookieJar`这个对象了，在这里我们使用它的子类`MozillaCookieJar`来实现Cookie的保存

```python
import cookielib
import urllib2
 
#设置保存cookie的文件，同级目录下的cookie.txt
filename = 'cookie.txt'
#声明一个MozillaCookieJar对象实例来保存cookie，之后写入文件
cookie = cookielib.MozillaCookieJar(filename)
#利用urllib2库的HTTPCookieProcessor对象来创建cookie处理器
handler = urllib2.HTTPCookieProcessor(cookie)
#通过handler来构建opener
opener = urllib2.build_opener(handler)
#创建一个请求，原理同urllib2的urlopen
response = opener.open("http://www.baidu.com")
#保存cookie到文件
cookie.save(ignore_discard=True, ignore_expires=True)
```

关于最后save方法的两个参数在此说明一下：

官方解释如下：

*ignore_discard: save even cookies set to be discarded.*     
*ignore_expires: save even cookies that have expiredThe file is overwritten if it already exists*

![Python爬虫入门05_02.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫入门05_02.jpg)

##### 3）从文件中获取Cookie并访问

已经做到把Cookie保存到文件中了，如果以后想使用，可以利用下面的方法来读取cookie并访问网站，感受一下

```python
import cookielib
import urllib2
 
#创建MozillaCookieJar实例对象
cookie = cookielib.MozillaCookieJar()

#从文件中读取cookie内容到变量
cookie.load('cookie.txt', ignore_discard=True, ignore_expires=True)

#创建请求的request
req = urllib2.Request("http://www.baidu.com")

#利用urllib2的build_opener方法创建一个opener
opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cookie))

response = opener.open(req)
print response.read()
```

设想，如果我们的 cookie.txt 文件中保存的是某个人登录百度的cookie，那么我们提取出这个cookie文件内容，就可以用以上方法模拟这个人的账号登录百度。


##### 4）利用cookie模拟网站登录

下面我们以/' *我们学校的教育系统为例* /'，利用cookie实现模拟登录，并将cookie信息保存到文本文件中，来感受一下cookie大法吧！

```python
import urllib
import urllib2
import cookielib
 
filename = 'cookie.txt'
#声明一个MozillaCookieJar对象实例来保存cookie，之后写入文件
cookie = cookielib.MozillaCookieJar(filename)
opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cookie))
postdata = urllib.urlencode({
            'stuid':'123456',
            'pwd':'123456'
        })
#登录教务系统的URL
loginUrl = 'http://jwxt.sdu.edu.cn:7890/pls/wwwbks/bks_login2.login'
#模拟登录，并把cookie保存到变量
result = opener.open(loginUrl,postdata)
#保存cookie到cookie.txt中
cookie.save(ignore_discard=True, ignore_expires=True)
#利用cookie请求访问另一个网址，此网址是成绩查询网址
gradeUrl = 'http://jwxt.sdu.edu.cn:7890/pls/wwwbks/bkscjcx.curscopre'
#请求访问成绩查询网址
result = opener.open(gradeUrl)
print result.read()
```

程序的原理如下:      
1. 创建一个带有cookie的opener          
2. 在访问登录的URL时，将登录后的cookie保存下来       
3. 利用这个cookie来访问其他网址。      
4. 登录之后才能查看的成绩查询呀，本学期课表呀等等网址     
模拟登录就这么实现啦，是不是很酷炫？          



