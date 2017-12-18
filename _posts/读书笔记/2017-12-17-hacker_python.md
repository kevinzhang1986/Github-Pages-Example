---
layout:     post
title:      读书笔记- Python高手之路
category: 读书笔记
description: Python高手之路读书笔记
tags: python
---

# Python高手之路读书笔记

## 第1章 项目开始

### 1. 1 Python 版本
- 2.7版本目前是也将仍然是2.x的最后一个版本。2.7将被继续使用到2020年左右，不会马上消失。
- 3.3 和 3.4 版本都是Python 3最近发行的两个版本，也是应该重点支持的版本。

同时支持2.7和3.3的技术将在第13章介绍。

### 1. 2 项目结构
一个常犯的错误是将单元测试放在包目录的外面。

![](images/2017-12-17-hacker_python/1.jpg)<br>
- setup.py是Python安装脚本的标准名称。在安装时，它会通过Python分发工具（distuils）进行包的安装。
- 也可以通过README.rst（或者README.txt，或者其他合适的名字）为用户提供重要信息。
- requirements.txt应该包含Python包所需要的依赖包。
- 还可以包含test-requirements.txt，它应该列出运行测试集所需要的依赖包。
- 最后，docs文件夹应该包括reStructuredText格式的文档，以便能够被Sphinx处理。<br>
常见的顶层目录：<br>
- etc用来存放配置文件的样例。
- tools用来存放与工具有关的shell脚本。
- bin用来存放将被setup.py安装的二进制脚本。
- data用来存放其他类型的文件，如媒体文件。

### 1．3 版本编号 
PEP440中定义版本号应该遵从以下正则表达式的格式：
> N[.N]+[{a|b|c|rc}N][.postN][.devN]


### 1．4 编码风格与自动检查 
编写Python代码的PEP 8标准。

* 每个缩进层级使用4个空格。
* 每行最多79个字符。
* 顶层的函数或类的定义之间空两行。
* 采用ASCII或UTF-8编码文件。
* 在文件顶端，注释和文件说明之下，每行每条import语句只导入一个模块，同时要按标准库、第三方库和本地库的导入顺序进行分组。
* 在小括号、中括号、大括号之间或着逗号之前没有额外的空格。
* 类的命名采用骆驼命名法；异常的定义使用Error前缀（如适用的话）；函数的命名使用小写字符；用下划线开头定义私有的属性或方法。

可以用pep8和flake8(openstack使用)来检查代码。如果需要处理更高级的版本号，可以考虑一下PEP 426

## 第2章　模块和库 
### 2．1 导入系统 
导入系统是相当复杂的.
sys模块包含许多关于Python导入系统的信息
sys.module
sys.builtin_module_names
sys.path
导入钩子机制是由PEP 302（http://www.python.org/dev/peps/pep-0302/）定义的3
hy模块加载器


### 2．2 标准库 
下面是一些必须了解的标准库模块。

* atexit允许注册在程序退出时调用的函数。
* argparse提供解析命令行参数的函数。
* bisect为可排序列表提供二分查找算法。
* calendar提供一组与日期相关的函数。
* codecs提供编解数据的函数。
* collections提供一组有用的数据结构。
* copy提供复制数据的函数。
* csv提供用于读写CSV文件的函数。
* datetime提供用于处理日期和时间的类。
* fnmatch提供用于匹配Unix风格文件名模式的函数。
* glob提供用于匹配Unix风格路径模式的函数。
* io提供用于处理I/O流的函数。
* json提供用来读写JSON格式数据的函数。
* logging提供对Python内置的日志功能的访问。
* multiprocessing可以在应用程序中运行多个子进程，而且提供API让这些子进程看上去像进程一样。
* operator提供实现基本的Python运算符功能的函数，可以使用这些函数而不是自己写lambda表达式。
* os提供对基本的操作系统函数的访问。
* random提供生成伪随机数的函数。
* re提供正则表达式功能。
* select提供对函数select()和poll()的访问，用于创建事件循环。
* shutil提供对高级文件处理函数的访问。
* signal提供用于处理POSIX信号的函数。
* tempfile提供用于创建临时文件和目录的函数。
* threading提供对处理高级线程功能的访问。
* urllib提供处理和解析URL的函数。
* uuid可以生成全局唯一标示符。

### 2．3 外部库
- Python 3兼容。尽管现在你可能并不准备支持Python 3，但很可能早晚会涉及，所以确认选择的库是Python 3兼容的并且承诺保持兼容是明智的。
- 开发活跃。[GitHub](http://github.com)和[Ohloh](http://www.ohloh.net/)通常提供足够的信息来判断一个库是否有维护者仍然在工作。
- 维护活跃。
- API兼容保证。没有比你的软件因为一个它依赖的库发生了变化而使整个API崩溃更糟的了。你一定很想知道选择的库在过去是否发生过类似的事件。

### 2．4 框架 
外部库选择的原则也适用与框架，但是更换框架比更换库难受一千倍。

### 2．5 Doug Hellmann访谈
一个模块加入标准库的步骤等。

### 2．6 管理API变化
- 可以文档知会，或者告警。
- 从Python 2.7开始，DeprecationWarning默认将不显示。可以通过在调用python时指定-W all选项来禁用这一过滤器。关于-W的可用值的更多信息可以参考python手册。

### 2．7 Christophe de Vienne访谈

## 第3章　文档 
Python中文档格式的事实标准是reStructuredText，简称reST。
项目的文档应该包括：
- 用一两句话描述项目解决的问题
- 项目基于的分发许可 （开源许可协议等）
- Demo
- 安装指南
- 一些链接（Github, Google Email, Wiki, Bug 等）

### 3．1 Sphinx和reST入门
### 3．2 Sphinx模块
### 3．3 扩展Sphinx

## 第4章　分发 
### 4．1 简史
### 4．2 使用pbr打包
### 4．3 Wheel格式 
### 4．4 包的安装 
### 4．5 和世界分享你的成果 
### 4．6 Nick Coghlan访谈
### 4．7 扩展点
#### 4．7．1 可视化的入口点 
#### 4．7．2 使用控制台脚本 
#### 4．7．3 使用插件和驱动程序 

## 第5章　虚拟环境
> virtualenv myappenv <br>
> source myappenv/bin/activate <br>
> pip install -r requirements.txt <br>
> deactivate

在某些特定场景下，仍然需要访问系统中安装的包。那么可以通过在创建虚拟环境时向virtualenv命令传入--system-site-packages标志来实现。

## 第6章　单元测试 
### 6．1 基础知识
### 6．2 fixture
### 6．3 模拟（mocking）
### 6．4 场景测试
### 6．5 测试序列与并行 
### 6．6 测试覆盖
### 6．7 使用虚拟环境和tox 
### 6．8 测试策略
### 6．9 Robert Collins访谈

## 第7章　方法和装饰器 
### 7．1 创建装饰器 
functools模块通过update_wrapper函数，复制这些属性给这个包装器本身。
要找特定的函数参数不够智能，使用inspect模块

```
 def desc.(f):
     def wraps(*args, **keargs):
        func_args = inspect.getcallargs(f, *arg, **kwargs)
        if func_args.get('user_name') != 'admin':
            raise ...
        f(*args, **kwargs)
        ...
    return wraps
```

- insect.getcallargs

### 7．2 Python中方法的运行机制 

- Python3中已经完全删除了未绑定方法这个概念。

### 7．3 静态方法
静态方法属于类的方法，但实际上并非运行在类的实例上。

- 不必为每个实例一个绑定方法，绑定方法是对象，创建是有开销的
> P.static_method is P().static_method is P(x).static_method
- 提高代码的可读性
- 可以在子类中覆盖静态方法，如果把其作为全局函数，则其只存在一份，而无法在同一个函数中根据对象类型而调用不同的方法。

### 7．4 类方法
类方法是直接绑定到类而非它的实例的方法。

### 7．5 抽象方法 
抽象方法是定义在基类中可能有或者没有任何实现的方法。

### 7．6 混合使用静态方法、类方法和抽象方法 1
### 7．7 关于super的真相 

## 第8章　函数式编程
在以函数式风格写代码时，函数应该设计成没有其他副作用。函数接受参数生成输出而不保留任何状态或修改任何不反映在返回值中的内容。<br>

函数式编程特点:

- 可形式化证明
- 模块化
- 简洁
- 并发
- 可测性

### 8．1 生成器 
生成器就是对象，每次调用它的next方法时返回一个值，直到它抛出StopIteration。在pep 255中引入。

解释器会保存对栈的引用，用来下一次调用next函数时恢复函数的执行。

包含yield语句的Python函数。Python会将其标识为生成器。可以使用inspect对其进行检查。

可以用send（）函数来向生成器发送一个值。类似lua。

### 8．2 列表解析
可以在单行内构造列表内容。可以使用多个for以及if判断进行过滤，原则上是不限制个数的。 
Python2.7之后可以在单行构造字典和集合。

### 8．3 函数式，函数的，函数化 
- map(function, iterable)，Python2中返回列表,Python3中返回可迭代的map对象
- filter(funciton or None, iterable)，返回同上
- 可以使用生成器和列表解析实现与filter或map等价的函数
- enumerate(iterable[,start])返回一个可迭代的enumerate对象
- sorted
- any / all
- zip, python3返回可迭代对象
- first
> next(filter(lambda x:x>0, [-1, 0, 1, ...]))
- functools.partial
- operator
> first([-1,0,1], key=partial(operator.le, 0))
- itertools.chain / combinations / compress / cpunt / cycle / dropwhile / groupby / permutations / product / takewhile

## 第9章　抽象语法树
### 9．1 Hy
### 9．2 Paul Tagliamonte访谈
## 第10章　性能与优化
### 10．1 数据结构
### 10．2 性能分析
### 10．3 有序列表和二分查找 
### 10．4 namedtuple和slots
### 10．5 memoization
### 10．6 PyPy
### 10．7 通过缓冲区协议实现零复制 1
### 10．8 Victor Stinner访谈
## 第11章　扩展与架构
### 11．1 多线程笔记
### 11．2 多进程与多线程 
### 11．3 异步和事件驱动架构 
### 11．4 面向服务架构 
## 第12章　RDBMS和ORM
### 12．1 用Flask和PostgreSQL流化数据 
### 12．2 Dimitri Fontaine访谈
## 第13章　Python 3支持策略
### 13．1 语言和标准库 
### 13．2 外部库
### 13．3 使用six
## 第14章　少即是多 
### 14．1 单分发器 
### 14．2 上下文管理器 