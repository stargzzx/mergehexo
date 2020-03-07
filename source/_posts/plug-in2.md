---
title: 直接用 python 控制手机,通过 USB 连接方式
date: 2019-03-11 10:15:57
categories:
- plug_in
tags:
- plug_in
- python
- airtest
- popc
- 外挂
- 手机外挂
---
如题

<!-- more -->

## 参考资料

[单独使用Python控制手机](https://juejin.im/post/5c42fd6251882525153c325a)

## 单独使用Python控制手机

在Airtest操作手机虽然方便，但是不可能在每一台电脑上都安装Airtest吧。所以需要想办法把代码从Airtest这个程序中分离出来。

Airtest基于Python的一个开源库Poco开发，而在Airtest的B区写的Python代码，实际上就是Poco的代码。所以只要安装Poco库，就可以在Python中直接控制手机。

安装Poco库的命令为：

	pip install pocoui
	
这个库依赖的东西有点多，安装稍稍慢一些。安装完成以后，我们把代码复制到PyCharm中，如下图所示。

{% img /images/plug_in/2_0.png %}

运行这段代码，如果是Linux或者macOS的用户，请注意看运行结果是不是有报错，提示adb没有运行权限。这是因为随Poco安装的adb没有运行权限，需要给它添加权限，在终端执行命令：

	# chmod +x 报错信息中给出的adb地址
	chmod +x /Users/kingname/.local/share/virtualenvs/ZhihuSpider/lib/python3.7/site-packages/airtest/core/android/static/adb/mac/adb(实际执行时请换成你的地址)

命令运行完成以后再次执行代码，可以看到代码运行成功，手机被成功控制了，如下图所示。

{% img /images/plug_in/2_1.png %}

## 如何获取屏幕文字

由于Airtest的编辑器中的代码运行后无法正常打印出中文，因此后面的代码都直接在PyCharm中执行。

既然要做爬虫，就需要获取手机上的文字内容。回到搜索页面，我想知道“古剑奇谭”三这个关键字能搜索出多少条结果，每条结果有多少个讨论，如下图所示：

{% img /images/plug_in/2_2.png %}

此时我们需要做两件事情：

	分别查看每一个搜索结果
	获取屏幕上的文字
	
E区的树状结构如下图所示：

{% img /images/plug_in/2_3.png %}

每一个搜索结果的标题作为text属性的值，在name='com.zhihu.android:id/magi_title'对应的元素中；每一个搜索结果的讨论数作为text属性的值，在name='com.zhihu.android:id/magi_count'对应的元素中。

最直接的做法就是分别获取三个标题和三个讨论数，然后把它们合并在一起：

	title_obj_list = poco(name='com.zhihu.android:id/magi_title')
	title_list = [title.get_text() for title in title_obj_list]
	discuss_obj_list = poco(name='com.zhihu.android:id/magi_count')
	discuss_list = [discuss.get_text() for discuss in discuss_obj_list]
	for title, discuss in zip(title_list, discuss_list):
		print(title, discuss)
		
运行效果如下图所示：

{% img /images/plug_in/2_4.png %}

但是这种做法实际上是很危险的，假设会有某一个很生僻的搜索结果，只有标题没有讨论数，那么这样分开抓取再组合的做法，就会导致最后匹配错位。所以合理的做法是先抓大再抓小。每一组标题和讨论数，他们都有自己的父节点，如下图箭头所指向的三个android.widget.LinearLayout:

{% img /images/plug_in/2_5.png %}

那么现在，使用先抓大再抓小的技巧，先把每一组结果的父节点抓下来，再到每一个结果里面分别获取标题和讨论数。

然而这个父节点又怎么获取呢？如下图所示，这个父节点每一个属性值都没有什么特殊的，写任何一个都有可能与别的节点撞上。

{% img /images/plug_in/2_6.png %}

此时，最简单的办法，就是在E区，双击父节点。定位代码就会自动添加，如下图所示。

{% img /images/plug_in/2_7.png %}

这个定位代码看起来非常复杂，但实际上它的内在逻辑非常简单，就是从顶层一层一层往下找而已。

自动生成的定位代码如下：

	poco("android.widget.LinearLayout").offspring("com.zhihu.android:id/action_bar_root").offspring("com.zhihu.android:id/parent_fragment_content_id").offspring("android.support.v7.widget.RecyclerView").child("android.widget.LinearLayout")[0]
	
在这个自动生成的定位代码中，我们看到了offspring、child这两种方法。其中child代表子节点，offspring代表孙节点、孙节点的子节点、孙节点的孙节点……。简言之，使用child只会在子节点中搜索需要的内容，而使用offspring会像文件夹递归一样把里面的所有节点都遍历一次，直到找到符合条件的属性为止。显然，offspring速度会比child慢。

实际上，我们可以对这个定位代码做一些精简：

	poco("com.zhihu.android:id/parent_fragment_content_id").offspring("android.support.v7.widget.RecyclerView").child("android.widget.LinearLayout")[0]

这个精简的方法，与从Chrome复制的XPath中进行精简是一样的逻辑，根本原则就是找到“独一无二”的属性值，然后用这个属性值来进行定位。

由于我点击的是第一个搜索结果，所以定位代码的最后有一个[0]。现在由于需要获得所有搜索结果的内容，所以应该去掉[0]而使用for循环展开，然后获取里面的内容：

	result_obj = poco("com.zhihu.android:id/parent_fragment_content_id").offspring("android.support.v7.widget.RecyclerView").child("android.widget.LinearLayout")
	for result in result_obj:
		title = result.child(name='com.zhihu.android:id/magi_title').get_text()
		count = result.child(name='com.zhihu.android:id/magi_count').get_text()
		print(title, count)

运行效果如下图所示。

{% img /images/plug_in/2_8.png %}

## 控制多台手机

当我们在电脑上插入多个Android手机时，执行命令：

	adb devices -l
	
运行效果如下图所示。

{% img /images/plug_in/2_9.png %}

每个手机都会被列出来。在最左边的编号就是手机串号。使用这个串号可以指定多个手机：

	from airtest.core.api import auto_setup
	from airtest.core.android import Android
	from poco.drivers.android.uiautomation import AndroidUiautomationPoco
	auto_setup(__file__)
	device_1 = Android('76efadf3a7ce4')
	device_2 = Android('adfasdfasf23')
	device_3 = Android('adifu39ernla')
	poco_1 = AndroidUiautomationPoco(device_1, use_airtest_input=True, screenshot_each_action=False)
	poco_2 = AndroidUiautomationPoco(device_2, use_airtest_input=True, screenshot_each_action=False)
	poco_3 = AndroidUiautomationPoco(device_3, use_airtest_input=True, screenshot_each_action=False)

通过这种方式，在一台电脑上使用USBHub，连上二三十台手机是完全没有问题的。















