---
title: pycharm的一些技巧
date: 2019-11-10 18:00:09
categories:
- [python,IDE,pycharm]
tags:
- pycharm
---
这里是关于pycharm的技巧。

<!-- more -->

<br/>

# 参考资料

<br/>

[受用一生的高效 PyCharm 使用技巧](https://www.itcodemonkey.com/article/14888.html)

<br/>

# 删文件

<br/>

pycharm的删除文件并不会放到回收站，它有它自己的机制。

![](/images/pycharm/0_0.png)

图中那两个选项都要选上，这样在删除的时候，它会自动查询其它地方有没有引用这个文件。

如果删除了那怎么办？Pycharm 的隐藏的一个功能 Local History，它会保存你对文件的所有操作记录。

![](/images/pycharm/0_1.png)

此时你可以在你的项目目录里，点击右键，有个 Local History 的选项，再点击子选项 Show History，你可以看到这里有个记录板。如果你想恢复删除的文件，就在删除的记录项点击右键，选择 Revert 即可恢复。

<br/>

# 代码排版，自动PEP8

<br/>

pep8 是Python 语言的一个代码编写规范。如若你是新手，目前只想快速掌握基础，而不想过多去注重代码的的编写风格（虽然这很重要），那你可以尝试一下这个工具 - autopep8

首先在全局环境中（不要在虚拟环境中安装），安装一下这个工具。

	sudo pip install autopep8
	
然后在 Pycharm 里导入这个工具。

	Name: AutoPep8
	Description: autopep8 your code
	Program: autopep8
	Arguments: --in-place --aggressive --aggressive $FilePath$
	Working directory: $ProjectFileDir$
	Output filters: $FILE_PATH$\:$LINE$\:$COLUMN$\:.*
	
找到首选项中的 tool 

![](/images/pycharm/0_2.jpg)

我随意写了一段不符合 pep8 规范的代码。

![](/images/pycharm/0_3.jpg)

点击右键，选择 External Tools -> AutoPep8

![](/images/pycharm/0_4.png)

你可能会说，Pycharm 本身就自带这个功能了呀，快捷键 Command+Option+L ，就可以实现一键pep8了。你可以对比一下，Pycharm 自带的代码 pep8 化功能 并没有像这个autopep8 来得彻底。 我相信你最终的选择肯定是后者。

<br/>

# 使用书签，快速定位

<br/>

都我在看框架的源代码时，最常使用的是 Command + B （也就是 command + 鼠标左键 ）一层一层地往里深入，但是当源代码比较多，可能一整个事件过程涉及十几文件，函数调用错综复杂，对于一个庞大的项目来说，有用的可能就几个关键函数，每次要找到这几个函数，都要重头从源函数再一层一层的找下去，这样实在太麻烦了，我常常因此把自己给看晕了。

直到后来我发现了 Pycharm 这个书签功能。

使用书签功能，我可以在在关键的位置打上书签，想看的时候，调用书签，快速定位即可。

使用它，你需要记住下面下几个快捷键

	Command + F11    打上书签
	Shift + F11       展示所有书签
	F11                   删除书签（经测试仅能在Windows上有效）
	在 macbook 中 Command + F11 也是取消书签，但是，必须移动焦点，再返回才行
	
![](/images/pycharm/0_5.jpg)

在你要打书签的位置，按下 Command + F11 ，你可以给这个位置加个序号，可以是数字也可以是字母，假如在下面这个位置 加了 1 这个序号，下次你就可以使用 Control + 1 直接跳转到这个位置。

当然你也可以不加，不加的话就是匿名书签了。你可以使用 Shift + F11 展示所有的书签，再进行跳转。

<br/>

# 指定参数执行脚本

<br/>

你在 Pycharm 运行你的项目，通常是怎么执行的？我的做法是，右键，然后点击 Run，或者使用快捷键 Shift + F10 。

有时候，在运行/调试脚本的时候，我们需要指定一些参数，这在命令行中，直接指定即可。

假设在命令行中，运行脚本的命令是这样

	python main.py init --local
	
对于刚使用 Pycharm 的同学，可能并不知道 Pycharm 也是可以指定参数的。点击下图位置

![](/images/pycharm/0_6.png)

进入设置面板，在 Script parameters 中填入参数即可。

![](/images/pycharm/0_7.jpg)

同样，我们可以在这个界面制定环境变量

比如，我们本地有的环境变量需要读，但是，那么我们将可以将环境变量配置成下面这样

![](/images/pycharm/0_9.jpg)

![](/images/pycharm/0_8.jpg)

<br/>

# 搜索时过滤测试文件

<br/>

我平时会看的框架是 OpenStack ，我不知道其他框架是怎样的，但在 OpenStack 里面带有大量（真的很多）的单元测试文件。这给我在使用 Find in Path  时带来了不小的困扰，你可以从下图的搜索结果中感受一下，搜索一个函数，test 文件里的结果比 正常文件要多很多。

![](/images/pycharm/0_10.jpg)

这些测试文件的搜索结果，对于我们看源代码不仅没有任何帮助的，更重要的是还干扰视线。于是我就研究了一下，从文件名入手，只要在 File mask 里填写 !test* 可以将这些test文件过滤掉。搜索结果一下子清晰很多。

![](/images/pycharm/0_11.jpg)

<br/>

# 关闭烦人的灯泡提示

<br/>

先来说下这个灯泡提示是什么，有什么用？

当我们在代码里面有语法错误，或者代码编写不符合 pep8 代码规范时，鼠标选择有问题的代码，就会自动弹出小灯泡，这个灯泡是有颜色之分的，如果是红灯泡，一般都是语法问题，如果不处理会影响代码运行。而如果是黄灯泡，就只是一个提示，提示你代码不规范等，并不会影响程序的运行。

虽然这个灯泡，是出于善意之举，但我认为它确实有点多余（可能是我个人没有使用它的习惯），要是语法错误会有红色波浪线提示。你可能会说灯泡不仅起到提示的作用，它还可以自动纠正代码，我个人感觉并没有人工校正来得效率，来得精准。

基于有时还会像知乎上这个朋友说的这样，会挡住我们的代码，会经常误点，这确实也是一个烦恼。

我研究了下，Pycharm （2018版本）里是有开关按钮的，将下图中的这个选项（Show intention bulb）取消勾选，就可以关闭这个功能。

![](/images/pycharm/0_12.jpg)

<br/>

# 关闭碍眼的波浪线

<br/>

下面我先给出了一小段代码示例，思考一下，为什么name，my_name 不会有波浪线，而 myname 和 wangbm 会有波浪线呢？

![](/images/pycharm/0_13.png)

Pycharm 本身会实时地对变量名进行检查，如果变量名不是一个已存在的英文单词，就会出现一条波浪线，当一个变量里有多个单词时，Python 推荐的写法是用下划线来分隔（其他语言可能会习惯使用驼峰式命名法 ，但 Python 是使用下划线），所以在 Pycharm 看来  my_name 是规范的，而 myname 会被当成是一个单词对待，由于它在单词库里并没有它，所以 myname 是不规范的。

每个人的变量命名习惯不一样，如何你在项目里大量使用了 myname 这种风格的变量命名方法，是让人挺不舒服的，总有一种代码有 bug 的错觉。

那么如何关闭这个非语法级别的波浪线呢？很简单，它的开关就在你的右下角那个像 人头像 一样的按钮

![](/images/pycharm/0_14.png)

然后选择 Syntax 级别的即可。同样一段代码，效果如下，干净了很多。

<br/>

# 调试技巧

<br/>

## 缓存变量

假如我们在一个爬虫的项目中，会使用到 正则表达式 来匹配我们想要抓取的内容。正则这种东西，有几个人能够一步到位的呢，通常都需要经过很多次的调试才能按预期匹配。在我们改了一次正则后，运行了下，需要重新向网站抓取请求，才能发现没有匹配上，然后又改了一版，再次运行同样需要发起请求，结果还是发现还是没有匹配上，往往复复，正则不好的同学可能要进行几十次的尝试。

（上面这个例子可能不太贴切，毕竟是有很多种方法实现不用重新发请求，只是列举了一种很笨拙且低效的调试过程，你看看就好了）

而我们在这几十次的调试中，向同一网站发起请求都是没有意义的重复工作。如果在 Pycharm 中可以像  IPython Shell  和 Jupyter Notebook 那样，可以记住运行后所有的变量信息，可以在不需要重新运行项目或脚本，只需稍微调整下我们的代码，直接就可以进行正则调试。

答案当然是有。

假如我在调试如下几行简单的代码。在第 3 行处打了个断点。然后点击图示位置 Show Python Prompt 按钮。

![](/images/pycharm/0_20.jpg)

就进入了 Python Shell 的界面，这个Shell 环境和我们当前运行的程序环境是打通的，使用的是同一个 namespace，这下你可以轻松地进行调试了。

![](/images/pycharm/0_21.jpg)

上面我们打了个断点，是为了方便说明这个效果。并不是说一定要打断点。如果不打断点，在脚本执行完成后，也仍然可以在这个界面查看并操作所有变量。

![](/images/pycharm/0_22.jpg)

现在我们已经可以满足我们的调试的需求，但是每次运行脚本，都要手动点击 Show Python Prompt ，有点麻烦。嗯？其实这个有地方可以设置默认打开的。这个开头还比较隐秘，一般人还真发现不了。

你需要点击图示位置 Edit Configurations 处。

![](/images/pycharm/0_23.png)

然后在这里打勾选中。

![](/images/pycharm/0_24.png)

设置上之后，之后你每次运行后脚本后，都会默认为你存储所有变量的值，并为你打开 console 命令行调试界面。

除了上面这种方法，其实还有一种方法可以在调试过程中，执行命令表达式，而这种大家可能比较熟悉了，这边也提一下，就当是汇总一下。但是从功能上来说，是没有上面这种方法来得方便易用的。因为这种方法，必须要求你使用 debug 模式运行项目，并打断点。

使用方法就是，在你打了断点后，在图示位置处，点击右键使用 Evaluate Expression

![](/images/pycharm/0_25.jpg)

就弹出了一个 Evaluate Expression 窗口，这里 可以运行命令表达式，直接操作变量。

![](/images/pycharm/0_26.jpg)

<br/>

# 代码模板，效率编码

<br/>

Pycharm 提供的这个代码模板，可以说是相当实用的一个功能了。它可以在你新建一个文件时，按照你预设的模板给你生成一段内容，比如解释器路径，编码方法，作者详细信息等

![](/images/pycharm/0_27.jpg)

按照上图模板，生成的效果如下。

![](/images/pycharm/0_28.jpg)

除了新建文件时可以初始化文件，在开发编写代码时，也同样使用 Pycharm 中自带的实用的代码模板，提高你的编码效率。

当你在键盘中敲入 Command + J 时，就可以调出一个面板，从下图可以看出里面有许多预设的模板。

![](/images/pycharm/0_29.jpg)

如果我们想选择最后一个 main ，可以继续键入 main，然后就可以直接生成如下这段平时都要手动敲入的代码。

![](/images/pycharm/0_30.gif)

<br/>

# 运行多个代码文件

<br/>

![](/images/pycharm/0_31.png)

选择 edit configurations

勾选右边的 allow parallel run 勾选完之后，你可以在这个面板中修改参数，运行都是根据这个面板参数运行的。

![](/images/pycharm/0_32.png)

<br/>

# 不让图片在 ide 中显示

<br/>

有的时候我们使用画图的库，比如 matplotlib 或者 pillow 的时候，在显示图片，会直接在 ide 中显示。

但是，这样显示的话，ide 会自动记录图片，导致有的时候内存占用率太大了。

我个人还是倾向于不再 ide 中显示图片，所以，我们要这样设置。

	File -> Settings -> Tools -> Python Scientific -> 去掉Show plots in tool window勾选

在 MacBook 上，我们选择 preferences ，然后直接搜索 tools 找到相关选项就好了。

enjoy it!!!

<br/>

# pycharm 的 flask 自启用

<br/>

pychram 会自动识别你这个项目用的是什么框架，比如，下面的 flask 

![](/images/pycharm/0_33.png)

pycharm 会将其变成 flask

![](/images/pycharm/0_34.png)

然后采用 flask 的启动方式，这样就导致

![](/images/pycharm/0_33.png)

中，线程启动不了。（不能 debug）

造成这个方面的原因，我认为可能有几个方面

1. 项目没有严格遵守 flask 的书写规范
2. pycharm 处理不好

但是，如果你在命令行中运行

	python main.py

是可以正常运行的，所以，我们的目的就是让 pycharm 按照 python main.py 的方式运行，而不是 flask

![](/images/pycharm/0_35.png)

然后我们运行这个 main.py 就好了

![](/images/pycharm/0_36.png)

<br/>

# 本地的包无法 import

<br/>

我近期遇见过本地的包符合编写规范，但是，就引入不了。

出现这个情况有两种。

## 非 pycharm 环境

这个的话，我们需要把那个路径添加到环境变量中。

## pycharm 环境

![](/images/pycharm/0_37.png)

pycharm 出现这样的情况可能是由于错误的设置了项目引用路径。

如果以上还不解决问题，可以尝试把路径添加到环境变量。