---
title: Jupyter notebook
date: 2019-01-20 17:37:27
categories:
- python
tags:
- python
- Anaconda
- anaconda
- Jupyter notebook
---
如何使用 Jupyter notebook。

<!-- more -->

## 参考资料

[jupyter notebook 可以做哪些事情？](https://www.zhihu.com/question/46309360/answer/254638807)

## Jupyter notebook 是什么？

在没有notebook之前，在IT领域工作的我都是这样工作的：

在普通的 Python shell 或者在IDE（集成开发环境）如Pycharm中写代码，然后在word中写文档来说明你的项目。

这个过程很反锁，通常是写完代码，再写文档的时候我还的重头回顾一遍代码。最蛋疼的地方在于，有些数据分析的中间结果，我还的重新跑代码，然后把结果弄到文档里给客户看。

有了notebook之后，我的世界突然美好了许多，因为notebook 可以直接在代码旁写出叙述性文档，而不是另外编写单独的文档。也就是它可以能将代码、文档等这一切集中到一处，让用户一目了然。

例如，我的数据分析社群小伙伴就用Jupyter notebook写出了他的学习笔记，长这样，是不是很酷：

![](/images/python/46_0.jpg)

所以，你现在明白了这句话是在说什么了：

>Jupyter notebook（http://jupyter.org/） 是一种 Web 应用，能让用户将说明文本、数学方程、代码和可视化内容全部组合到一个易于共享的文档中。

![](/images/python/46_1.jpg)

Jupyter Notebook 已迅速成为数据分析，机器学习的必备工具。因为它可以让数据分析师集中精力向用户解释整个分析过程。

Jupyter这个名字是它要服务的三种语言的缩写：Julia，PYThon和R，这个名字与“木星（jupiter）”谐音。

## 如何安装 Jupyter notebook？

对于做数据分析这么有用的神器，不安装使用下是不是很遗憾？

安装 Jupyter 的最简单方法是使用 Anaconda。该发行版附带了 Jupyter notebook。你能够在默认环境下使用 notebook。

确保你已经安装了Anaconda，如果不知道如何安装的，可以看我之前写的：

[安装 Anaconda](https://benpaodewoniu.github.io/2019/01/20/python44/)

但是如果只安装了 conda 和 源生python 的话，你们还需要做下面的工作。

要在 conda 环境中安装 Jupyter notebook，在conda终端使用命令（以下所有命令是指conda的终端Anaconda Prompt）：

	conda install jupyter notebook
	
![](/images/python/46_2.jpg)

也可以通过python shell的 pip 来安装：pip install jupyter notebook。

## 如何启动Jupyter notebook？

有两种方法可以启动 notebook 服务器：


### 方法一：windowns在开始菜单中按下图点击打开Notebook

![](/images/python/46_3.jpg)

### 方法2：在终端中输入命令： jupyter notebook。

服务器会在你运行此命令的“notebook工作文件夹”中启动。也就是说后面你操作的任何 notebook 文件都会保存在该文件夹下，类似于你用优酷下载视频，优酷都会放到自己的下载目录一样。例如我在下面的C:\houzi 下面启动目录后，会在该目录下看到我后面运行的文件。

![](/images/python/46_4.jpg)

启动notebook 服务器后，在浏览器中打开notebook页面地址：http://localhost:8888

![](/images/python/46_5.jpg)

如果你同时启动了另一个 notebook 服务器，新服务器会尝试使用端口 8888，但由于此端口已被占用，因此新服务器会在端口 8889 上运行。之后，你可以通过 http://localhost:8889 连接到新服务器。以此类推。

## 新手如何快速使用notebook？

### 确保你在Anaconda终端中安装了以下包：

#### 安装环境自动关联包

	conda install nb_conda
	
该包可以将conda中创建的环境自动关联到你的notebook中。

上面那句话是什么意思呢?

假设我们创建了一个 conda 的虚拟环境，假设为 py2.

当我们打开 Jupyter Notebook 的时候。

![](/images/python/46_10.png)

我们选择 file 打开右边的 new ，虽然我们创建了 py2 虚拟环境，但其实在 new （图中 2）中，是看不见 py2 的。

但是我们安装了 nb_conda 之后，就会如图所示，能看见了。

那么要怎样安装 nb_conda 呢。

首先，我们要进入 conda 的 Anaconda Prompt ,然后

	conda env list
	
列出所有的虚拟环境，激活 py2

	activate py2
	
进入虚拟环境后，我们再安装

	conda install nb_conda
	
这样，我们的 py2 就会再 file 那个界面中出现了。

下面的这段话，我也没理解是什么意思。。。感觉原作者写的逻辑有问题，但是如果让我猜的话，应该是他创建了虚拟环境 py2 ，但是再执行下面的语句的时候，py2 并没有出来。

我们可以对应conda中的环境，就知道这些环境对应conda中的环境列表。用 conda env list 就可以列出你创建的所有环境。

![](/images/python/46_6.jpg)

其中py2是我在conda中创建的Python2环境名称，

root和default一样是默认环境，因为我安装的是Anaconda3，所以默认环境是Python3。

你会发现环境名称py3没有出现在notebook中。解决办法是按下图步骤安装包ipykernel。(这个情况我没有遇到过，因为创建的虚拟环境，都有这个包)

（同样的，在你的py2环境下也要像刚才步骤那样安装一次这个包）

![](/images/python/46_7.jpg)

完成上面安装步骤，回到标签页“Files”，再新建notebook时，会发现已经关联了环境名称py2和py3：

![](/images/python/46_8.jpg)

这样你在notebook中可以轻松切换Python2和Python3环境了。

经过上面步骤后，notebook的首页右上角，在新建的时候没有显示py3和py2两个环境的关联，这个时候，你可以尝试重启浏览器，注意！是重启浏览器，不是notebook！刷新浏览器有时候是没有用滴（因为缓存的原因），而然我刷了两个小时。。。

### 在Anaconda终端安装代码自动补全包

	conda install pyreadline
	
![](/images/python/46_9.jpg)

什么是代码自动补全呢？

后面会介绍，您就等好吧。

## 顶部的3个选项卡

这个是老版本的了，现在还多了一个 conda

顶部的3个选项卡是：Files（文件）、Running（运行）和 Cluster（集群）。

Files（文件）显示当前“notebook工作文件夹”中的所有文件和文件夹。

点击 Running（运行）选项卡会列出所有正在运行的 notebook。可以在该选项卡中管理这些 notebook。

Clusters一般不会用到。因为过去在 Clusters（集群）中创建多个用于并行计算的内核。现在，这项工作已经由 ipyparallel 接管。

![](/images/python/46_11.jpg)

### 如何创建一个新的notebook？

像下面图片中一样，在右侧点击“New”（新建），创建新的 notebook、文本文件、文件夹或终端。

“Notebooks”下的列表显示了你已安装的内核。由于我在 Python 3 环境中运行服务器，因此列出了 Python 3 内核。你在这里看到的可能是 Python 2。这里我点击Python3。

![](/images/python/46_12.jpg)

这样你就打开了下面的页面，你会看到外框为绿色的一个小方框。它称为单元格。单元格是你编写和运行代码的地方。以后你就可以在这里写你的数据分析代码了。

![](/images/python/46_13.jpg)

在这里你可以输入自己人生中的第一行Python代码Hello world。然后点击图中的运行按钮，会执行你当前所在的代码，其实我更喜欢用快捷键（键盘上同时按住ctrl+enter键）来执行代码。

![](/images/python/46_14.gif)

这句代码的意思是在界面输出字符串"Hello world！"，所以你会看到在下面与输出结果出来。

运行代码单元格时，单元格下方会显示输出。单元格还会被编号（左侧会显示 In [1]:）。如果运行了多个单元格的话（也就是多块代码），这能让你知道运行的代码和运行顺序。

![](/images/python/46_15.jpg)

notebook 中的大部分工作均在代码单元格中完成。这是编写和执行代码的地方。在代码单元格中可以执行多种操作，例如编写代码、给变量赋值、导入包，展示数据分析结果等。在一个单元格中执行的任何代码在所有其他单元格中均可用。

+按钮用于创建新的单元格

![](/images/python/46_16.gif)

还记得一开始我提到代码自动补全功能吗？那么，什么是代码自动补全呢？

比如 我定义了下面的变量。

![](/images/python/46_17.jpg)

在后面代码中用到这个变量是，我只要输入第一个变量的第一个字母p，然后按下Tab键，边会自动查找到代码中以p开头的变量名称，这可以大幅度提供你写代码的效率。

![](/images/python/46_18.jpg)

### 重命名notebook

你会看到刚才我建的notebook文件名是下面这样默认的，我想修改成自己喜欢的文件名如何办呢？

![](/images/python/46_19.jpg)

点击“File”->Rename，可以对notebook文件进行重命名，这里我命名成‘Helloworld’

![](/images/python/46_20.jpg)

同时，你可以在当前运行notebook服务器的“notebook工作文件夹”下看到创建的notebook，文件名后缀是ipynb。

（其实Notebook 就是个扩展名为 .ipynb 的大型 JSON 文件。）

![](/images/python/46_21.jpg)

点击下面的保存按钮，可以保存你的notebook文件。但 notebook 也会定期自动保存。

![](/images/python/46_22.jpg)

![](/images/python/46_23.jpg)

### 关闭 notebook文件

通过在服务器主页上选中 notebook 旁边的复选框，然后点击“Shutdown”（关闭），你就可以关闭各个 notebook。

但是，在这样做之前，请确保你保存了工作！否则，在你上次保存后所做的任何更改都会丢失。同时如果不保存，你下次运行 notebook 时，你还需要重新运行代码。

![](/images/python/46_24.jpg)

### 如何共享你的notebook？

点击File->Download as，你可以选择多种格式下载你的notebook。一般我都会根据下面的用途来选择不同的下载格式：

>1）如果我想和客户分享我的数据分析成果，我会选择将notebook下载为HTML文件。
2）如果我希望将自己的数据分析成果和代码嵌入到项目中，比如为药店管理系统做个数据分析子模块，我就会选择Python（.py）模块，这可以将我的代码融入项目中，成为子模块，方便和其他开发人员共同完成任务。
3）如果要在博客或文档中使用 notebook，我就选择Markdown格式。


![](/images/python/46_25.jpg)

### 关闭Jupler notebook服务器

通过在终端中按两次 Ctrl + C，可以关闭整个服务器。再次提醒，这会立即关闭所有运行中的 notebook，因此，请确保你保存了工作！

关闭notebook服务器后，下次启动再打开notebook，当你继续在该notebook中写代码时，发现之前的变量无法访问了。需要你在该notebook的Kernerl选项卡中选择“Run All”重新编译下之前的代码。

![](/images/python/46_26.jpg)

### 安装的包在notebook中不可使用的问题

是因为安装包的时候，不在当前notebook所在的python环境下安装了包，所以在这个python环境下找不到包。解决办法如下：

![](/images/python/46_27.jpg)

![](/images/python/46_28.jpg)




