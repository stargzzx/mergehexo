---
title: matlab安装，以及安装eeglab
date: 2019-01-26 14:10:02
categories:
- matlab
tags:
- amtlab
- eeglab
- 脑电信号
- eeg
---
如题。
<!-- more -->
## 下载链接
公众号：软件安装管家
[Download EEGLAB zip file (requires Matlab)](https://sccn.ucsd.edu/eeglab/downloadtoolbox.php)
## 参考资料
[EEGLAB 使用记录](https://blog.csdn.net/missxy_/article/details/81669880)
## 版本 2018a
下载下来之后我安装 eeglab 总是出错，如下图
{% img /images/matlab/0_0.png %}
但是由于 matlab 的语法我不会，所以，我不知道如何修复，经过询问师姐后，他们用的是 16a ，没有问题
## 版本 16a
安装了 16 a 版本之后，经过检验确实可以。
## 调用 matlab 命令行
首先我们要将 matlab 的路径添加到环境变量中去，我的路径如下

	D:\matlab\bin
	
调用 matlab 的命令行，用一下命令

	matlab -nodesktop
	
## 安装 eeglab
>To install EEGLAB
1.Unzip the EEGLAB zip file in the folder of your choice
2.Start Matlab
3.Change the Matlab path to the EEGLAB folder you have just uncompressed
4.Type "eeglab" and press enter on the Matlab prompt

上面的意思就是说明，先下载 eeglab 到文件夹下，并解压，然后把相关的路径添加进 matlab 中。
{% img /images/matlab/0_1.png %}
{% img /images/matlab/0_2.png %}
上述步骤做完之后，就可以用命令行的方式调用 eeglab 。
即进入 matlab 命令行，输入

	eeglab
	
出现下面这幅图就证明成功了。
{% img /images/matlab/0_3.png %}
## 制作相关的数据，检验
### 制作数据集

	% 32通道 采样率256Hz 100秒
	eegdata=rand(32,256*100);
	save eegdata.mat eegdata;

导入eegdata.mat，操作：

	File > Import data > from ascii/float file or Matlab array 
	
在数据文件/数组中，单击列表中的选项Matlab变量，并将名称设置为eegdata。将采样频率设置为256 Hz，然后按OK。其他数据集参数将自动调整。 
{% img /images/matlab/0_4.jpg %}
点击Plot > Channel data(scroll) 绘制EEG信号界面。 
{% img /images/matlab/0_5.png %}
### 预处理 滤波
点击

	Tools > Filter the data > Basic FIR filter(new,default) 

比如设置频率通带的下边缘5（Hz）和频率通带的较高边沿30（Hz），点击OK。 
{% img /images/matlab/0_6.png %}
再次点击Plot > Channel data(scroll) 绘制EEG信号界面。 
{% img /images/matlab/0_7.png %}






























