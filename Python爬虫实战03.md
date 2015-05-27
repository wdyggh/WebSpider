###Python爬虫实战03 登录大学官网

`webspider103.py`

* URL的获取
* 模拟登录
* 提取相关信息
* 写入文件
* 完善代码

#### 1. URL的获取

首先，我学校的登录地址是: http://haksa.andong.ac.kr:8080/sugang/sso/login_sso.jsp

**这个网站只能用IE登录 而且还要装ActiveX控件，不然就抱错。**
**想问问是不是通过改 'User-Agent' 就能绕过控件？ 不然我再怎么爬也 爬不进去。**

可以看到 ID & PWD 的输入

![Python爬虫实战03_01.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战03_01.jpg)

登录后能看到 有4条 POST 的数据，
**在爬虫端 是不是也要发送4条数据才算登录成功？**

![Python爬虫实战03_02.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战03_02.jpg)

我认为真的接收ID&密码的网址 如下：

![Python爬虫实战03_03.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战03_03.jpg)

再细看发送的数据 我认为有4个

* udi
* password
* gid
* returl

其中 gid 和 returl 是固定值
![Python爬虫实战03_04.jpgs](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫实战03_04.jpg)


#### 2. 模拟登录

代码大致如下 暂时还是登录不成功 等我再学习段时间看看 有兴趣的朋友可以一起看看：

```python
__author__ = 'CQC'
# -*- coding:utf-8 -*-
 
import urllib
import urllib2
import cookielib
import re
 

class SDU:
 
    def __init__(self):
        self.user_agent = 'Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko'
        self.headers = {
            'Host' : 'haksa.andong.ac.kr:9000' ,
            # 'Host' : 'haksa.andong.ac.kr:8080' ,
            'User-Agent' : self.user_agent ,
            # 'Referer' : 'http://haksa.andong.ac.kr:9000/sso/pmi-logout-url.html?pmi-logout-url=http://gw.andong.ac.kr:9000/sso/pmi-logout.html&returl=http://haksa.andong.ac.kr:8080/sugang/sso/login.jsp',
            'Referer' : 'http://haksa.andong.ac.kr:8080/sugang/sso/login_sso.jsp',
            # 'Content-Type': 'application/x-www-form-urlencoded',
            'Content-Type': 'text/html; charset=euc-kr',
            'Connection' : 'Keep-Alive' 
            }
        # self.loginUrl = 'http://haksa.andong.ac.kr:8080/sugang/sso/login_sso.jsp'
        self.loginUrl = 'http://haksa.andong.ac.kr:9000/sso/pmi-sso-login-uid-password.html'
        # self.cookies = cookielib.CookieJar()
        self.cookies = cookielib.MozillaCookieJar()
        self.postdata = urllib.urlencode({
            'uid':'20111111',
            'password':'123456',
            'gid' : 'gid_haksa',
            'returl' : 'http://haksa.andong.ac.kr:8080/sugang/sso/login'
            
         })
        self.opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(self.cookies))
 
    def getPage(self):
        request  = urllib2.Request(
            url = self.loginUrl,
            headers = self.headers,
            data = self.postdata)
        result = self.opener.open(request)
        #打印登录内容  韩语
        # tempurl = 'https://haksa.andong.ac.kr:8081/sugang/jsp/mainFrame.jsp'
        # tempurl = 'http://eanu.andong.ac.kr:9090/eclass/std/index.jsp'
        # tempurl = 'http://eanu.andong.ac.kr:9090/eclass/std/index.htm'
        # tempurl = 'http://eanu.andong.ac.kr:9090/eclass/servlets/S_LecMstServlet'
        # resulttemp = self.opener.open(tempurl)
        print result.read().decode('EUC-KR')
        # print resulttemp.read().decode('EUC-KR')
 
sdu = SDU()
sdu.getPage()



```
