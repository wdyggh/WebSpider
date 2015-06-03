###scrapy入门03 命令行工具(Command line tools)

* 1
* 2
* 3
* 4
* 5
* 6

Scrapy是通过 scrapy 命令行工具进行控制的。 这里我们称之为 `“Scrapy tool”` 以用来和子命令进行区分。 对于子命令，我们称为 “command” 或者 “Scrapy commands”。

Scrapy tool 针对不同的目的提供了多个命令，每个命令支持不同的参数和选项。

#### 默认的Scrapy项目结构

在开始对命令行工具以及子命令的探索前，让我们首先了解一下Scrapy的项目的目录结构。

虽然可以被修改，但所有的Scrapy项目默认有类似于下边的文件结构:

```bash
scrapy.cfg
myproject/
    __init__.py
    items.py
    pipelines.py
    settings.py
    spiders/
        __init__.py
        spider1.py
        spider2.py
        ...
```

scrapy.cfg 存放的目录被认为是 项目的根目录 。该文件中包含python模块名的字段定义了项目的设置。例如:

```bash
[settings]
default = myproject.settings
```

#### 使用 `scrapy` 工具

您可以以无参数的方式启动Scrapy工具。该命令将会给出一些使用帮助以及可用的命令:

```bash
Scrapy X.Y - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  crawl         Run a spider
  fetch         Fetch a URL using the Scrapy downloader
[...]
```

如果您在Scrapy项目中运行，当前激活的项目将会显示在输出的第一行。上面的输出就是响应的例子。如果您在一个项目中运行命令将会得到类似的输出:

```bash
Scrapy X.Y - project: myproject

Usage:
  scrapy <command> [options] [args]

[...]
```

#### 创建项目

一般来说，使用 `scrapy` 工具的第一件事就是创建您的Scrapy项目:

```bash
scrapy startproject myproject
```

该命令将会在 `myproject` 目录中创建一个Scrapy项目。  
接下来，进入到项目目录中:  
`cd myproject`   
这时候您就可以使用 scrapy 命令来管理和控制您的项目了。  

#### 控制项目

您可以在您的项目中使用 scrapy 工具来对其进行控制和管理。比如，创建一个新的spider:

```bash
scrapy genspider mydomain mydomain.com
```

有些Scrapy命令(比如 `crawl`)要求必须在Scrapy项目中运行。 您可以通过下边的 ``commands reference` 来了解哪些命令需要在项目中运行，哪些不用。

另外要注意，有些命令在项目里运行时的效果有些许区别。 以fetch命令为例，如果被爬取的url与某个特定spider相关联， 则该命令将会使用spider的动作(`spider-overridden behaviours`)。 (比如spider指定的 `user_agent`)。 该表现是有意而为之的。一般来说， `fetch` 命令就是用来测试检查spider是如何下载页面。

#### 可用的工具命令(tool commands)

该章节提供了可用的内置命令的列表。每个命令都提供了描述以及一些使用例子。您总是可以通过运行命令来获取关于每个命令的详细内容:

```bash
scrapy <command> -h
```

您也可以查看所有可用的命令:

`scrapy -h`

Scrapy提供了两种类型的命令。一种必须在Scrapy项目中运行(针对项目(Project-specific)的命令)，另外一种则不需要(全局命令)。全局命令在项目中运行时的表现可能会与在非项目中运行有些许差别(因为可能会使用项目的设定)。

<a name="index"></a>
**全局命令:**

* [startproject](#startproject)
* settings
* runspider
* shell
* fetch
* view
* version

**项目(Project-only)命令:**

* crawl
* check
* list
* edit
* parse
* genspider
* deploy
* bench

<a name="startproject"></a>  
##### startproject

语法: `scrapy startproject <project_name>`  
是否需要项目: `no`  
在 `project_name` 文件夹下创建一个名为 project_name 的Scrapy项目。  
例子:  

```bash
$ scrapy startproject myproject
```
[回到目录](#index) 





















