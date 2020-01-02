---
title: ubuntu 的安装
date: 2019-01-19 03:05:19
categories:
- ubuntu
tags:
- ubuntu
---
我安装 ubuntu 安装了 7，8次，在后续的安装过程中，均成功，所以，记录一下过程。OK，大前提，我是 GPT + UEFI。
<!--more-->
## 参考资料
[Ubuntu 16.04与Win10双系统双硬盘安装图解](https://blog.csdn.net/fesdgasdgasdg/article/details/54183577)
[U盘做成Mac启动盘之后怎么恢复成原来的U盘（U盘变成efi怎么恢复）](https://blog.csdn.net/chengtaoyan/article/details/64441715)
[win10如何安装Ubuntu 18.04 LTS](https://jingyan.baidu.com/article/e2284b2b97e821e2e6118dff.html)
[如何制作Ubuntu 18.04 LTS安装启动U盘](https://jingyan.baidu.com/article/ce4366494448fd3773afd306.html)
[实践Windows下安装Ubuntu双系统](https://baijiahao.baidu.com/s?id=1609584979028311540&wfr=spider&for=pc&isFailFlag=1)
[win7+ubuntu 13.04双系统安装方法](https://jingyan.baidu.com/article/60ccbceb18624464cab197ea.html)
## 环境介绍

	win10
	system：ubuntu-18.04.1-desktop-amd64
	AMAX工作站
	1块 500 G 的固态
	1块 2T 的机械
	
## 了解你的电脑是 UEFI+GPT 与 BIOS+MBR
这两个的安装方式和引导都是不一样的，如果安装错了，后期就会出现很多 BUG。
[UEFI+GPT与BIOS+MBR区别](https://benpaodewoniu.github.io/)
经过查询我的系统是 UEFI+GPT 。
所以，我们一定要用 Rufus 制作系统盘。
## 制作系统盘
下载 Rufus。
下载 ubuntu-18.04.1-desktop-amd64
进行制作
{% img /images/ubuntu/0_0.png %}
{% img /images/ubuntu/0_1.png %}
{% img /images/ubuntu/0_2.png %}
{% img /images/ubuntu/0_3.png %}
## 给 ubuntu 划分空间
打开windows的“磁盘管理”工具。
我的固态一开始就为 ubuntu 划分了一个区。我的想法是固态分区用来装 windows 和 Ubuntu 。
所以，在装之前，我要确保我的磁盘 0 上有未分配的分区。
如果，你没有为 ubuntu 进行保留分区的话，那么你就要在磁盘 0 上压缩卷（将没有用过的空间拿出来给 ubuntu 用）
网上找的图。
{% img /images/ubuntu/0_6.png %}
另外，值得注意的是，我们要记住这个未分配的分区大小，到后期我们会用到。
下面这张图是我装了 ubuntu 之后的分区样子。
{% img /images/ubuntu/0_7.png %}
图中黄色区域就是我安装的 ubuntu 。我在安装 ubuntu 的时候，划了四个区。当然，这是后话。
但是，令人悲哀的是，我们可能在第一次安装 ubuntu 并不会成功，所以，我们要连续很多次安装。
那么重新安装之间，只需要把上面的黄色区域依次进行 删除卷 的操作，就能将他们合并成一个未分配的卷了。
（忽略我固态的分区样子，说多了都是泪，我的分区是不合理的，正确的分区是固态只安装 win 和 ubuntu）
## 安装 ubuntu
我用的是 AMAX 工作站，所以在开机的时候要按 F11 。
在这里我们要注意的是，传统系统安装都是 USB 启动，但是，由于我们的引导项是 UEFI ，所以哦我们要通过 UEFI 启动。
我的 AMAX 安装界面。
{% img /images/ubuntu/0_4.jpg %}
另一台电脑的安装界面。
{% img /images/ubuntu/0_5.jpg %}
如果你从 usb 那个选项启动的话，你会遇见：
{% img /images/ubuntu/0_8.jpg %}
进入启动界面，选择try ubuntu试用，不要选择立即安装。
因为试用时，可以操作ubuntu，打开里面的浏览器查阅资料等。立即安装后面的流程，是操作不了ubuntu系统的。图：
{% img /images/ubuntu/0_9.png %}
进入系统后，你可以先玩玩，点击桌面右上角联网，点击左侧启动器里面的火狐浏览器上网。
点击桌面左上角的“安装 Ubuntu 16.04 LTS”启动安装程序，选择界面语言，继续：
{% img /images/ubuntu/0_10.png %}
键盘布局直接选择英语(美国)就好，因为我们的键盘标准大都是美国标准。
继续：
这里我们选择最小安装，这个是ubuntu 18.04新提供的功能
然后把最下面为图像和无线硬件安装第三方软件打上勾
最小安装包含浏览器和基本工具
正常安装包含以上的东西和办公软件、游戏、多媒体播放器等
不过，ubuntu默认的办公软件、游戏、多媒体播放工具等都不太适合国人的使用，所以这里不建议默认的，我比较喜欢自己安装，甚至你也可以不勾选 为图像和无线硬件安装第三方软件
{% img /images/ubuntu/0_11.png %}
这一步很关键，选择错了位置，会毁掉你windows系统上所有的数据。
选择“其他选项”，使用之前预留的独立区域来安装ubuntu系统，继续：
{% img /images/ubuntu/0_12.png %}
上面的其他选项就是我们有意识的 ubuntu 安装到我们所划分的区。
选中“空闲”区域，核对一下大小对不对，点击左下角的加号创建分区，若创建错了就点减号删除。
若之前在windows系统里面，你没有按照我说的删除预留分区，则在此处就难以辨认是哪一个了。此处选择错了分区后果你懂得。
{% img /images/ubuntu/0_13.png %}
我们一般是划分四个区。
### /
挂载点：“/”
大小：22000MB
新分区的类型：主分区
新分区的位置：空间起始位置
用于：EXT4日志文件系统
第一次分区完毕
{% img /images/ubuntu/0_14.png %}
### 交换空间
挂载点：（不设置）
大小：10240MB（网上认为和物理内存大小应该一样）（因为我的是 32G 内存，但是，还有一种观点就是如果内存足够大，那么这个是不需要的，但是为了稳妥我还是弄了）
新分区的类型：逻辑分区
新分区的位置：空间起始位置
用于：交换空间
第二次分区完毕
{% img /images/ubuntu/0_15.png %}
### /boot
挂载点：/boot  （网上有的说不需要设置这项，但双系统引导时需要，先不要去理解这些）
大小：1024MB
新分区的类型：逻辑分区
新分区的位置：空间起始位置
用于：EXT4日志文件系统
第三次分区完毕
{% img /images/ubuntu/0_16.png %}
另外，这个分区有一点要特别注意，就是网上分区一般都是 200M ，但是在 18.04 中 200M 的内存空间时会出错的，会出现下面的错误：
{% img /images/ubuntu/0_17.jpg %}
这是因为 boot 的空间不足，所以不能够删除成功。建议给 1G
### /home 
大小：（剩余全部空间，剩下显示多少，就多少）
新分区的类型：逻辑分区
新分区的位置：空间起始位置
用于：EXT4日志文件系统
{% img /images/ubuntu/0_18.png %}
分区设置完毕后，下方还有一项“安装启动引导器的设备”，默认就如下图所示那样，也是网友们所说的sda，如果选择默认，则是ubuntu引导windows7，到时候卸载ubuntu时，可能会麻烦些，如果想用windows7引导ubuntu，请选择你/boot所在的盘符，之后还要在windows下安装引导类的软件，才能启动ubuntu，过程会复杂些。这一步骤请大家慎重选择，笔者选择的默认选项，以后产生什么问题，以后再说吧。
{% img /images/ubuntu/0_19.png %}
我也选择的默认。之前尝试过其他的，但是都失败了，所以，之后我选择的全都是默认。
之后就按照提示来就可以了，没什么难度了。
全部安装之后，第一次开机会出现
{% img /images/ubuntu/0_21.png %}
## 其他注意事项
这些注意事项的前提是你是 UEFI + GPT
### 使用 EasyBCD 来添加引导项
按照上面的安装步骤的话，是用 ubuntu 来引导 win ，如果用 win 来引导 ubuntu 的话，则需要 EasyBCD ，但是，由于我们是的启动方式是 UEFI ，所以，用 EasyBCD 会失败，甚至选择 linux 的时候就是灰色。
这个时候，有一部分人就会选择 EasyBCD2.2 来解决灰色不可选问题，虽然，你最后能在 2.2 版本看见 linux ，并且加入启动项，但是在启动选择的时候还是会出错，当然，如果你按照上面的步骤安装就不会有这个问题。
但是，假如你实在是想通过 win 来引导 ubuntu ，那么你需要 EasyUEFI 来创建引导。教程请自行谷歌。
### 非要用 USB 来启动
假设，你他妈的就是不听劝，非要用 UltraISO 和 USB 启动来安装 ubuntu18.04 ，那么你需要在写入磁盘的写入方式中选择 RAW 方式。
{% img /images/ubuntu/0_20.png %}
选择通过 USB
{% img /images/ubuntu/0_4.jpg %}
但是，我有个疑问，你他妈为什么不听劝，非要用这个方式来安装 ubuntu 。
### 将 U 盘还原
当你用上面的这个方式（注意事项中的制作方式）来制作系统盘的时候，你就会发现你的优盘变成了大概 3M 左右，假设你原来有 30G。
但是，做完系统，我们需要还原 U 盘。
所以，请按照下面的步骤：
以管理员身份运行命令提示行，输入diskpart，回车，然后list disk，显示电脑连接的驱动器清单，记住优盘前面的编号，比如是1，输入select disk 1，选择优盘，然后输入clean，清空优盘，退出命令提示行，右键点击桌面上的计算机--管理--磁盘管理，右键点击优盘，进行分区和格式化就可以了。
所以，为什么不按照正确的方式来呢？









