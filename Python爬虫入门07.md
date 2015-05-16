###Python爬虫入门06 Beautiful Soup的用法

`webspider06.py`

* Beautiful Soup的简介
* Beautiful Soup 安装
* 开启Beautiful Soup 之旅
* 创建 Beautiful Soup 对象
* 四大对象种类
* 遍历文档树
* 搜索文档树
* CSS选择器
* 总结

上一节介绍了正则表达式，它的内容其实还是蛮多的，如果一个正则匹配稍有差池，那可能程序就处在永久的循环之中，而且有的小伙伴们也对写正则表 达式的写法用得不熟练，没关系，我们还有一个更强大的工具，叫 `Beautiful Soup`，有了它我们可以很方便地提取出HTML或XML标签中的内容，实在是方便，这一节就让我们一起来感受一下Beautiful Soup的强大吧。

#### 1. Beautiful Soup的简介

简单来说，`Beautiful Soup` 是python的一个库，最主要的功能是从网页抓取数据。官方解释如下：

*Beautiful Soup提供一些简单的、python式的函数用来处理导航、搜索、修改分析树等功能。它是一个工具箱，通过解析文档为用户提供需要抓取的数据，因为简单，所以不需要多少代码就可以写出一个完整的应用程序。*

Beautiful Soup自动将**输入文档转换为Unicode编码**，**输出文档转换为utf-8编码**。你不需要考虑编码方式，除非文档没有指定一个编码方式，这时，Beautiful Soup就不能自动识别编码方式了。然后，你仅仅需要说明一下原始编码方式就可以了。

**Beautiful Soup**已成为和**lxml、html6lib一样出色的python解释器**，为用户灵活地提供不同的解析策略或强劲的速度。

#### 2. Beautiful Soup 安装

Beautiful Soup 3 目前已经停止开发，推荐在现在的项目中使用**Beautiful Soup 4**，不过它已经被移植到BS4了，也就是说导入时我们需要 `import bs4` 。所以这里我们用的版本是 `Beautiful Soup 4.3.2 (简称BS4)`，另外据说 BS4 对 Python3 的支持不够好，不过我用的是 `Python2.7.7`，如果有小伙伴用的是 Python3 版本，可以考虑下载 BS3 版本。

* Debian | Ubuntu 下安装

	```bash
	sudo apt-get install python-bs4
	```

	这里有[最新版4.3.2](https://pypi.python.org/pypi/beautifulsoup4/4.3.2)

	下载完成之后解压，运行下面的命令即可完成安装

	```bash
	sudo python setup.py install 
	```

	然后需要安装 lxml

	```bash
	sudo apt-get install python-lxml
	```

	Beautiful Soup支持Python标准库中的HTML解析器,还支持一些第三方的解析器，如果我们不安装它，则 Python 会使用 Python默认的解析器，lxml 解析器更加强大，速度更快，推荐安装。

#### 3. 开启Beautiful Soup 之旅

[官方文档](http://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html)

#### 4. 创建 Beautiful Soup 对象

首先导入 bs4 库

```python
form bs4 inport BeautifulSoup
```

* 创建 beautifulsoup 对象

```html
<html>
 <head>
  <title>
   The Dormouse's story
  </title>
 </head>
 <body>
  <p class="title" name="dromouse">
   <b>
    The Dormouse's story
   </b>
  </p>
  <p class="story">
   Once upon a time there were three little sisters; and their names were
   <a class="sister" href="http://example.com/elsie" id="link1">
    <!-- Elsie -->
   </a>
   ,
   <a class="sister" href="http://example.com/lacie" id="link2">
    Lacie
   </a>
   and
   <a class="sister" href="http://example.com/tillie" id="link3">
    Tillie
   </a>
   ;
and they lived at the bottom of a well.
  </p>
  <p class="story">
   ...
  </p>
 </body>
</html>
```

```python
soup = BeautifulSoup(html)
```

* 打开本地 HTML 文件来创建对象

```python 
soup = BeautifulSoup(open('index.html'))
# 加载本地 index.html文件 用它来创建 soup 对象
print soup.prettify()
#打印soup对象内容，格式化输出
```

`soup.prettify()`格式化打印它的内容

![Python爬虫入门07_01.JPG](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫入门07_01.JPG)       

#### 5. 四大对象种类

Beautiful Soup将复杂HTML文档转换成一个复杂的树形结构,每个节点都是Python对象,所有对象可以归纳为4种:

* Tag
* NavigableString
* BeautifulSoup
* Comment

##### 1) Tag

Tag 通俗点讲就是 HTML 中的一个个标签，例如

```html
<title>The Dormouse's story</title>

<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
```

上面的 `title` `a` 等等 HTML 标签加上里面包括的内容就是 Tag，下面我们来感受一下怎样用 Beautiful Soup 来方便地获取 Tags

下面每一段代码中注释部分即为运行结果

```python
print soup.title
#<title>The Dormouse's story</title>

print soup.head
#<head><title>The Dormouse's story</title></head>

print soup.a
#<a class="sister" href="http://example.com/elsie" id="link1"><!-- Elsie --></a>

print soup.p
#<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
```

`print soup.head` 运行结果如下：    
![Python爬虫入门07_02.jpg](http://7xifyp.com1.z0.glb.clouddn.com/Python爬虫入门07_02.jpg)         

验证一下这些对象的类型：

```python
print type(soup.a)
#<class 'bs4.element.Tag'>
```

对于 Tag，它有两个重要的属性，是 `name` 和 `attrs`，下面我们分别来感受一下

**name**

soup 对象本身比较特殊，它的 name 即为 [document]，对于其他内部标签，输出的值便为标签本身的名称。

```python
print soup.name
print soup.head.name
#[document]
#head
```

**attrs**

p 标签的所有属性打印输出了出来，得到的类型是一个字典。

```python
print soup.p.attrs
#{'class': ['title'], 'name': 'dromouse'}
```

想要单独获取某个属性，可以这样，例如我们获取它的 class 叫什么

```python
print soup.p['class']
#['title']
```

想要单独获取某个属性，还可以这样，利用get方法，传入属性的名称，二者是等价的

```python
print soup.p.get('class')
#['title']
```

我们可以对这些属性和内容等等进行**修改**，例如

```python
soup.p['class']="newClass"
print soup.p
#<p class="newClass" name="dromouse"><b>The Dormouse's story</b></p>
```

还可以对这个属性进行**删除**，例如

```python
del soup.p['class']
print soup.p
#<p name="dromouse"><b>The Dormouse's story</b></p>
```

不过，对于修改删除的操作，不是我们的主要用途，在此不做详细介绍了，如果有需要，请查看前面提供的官方文档

##### 2) NavigableString

已经得到了标签的内容，要想获取标签内部的文字怎么办呢？很简单，用 .string 即可，例如

```python
print soup.p.string
#The Dormouse's story
```

这样我们就轻松获取到了标签里面的内容，想想如果用正则表达式要多麻烦。它的类型是一个 NavigableString，翻译过来叫 可以遍历的字符串。

```python
print type(soup.p.string)
#<class 'bs4.element.NavigableString'>
```

##### 3) BeautifulSoup

BeautifulSoup 对象表示的是一个文档的全部内容,大部分时候,可以把它当作 Tag 对象，是一个特殊的 Tag，我们可以分别获取它的类型，名称，以及属性来感受一下

```python
print type(soup.name)
#<type 'unicode'>
print soup.name 
# [document]
print soup.attrs 
#{} 空字典
```

##### 4) Comment

Comment 对象是一个特殊类型的 NavigableString 对象，其实输出的内容仍然不包括注释符号，但是如果不好好处理它，可能会对我们的文本处理造成意想不到的麻烦。

我们找一个带注释的标签

```python
print soup.a
print soup.a.string
print type(soup.a.string)
```

运行结果如下：

```html
<a class="sister" href="http://example.com/elsie" id="link1"><!-- Elsie --></a>
 Elsie 
<class 'bs4.element.Comment'>
```

`a` 标签里的内容实际上是注释，     
用 .string 来输出它的内容，我们发现它已经把注释符号去掉了    

另外我们打印输出下它的类型，发现它是一个 `Comment ` 类型，所以，我们在使用前 **最好做一下判断，判断代码如下**        

```python
if type(soup.a.string)==bs4.element.Comment:
    print soup.a.string
```

上面的代码中，我们首先判断了它的类型，是否为 `Comment` 类型，然后再进行其他操作，如打印输出。

#### 6. 遍历文档树

##### 1) 直接子节点

**要点： .contents .children**

**.contents**

`tag` 的 `.content` 属性可以将
`tag`的子节点以列表的方式输出

```python
print soup.head.contents
#[<title>The Dormouse's story</title>]
```

输出方式为**列表**，我们可以用列表索引来获取它的某一个元素

```python
print soup.head.contents
#[<title>The Dormouse's story</title>]
```

怎样获得里面的内容呢？很简单，遍历一下就好了，代码及结果如下

```python
for child in soup.body.children:
    print child
```

```html
<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
 
<p class="story">Once upon a time there were three little sisters; and their names were
<a class="sister" href="http://example.com/elsie" id="link1"><!-- Elsie --></a>,
<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
<a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>
 
<p class="story">...</p>
```

##### 2) 所有子孙节点




#### 7. 搜索文档树
#### 8. CSS选择器
#### 9. 总结