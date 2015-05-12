###Python爬虫入门06 正则表达式

`webspider05.py`

* 正则表达式的概念
* 正则表达式的语法规则
* 正则表达式的相关注解
* Python Re模块
* Python Re模块的另一种使用方式



#### 1. 正则表达式的概念

*正则表达式是对字符串操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。*

正则表达式是用来匹配字符串非常强大的工具，在其他编程语言中同样有正则表达式的概念，Python也不例外，利用正则表达式，我们想要从返回的页面里提取想要的内容就变得很简单。

正则表达式的大致匹配过程是：     
1. 依次让表达式和文本中的字符比较     
2. 如果每个字符都能匹配，则匹配成功；有一个字符不匹配，则匹配失败     
3. 如果表达式中有量词或边界，这个过程会稍微有一些不同     

#### 2. 正则表达式的语法规则

下面是Python正则表达式的一些匹配规则，图片来自CSDN     

![Python正则表达式的一些匹配规则](http://qiniu.cuiqingcai.com/wp-content/uploads/2015/02/20130515113723855.png)

#### 3. 正则表达式相关注解

* 数量词的贪婪模式 & 非贪婪模式    
		正则表达式通常用于文本中查找匹配的字符串。Python里数量词默认是贪婪的（在少数语言里也可能默认非贪婪），总是尝试匹配尽可能多的字符；非贪婪的则相反，总是尝试匹配尽可能少的字符。例如：正则表达式”ab*”如果用于查找”abbbc”，将找到”abbb”。而如果使用非贪婪的数量 词”ab*?”，将找到”a”。      
		注：我们一般使用非贪婪模式来提取。     

* 反斜杠问题    
		与大多数编程语言相同，正则表达式里使用”\”作为转义字符，这就可能造成反斜杠困扰。假如你需要匹配文本中的字符”\”，那么使用编程语言表示的正则表达式里将需要4个反 斜杠”\\\\”：前两个和后两个分别用于在编程语言里转义成反斜杠，转换成两个反斜杠后再在正则表达式里转义成一个反斜杠。    
		Python里的原生字符串很好地解决了这个问题，这个例子中的正则表达式可以使用r”\\”表示。同样，匹配一个数字的”\\d”可以写成r”\d”。有了原生字符串，妈妈也不用担心是不是漏写了反斜杠，写出来的表达式也更直观勒。      

#### 4. Python Re模块

Python 自带了re模块，它提供了对正则表达式的支持。主要用到的方法列举如下

```python
#返回pattern对象
re.compile(string[,flag])  
#以下为匹配所用函数
re.match(pattern, string[, flags])
re.search(pattern, string[, flags])
re.split(pattern, string[, maxsplit])
re.findall(pattern, string[, flags])
re.finditer(pattern, string[, flags])
re.sub(pattern, repl, string[, count])
re.subn(pattern, repl, string[, count])
```

在介绍这几个方法之前，我们先来介绍一下pattern的概念，pattern可以理解为一个匹配模式，那么我们怎么获得这个匹配模式呢？很简单，我们需要利用re.compile方法就可以。例如

```python
pattern = re.compile(r'hello')
```

在参数中我们传入了原生字符串对象，通过compile方法编译生成一个pattern对象，然后我们利用这个对象来进行进一步的匹配。

大家可能注意到了另一个参数 flags，在这里解释一下这个参数的含义：

参数flag是匹配模式，取值可以使用按位或运算符’|’表示同时生效，比如re.I | re.M 可选值有：

> re.I(全拼：IGNORECASE): 忽略大小写（括号内是完整写法，下同）    
> re.M(全拼：MULTILINE): 多行模式，改变'^'和'$'的行为（参见上图）     
> re.S(全拼：DOTALL): 点任意匹配模式，改变'.'的行为     
> re.L(全拼：LOCALE): 使预定字符类 \w \W \b \B \s \S 取决于当前区域设定   
> re.U(全拼：UNICODE): 使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性        
> re.X(全拼：VERBOSE): 详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释。     

在刚才所说的另外几个方法例如 re.match 里我们就需要用到这个 pattern 了，下面我们一一介绍。

*注：以下七个方法中的flags同样是代表匹配模式的意思，如果在pattern生成时已经指明了flags，那么在下面的方法中就不需要传入这个参数了。*

<<<<<<< HEAD
* re.match(pattern, string[, flags])      

=======
* #####re.match(pattern, string[, flags])      
>>>>>>> origin/master
		这个方法将会从string（我们要匹配的字符串）的开头开始，尝试匹配pattern，一直向后匹配，如果遇到无法匹配的字符，立即返回 None，如果匹配未结束已经到达string的末尾，也会返回None。两个结果均表示匹配失败，否则匹配pattern成功，同时匹配终止，不再对 string向后匹配。下面我们通过一个例子理解一下    


		```python
		__author__ = 'CQC'
		# -*- coding: utf-8 -*-
		 
		#导入re模块
		import re
		 
		# 将正则表达式编译成Pattern对象，注意hello前面的r的意思是“原生字符串”
		pattern = re.compile(r'hello')
		 
		# 使用re.match匹配文本，获得匹配结果，无法匹配时将返回None
		result1 = re.match(pattern,'hello')
		result2 = re.match(pattern,'helloo CQC!')
		result3 = re.match(pattern,'helo CQC!')
		result4 = re.match(pattern,'hello CQC!')
		 
		#如果1匹配成功
		if result1:
		    # 使用Match获得分组信息
		    print result1.group()
		else:
		    print '1匹配失败！'
		 
		#如果2匹配成功
		if result2:
		    # 使用Match获得分组信息
		    print result2.group()
		else:
		    print '2匹配失败！'
		 
		#如果3匹配成功
		if result3:
		    # 使用Match获得分组信息
		    print result3.group()
		else:
		    print '3匹配失败！'
		 
		#如果4匹配成功
		if result4:
		    # 使用Match获得分组信息
		    print result4.group()
		else:
		    print '4匹配失败！'

		```
