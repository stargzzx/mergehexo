---
title: linux | 安装软件相关的命令
date: 2020-01-09 18:09:01
categories:
- [linux,基础]
tags:
- apt-get
- linux
- dpkg
- apt
mathjax: true
---
linux 如何安装软件，每次安装软件执行的命令是怎么回事？这篇博文告诉你答案。

<!-- more -->

<br/>

# 什么是 apt-get?

<br/>

Ubuntu源自Debian Linux。Debian使用dpkg打包系统。包装系统是一种为安装提供程序和应用程序的方法。这样，您就不必从源代码构建程序。

APT（高级软件包工具）是与此打包系统交互的命令行工具。已经有dpkg命令来管理它。但apt更适合处理包装。您可以使用它来查找和安装新软件包，升级软件包，清理软件包等。

它有两个主要工具：apt-get和apt-cache。apt-get用于安装，升级和清理包，而apt-cache用于查找新包。我们将在本指南后面的示例中看到所有这些命令。

<br/>

# dpkg和apt-get区别

<br/>

一般来说著名的 Linux 系统基本上分两大类：

	RedHat 系列：Redhat、Centos、Fedora 等
	Debian 系列：Debian、Ubuntu 等

Dpkg (Debian系)：Ubuntu

RPM (Red Hat系)：CentOS、Fedora

## RedHat 系列

	常见的安装包格式 rpm 包，安装rpm包的命令是“rpm -参数”
	包管理工具 yum
	支持 tar 包

## Debian系列

	常见的安装包格式 deb 包，安装 deb 包的命令是“dpkg -参数”
	包管理工具 apt-get
	支持 tar 包

两者的区别：dpkg绕过apt-get包管理数据库直接对软件包进行操作

所以用dpkg安装过的软件包可以用apt-get可以再安装一遍，系统不知道之前已经安装过了，将会覆盖之前dpkg的安装。

dpkg和apt-get：

dpkg:用来安装.deb文件时，不会解决模块的依赖关系，且不会关心ubuntu的软件仓库内的软件，可以用于安装本地的deb文件。

apt-get:会解决和安装模块的依赖问题，并会咨询软件仓库，但不会安装本地的deb文件，apt-get是建立在dpkg之上的软件管理工具。

<br/>

# apt 和 apt-get 的区别

<br/>

Ubuntu 16.04 发布时，一个引人注目的新特性便是 apt 命令的引入。其实早在 2014 年，apt 命令就已经发布了第一个稳定版，只是直到 2016 年的 Ubuntu 16.04 系统发布时才开始引人关注。

随着 apt install package 命令的使用频率和普遍性逐步超过 apt-get install package，越来越多的其它 Linux 发行版也开始遵循 Ubuntu 的脚步，开始鼓励用户使用 apt 而不是 apt-get。

那么，apt-get 与 apt 命令之间到底有什么区别呢？如果它们有类似的命令结构，为什么还需要新的 apt 命令呢？是否 apt 真的比 apt-get 更好？普通用户应该使用新的 apt 命令还是坚持旧有习惯继续使用 apt-get 呢？

## apt与apt-get

在开始对比 apt 与 apt-get 命令的区别之前，我们先来看看这两个命令的背景，以及它们要试图达到的目的。

Debian 作为 Ubuntu、Linux Mint 和 elementary OS 等 Linux 操作系统的母板，其具有强健的「包管理」系统，它的每个组件和应用程序都内置在系统中安装的软件包中。Debian 使用一套名为 Advanced Packaging Tool（APT）的工具来管理这种包系统，不过请不要把它与 apt 命令混淆，它们之间是其实不是同一个东西。

在基于 Debian 的 Linux 发行版中，有各种工具可以与 APT 进行交互，以方便用户安装、删除和管理的软件包。apt-get 便是其中一款广受欢迎的命令行工具，另外一款较为流行的是 Aptitude 这一命令行与 GUI 兼顾的小工具。

如果你已阅读过我们的 apt-get 命令指南，可能已经遇到过许多类似的命令，如apt-cache、apt-config 等。如你所见，这些命令都比较低级又包含众多功能，普通的 Linux 用户也许永远都不会使用到。换种说法来说，就是最常用的 Linux 包管理命令都被分散在了 apt-get、apt-cache 和 apt-config 这三条命令当中。

apt 命令的引入就是为了解决命令过于分散的问题，它包括了 apt-get 命令出现以来使用最广泛的功能选项，以及 apt-cache 和 apt-config 命令中很少用到的功能。

在使用 apt 命令时，用户不必再由 apt-get 转到 apt-cache 或 apt-config，而且 apt 更加结构化，并为用户提供了管理软件包所需的必要选项。

>简单来说就是：apt = apt-get、apt-cache 和 apt-config 中最常用命令选项的集合。

## apt与apt-get之间的区别

通过 apt 命令，用户可以在同一地方集中得到所有必要的工具，apt 的主要目的是提供一种以「让终端用户满意」的方式来处理 Linux 软件包的有效方式。

apt 具有更精减但足够的命令选项，而且参数选项的组织方式更为有效。除此之外，它默认启用的几个特性对最终用户也非常有帮助。例如，可以在使用 apt 命令安装或删除程序时看到进度条。

apt 还会在更新存储库数据库时提示用户可升级的软件包个数。

如果你使用 apt 的其它命令选项，也可以实现与使用 apt-get 时相同的操作。

## apt和apt-get命令之间的区别

虽然 apt 与 apt-get 有一些类似的命令选项，但它并不能完全向下兼容 apt-get 命令。也就是说，可以用 apt 替换部分 apt-get 系列命令，但不是全部。


|apt 命令|取代的命令|命令的功能|
|---|---|---|
|apt install|  apt-get install|  安装软件包|
|apt remove|  apt-get remove|  移除软件包|
|apt purge|  apt-get purge|  移除软件包及配置文件|
|apt update|  apt-get update|  刷新存储库索引|
|apt upgrade|  apt-get upgrade|  升级所有可升级的软件包|
|apt autoremove|  apt-get autoremove|  自动删除不需要的包|
|apt full-upgrade|  apt-get dist-upgrade|  在升级软件包时自动处理依赖关系|
|apt search|  apt-cache search|  搜索应用程序|
|apt show|  apt-cache show|  显示装细节|

当然，apt 还有一些自己的命令：

|新的apt命令| 命令的功能|
|---|---|
|apt list|  列出包含条件的包（已安装，可升级等）|
|apt edit-sources|  编辑源列表|

需要大家注意的是：apt 命令也还在不断发展， 因此，你可能会在将来的版本中看到新的选项。

## apt-get已弃用？

目前还没有任何 Linux 发行版官方放出 apt-get 将被停用的消息，至少它还有比 apt 更多、更细化的操作功能。对于低级操作，仍然需要 apt-get。

## 我应该使用apt还是apt-get？

既然两个命令都有用，那么我该使用 apt 还是 apt-get 呢？作为一个常规 Linux 用户，系统极客建议大家尽快适应并开始首先使用 apt。不仅因为广大 Linux 发行商都在推荐 apt，更主要的还是它提供了 Linux 包管理的必要选项。

最重要的是，apt 命令选项更少更易记，因此也更易用，所以没理由继续坚持 apt-get。

<br/>

# dpkg 的用法

<br/>

## 安装软件包

|dpkg命令| 描述|
|---|---|
|dpkg -i package_name.deb|  安装本地软件包，不解决依赖关系|
|apt-get install package|  在线安装软件包|
|aptitude install pattern|  同上|
|apt-get install package --reinstall|  重新安装软件包|
|apitude reinstall package|  同上|
|dpkg -R /usr/local/src|  安装一个目录下面所有的软件包|

## 移除软件包

|dpkg命令| 描述|
|---|---|
|dpkg -r package|  删除软件包|
|dpkg -P|  删除软件包及配置文件|
|apt-get autoremove |  自动移除不再需要的软件包| 

## 其他

|dpkg命令|描述|
|---|---|---|
|dpkg –help|  显示帮助信息|
|dpkg –version|  显示dpkg的版本号|
|dpkg -l package-name-pattern|  搜索Deb包(例子：dpkg -I vim)|
|dpkg -L package-Name|  显示一个包安装到系统里面的文件目录信息|

很多人抱怨用了Ubuntu或者Debian以后，不知道自己的软件给安装到什么地方了。其实可以用上面的dpkg -L命令来方便的查找。看来基础还是非常重要的，图形界面并不能够包办一切。

<br/>

# apt-get的使用方法

<br/>

## 使用apt-get更新包数据库

apt-get基本上适用于可用包的数据库。如果不更新此数据库，系统将不知道是否有更新的软件包可用。实际上，这是在全新安装后需要在任何Linux系统中运行的第一个命令。

更新包数据库需要超级用户权限，因此您需要使用sudo。

	sudo apt-get update

运行此命令时，您将看到从各种服务器检索的信息。

![](/images/linux/7_0.jpg)

你会在这里看到三种类型的线，hit，get和ign。让我向你解释一下：

hit:包版本没有变化

ign：包被忽略了。可能有各种原因。这个包太新了以至于它甚至都懒得检查，或者检索文件时出错，但错误是微不足道的，因此它被忽略了。这不是错误。没有必要担心。

get:有一个新版本可用。它将下载信息（而不是包本身）。您可以在上面的屏幕截图中看到有“get”行的下载信息。

## 使用apt-get升级已安装的软件包

更新软件包数据库后，可以升级已安装的软件包。最方便的方法是升级所有可用更新的软件包。您可以使用以下命令来实现此目的：

	sudo apt-get upgrade

要仅升级特定程序，请使用以下命令：

	sudo apt-get upgrade <package_name>

还有另一种方法可以使用以下命令提供完整的升级：

	sudo apt-get dist-upgrade

这实际上是查找新版本的依赖项并尝试安装它。但你应该避免使用它。我将在下一节中解释它。

## 升级和dist-upgrade之间的区别

命令apt-get upgrade非常听话。它永远不会尝试删除任何包或尝试自己安装新包。

另一方面，命令apt-get dist-upgrade是主动的。它会查找正在安装的较新版本软件包的依赖项，并尝试安装新软件包或自行删除现有软件包。

听起来像dist-upgrade更强大，更聪明，不是吗？但它存在风险。

看，它有一个“智能”冲突解决系统。有了它，它将尝试升级最重要的包，而不是那些不太重要的包。这可能会导致您删除一些您可能不想要的包。这是在生产机器上应该避免dist-upgrade的主要原因。

## apt-get update和apt-get升级有什么区别？

这是一个非常普遍的混乱。您不是唯一一个被术语更新和升级混淆的人。

虽然听起来像是在进行apt-get更新时，但它会更新软件包。但事实并非如此。apt-get update仅更新包的数据库。例如，如果安装了XYX软件包版本1.3，则在apt-get update之后，数据库将知道有更新的版本1.4可用。

当您在apt-get update之后进行apt-get升级时，它会将已安装的软件包升级（或更新，无论您喜欢哪个术语）到新版本。

这就是为什么更新Ubuntu的最快和最方便的方法是使用此命令：

	sudo apt-get update && sudo apt-get upgrade -y

## 使用apt-cache命令搜索包

我会跟你说实话。这不是我搜索包的首选方式。但是当你在寻找一些特定的lib时，这非常方便。

您需要做的就是使用以下命令。你甚至不需要sudo。

	apt-cache search <search term>

![](/images/linux/7_1.jpg)

您无需知道包的确切名称。它会搜索包名称及其简短描述，并根据该结果显示结果。

如果您只想搜索具有特定包名称的包，可以使用以下命令：

	apt-cache pkgnames <search_term>

这将为您提供以搜索词开头的所有包的列表。

![](/images/linux/7_2.jpg)

一旦知道确切的包名，就可以使用以下命令获取有关它的更多信息，例如版本，依赖关系等：

	apt-cache showpkg <package_name>

## 如何使用apt-get安装新软件包

如果您知道包的名称，可以使用以下命令轻松安装它：

	sudo apt-get install <package_name>

只需将<package_name>替换为所需的包。假设我想安装Pinta图像编辑器，我需要做的就是使用以下命令：

	sudo apt-get install pinta

这个命令的好处是它有自动完成功能。因此，如果您不确定确切的包名称，可以键入几个字母并按Tab键，它会建议所有包含这些字母的包。例如：

![](/images/linux/7_3.png)

## 如何安装多个包

	sudo apt-get install <package_1> <package_2> <package_3>

## 如果在已安装的软件包上运行install，该怎么办？

假设您已经安装了一个软件包，但是您已经为已安装的软件包使用了install命令。这实际上会查看数据库，如果找到更新的版本，它会将已安装的软件包升级到更新的软件包。因此，除非您不希望升级，否则使用此命令不会造成任何损害。

## 如何在不升级的情况下安装包

假设由于某种原因你想要安装一个软件包但是如果已经安装了它就不想升级它。听起来很奇怪，但你可能有理由这样做。

对于这种情况，您可以按以下方式使用sub -no-upgrade：

	sudo apt-get install <package_name> --no-upgrade

## 如何只升级包，而不是安装它

如果您只想升级包但不想安装它（如果尚未安装），则可以使用以下命令执行此操作：

	sudo apt-get install <package_name> --only-upgrade

## 如何安装特定版本的应用程序

默认情况下，将为任何应用程序安装存储库中可用的最新版本。但由于某些原因，如果您不想安装最新版本，则可以指定版本号（您需要知道要安装的确切版本号）。

您需要做的就是添加带有包名称的= version。

	sudo apt-get install <package_name>=<version_number>

## 如何使用apt-get删除已安装的软件包

这不是你只能用apt-get安装软件包。您也可以使用它删除包。您需要做的就是以这种方式使用命令：

	sudo apt-get remove <package_name>

自动完成也适用于此处。所以你只需要开始输入包名称并按Tab键，它将建议所有已安装的包以这些字母开头。

卸载软件包的另一种方法是使用清除。该命令以下列方式使用：

	sudo apt-get purge <package_name>

## apt-get remove和apt-get purge有什么区别？

	apt-get remove只删除包的二进制文件。它不会触及配置文件
	apt-get purge删除与包相关的所有内容，包括配置文件

因此，如果您“删除”特定软件并再次安装，您的系统将具有相同的配置文件。当然，再次安装时，系统会要求您覆盖现有的配置文件。

当你搞砸了程序的配置时，清除特别有用。您希望从系统中完全擦除其痕迹，并且可能重新开始。

大多数情况下，简单删除对于卸载软件包来说已经足够了。

## 如何使用apt-get清理系统

哦，是的！您还可以使用apt-get清理系统并释放一些磁盘空间。

您可以使用以下命令清除检索到的包文件的本地存储库：

	sudo apt-get clean

另一种方法是使用autoclean。与上面的clean命令不同，autoclean只删除那些现在有更新版本的检索包文件，它们将不再使用。

	sudo apt-get autoclean

另一种释放磁盘空间的方法是使用autoremove。它会删除自动安装的lib和软件包，以满足已安装软件包的依赖关系。如果删除了包，则这些自动安装的包在系统中是无用的。此命令删除此类包。

	sudo apt-get autoremove
