---
title: 安装 Anaconda
date: 2019-01-20 11:33:02
categories:
- anaconda
tags:
- anaconda
---
这是安装 anaconda 的教程。
<!-- more -->
## 参考资料
[初学 Python 者自学 Anaconda 的正确姿势是什么？](https://www.zhihu.com/question/58033789/answer/254673663)
[Python管理包工具anaconda安装过程常见问题解决办法](https://zhuanlan.zhihu.com/p/34337889)
[conda的官方文档](https://conda.io/docs/user-guide/tasks/index.html)
## Anaconda是什么？
Anaconda在英文中是“蟒蛇”，麻辣鸡（Nicki Minaj妮琪·米娜）有首歌就叫《Anaconda》，表示像蟒蛇一样性感妖娆的身体。
所以你看下面Anaconda的图标就像一个收尾互相咬住的“蟒蛇”。
{% img /images/python/44_0.jpg %}
## 你可能已经安装了 Python，那么为什么还需要 Anaconda？
>1）Anaconda 附带了一大批常用数据科学包，它附带了 conda、Python 和 150 多个科学包及其依赖项。因此你可以立即开始处理数据。
2）管理包Anaconda 是在 conda（一个包管理器和环境管理器）上发展出来的。在数据分析中，你会用到很多第三方的包，而conda（包管理器）可以很好的帮助你在计算机上安装和管理这些包，包括安装、卸载和更新包。
3）管理环境为什么需要管理环境呢？比如你在A项目中用了 Python 2，而新的项目B老大要求使用Python 3，而同时安装两个Python版本可能会造成许多混乱和错误。这时候 conda就可以帮助你为不同的项目建立不同的运行环境。还有很多项目使用的包版本不同，比如不同的pandas版本，不可能同时安装两个 Numpy 版本，你要做的应该是，为每个 Numpy 版本创建一个环境，然后项目的对应环境中工作。这时候conda就可以帮你做到。

## 如何安装Anaconda？
我们要注意的是， Anaconda 的 2 和 3 并不能共存，如果同时安装 2 和 3 的话，那么最后之后存在最后安装的那个版本。
官网给的建议是，创建对应 python 版本的虚拟机。
[Managing environments](https://conda.io/docs/user-guide/tasks/manage-environments.html)
Anaconda 可用于多个平台（ Windows、Mac OS X 和 Linux）。你可以在下面地址上找到安装程序和安装说明。根据你的操作系统是32位还是64位选择对应的版本下载。
（Anaconda已经不支持Windows XP；同时查看自己电脑是32位还是64位，不要装错了。）
[下载地址](https://www.anaconda.com/download/#windows)
有一个技巧，就是复制资源链接，然后用迅雷下载。
Anaconda 的下载文件比较大（约 500 MB），因为它附带了 Python 中最常用的数据科学包。
如果计算机上已经安装了 Python，安装不会对你有任何影响。实际上，脚本和程序使用的默认 Python 是 Anaconda 附带的 Python。
注意：如果你是windows 10系统，注意在安装Anaconda软件的时候，右击安装软件→选择以管理员的身份运行。
{% img /images/python/44_1.jpg %}
完成安装后，如果你是在windows上操作，按下面图打开 Anaconda Prompt （或者 Mac 下的终端），后面我会将Anaconda Prompt统一称为“终端”。
{% img /images/python/44_2.jpg %}
注意：如果你是windows 10系统，按下图操作
{% img /images/python/44_3.jpg %}
如果win10系统没有按上述操作，后面会报下面的错误信息：
{% img /images/python/44_4.jpg %}
可以在终端或命令提示符中键入 conda list，以查看你安装的内容。
{% img /images/python/44_5.jpg %}
如果Anaconda Prompt中可以使用conda命令，接着下面继续操作。
为了避免后面使用报错，你需要先更新下所有包。在终端输入更新所有包的命令：

	conda upgrade    --all
	
并在提示是否更新的时候输入 y（Yes）让更新继续。初次安装下的软件包版本一般都比较老旧，因此提前更新可以避免未来不必要的问题。
## 如何管理包？
安装了 Anaconda 之后，就可以很方便的管理包了（安装，卸载，更新）。
### 安装包
在终端中键入：

	conda install package_name
	
例如，要安装 pandas，在终端中输入：

	conda install pandas
	
你还可以同时安装多个包。类似 conda install pandas numpy 的命令会同时安装所有这些包。还可以通过添加版本号（例如 conda install numpy=1.10）来指定所需的包版本。
conda 还会自动为你安装依赖项。例如，scipy 依赖于 numpy，因为它使用并需要 numpy。如果你只安装 scipy (conda install scipy)，则 conda 还会安装 numpy（如果尚未安装的话）。
### 卸载包
在终端中键入 ：

	conda remove package_names
	
上面命令中的package_names是指你要卸载包的名称，例如你想卸载pandas包：conda remove pandas
### 更新包
在终端中键入：

	conda update package_name
	
如果想更新环境中的所有包（这样做常常很有用），使用：conda update --all。
### 列出已安装的包

	#列出已安装的包
	conda list
	
例如我已经成功安装了numpy和pandas这两个常用的包。
{% img /images/python/44_6.jpg %}
如果不知道要找的包的确切名称，可以尝试使用 conda search search_term 进行搜索。例如，我知道我想安装numpy，但我不清楚确切的包名称。我可以这样尝试：conda search num。
## 如何管理环境？
conda 可以为你不同的项目建立不同的运行环境。
>在这里我必须提一下，我在关联环境的时候出现错误，具体体现在我创建了虚拟环境，但是在 file 页面的右边 new 选项中，我却没有看见虚拟环境。
我按照下面的步骤做了一下，我不知道哪一步是正确的，但是确实有用。。。
第一步，我在最原始的环境（不是虚拟机的环境）安装了 nb_conda
第二步，我在对应的虚拟环境下也安装了上面的包。
第三步，刷新浏览器还是不行，于是，我重启服务器（Jupyter），然后关闭浏览器，重新打开，最后看见了。

安装nb_conda用于notebook自动关联nb_conda的环境。
{% img /images/python/44_7.jpg %}
### 创建环境
在终端中使用:

	conda create -n env_name package_names
	
上面的命令中，env_name 是设置环境的名称（-n 是指该命令后面的env_name是你要创建环境的名称），package_names 是你要安装在创建环境中的包名称。
默认的安装路径是在 envs 下面，比如我的路径是

	E:\anaconda\anaconda3.7\envs
	
例如，要创建环境名称为 py3 的环境并在其中安装 numpy，在终端中输入 conda create -n py3 pandas。
{% img /images/python/44_8.jpg %}
### 创建环境时，可以指定要安装在环境中的 Python 版本
当你同时使用 Python 2.x 和 Python 3.x 中的代码时这很有用。要创建具有特定 Python 版本的环境，例如创建环境名称为py3，并安装最新版本的Python3在终端中输入：

	conda create -n py3 python=3 
	
或也可以这样创建环境名称为py2，并安装最新版本的Python2：

	conda create -n py2 python=2
	
因为我做的项目不同，有时候会用到Python2，还有时候会用到Python3。所以我在自己的计算机上创建了这两个环境，并分别取了这样的环境名称：py2,py3。这样我可以根据不同的项目轻松使用不同版本的python。
如果你要安装特定版本（例如 Python 3.6），请使用 conda create -n py python=3.6
### 进入环境
用 conda env list 就可以列出你创建的所有环境。
在 Windows 上，你可以使用 activate my_env进入。在 OSX/Linux 上使用 source activate my_env 进入环境。
进入环境后，你会在终端提示符中看到环境名称，下面图片是我进入py3的环境（这里的py3是我上面创建环境时自己起的名称，你可以起个自己喜欢的名称）。
{% img /images/python/44_9.jpg %}
进入环境后，我可以用conda list 查看环境中默认安装的几个包：
{% img /images/python/44_10.jpg %}
在环境中安装包的命令与前面一样：conda install package_name。
不过，这次你安装的特定包仅在你进入环境后才可用。
### 离开环境
在 Windows 上，终端中输入：

	deactivate
	
在 OSX/Linux 上 输入：

	source deactivate
	
### 共享环境
共享环境非常有用，它能让其他人安装你的代码中使用的所有包，并确保这些包的版本正确。比如你开发了一个药店数据分析系统，你要提交给项目部署系统的王二狗来部署你的项目，但是王二狗并不知道你当时开发时使用的是哪个python版本，以及使用了哪些包和包的版本。这怎么办呢？
你可以在你当前的环境中终端中使用 conda env export > environment.yaml 将你当前的环境保存到文件中包保存为YAML文件（包括Pyhton版本和所有包的名称）。
命令的第一部分 conda env export 用于输出环境中的所有包的名称（包括 Python 版本）。
{% img /images/python/44_11.jpg %}
在“notebook工作文件夹”下（及你在终端中上图的路径）可以看到导出的环境文件：
{% img /images/python/44_12.jpg %}
在 GitHub 上共享代码时，最好同样创建环境文件并将其包括在代码库中。这能让其他人更轻松地安装你的代码的所有依赖项。
导出的环境文件，在其他电脑环境中如何使用呢？
首先在conda中进入你的环境，比如activate py3
然后在使用以下命令更新你的环境：

	#其中-f表示你要导出文件在本地的路径，所以/path/to/environment.yml要换成你本地的实际路径
	conda env update -f=/path/to/environment.yml
	
对于不使用 conda 的用户，我通常还会使用 pip freeze > environment.txt 将一个 txt文件导出并包括在其中。
[文档说明](https://pip.pypa.io/en/stable/reference/pip_freeze/)
举个例子你可能更容易理解这个使用场景：
首先，我在自己的电脑上在conda中将项目的包导出成environment.txt 文件：
{% img /images/python/44_13.jpg %}
然后我将该文件包含在项目的代码库中，其他项目成员即使在他的电脑上没有安装conda也可以使用该文件来安装和我一样的开发环境：
他在自己的电脑上进入python命令环境，然后运行以下命令就可以安装该项目需要的包：

	pip install -r /path/requirements.txt
	
其中/path/requirements.txt是该文件在你电脑上的实际路径。
{% img /images/python/44_14.jpg %}
### 列出环境
我有时候会忘记自己创建的环境名称，这时候用 conda env list 就可以列出你创建的所有环境。
你会看到环境的列表，而且你当前所在环境的旁边会有一个星号。默认的环境（即当你不在选定环境中时使用的环境）名为 root。
{% img /images/python/44_15.jpg %}
### 删除环境
如果你不再使用某个环境，可以使用 conda env remove -n env_name 删除指定的环境（在这里环境名为 env_name）。
最后重新再强调下，不要被上面的命令吓到。虽然上述命令多，给了谁都记不住的。后面你跟着我在知乎上多做项目，用的多了自然记住了。你只需要跟着上面步骤操作下，并理解了每一步是干什么的就可以了。后面遇到要做的事情，忘记了回头查这个文档就可以了。




























