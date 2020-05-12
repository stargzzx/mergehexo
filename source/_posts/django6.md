---
title: 微信小程序——开发入门
date: 2019-08-14 10:25:47
categories:
- [微信小程序]
- [python,框架,django]
tags:
- Django
- 微信小程序
---
这个是微信小程序的入门环节。

<!-- more -->

配置好小程序的开发后

![](/images/django/6_2.png)

<br/>

# 目录规范

<br/>

	utils : 工具类的代码
	pages: 页面相关的代码
	components: 组建相关的代码
	thirdparty: 第三方库
	resources: 小程序的资源

<br/>

# 小程序的文件类型

<br/>

	.wxml 结构
	.wxss 表现
	.js 行为
	.json 配置
	
![](/images/django/6_0.png)

<br/>

# .wxml

<br/>

用 wxml 获取 js 中的数据，其格式是有要求的，便是，wxml中的动态数据都是来自页面js的Page的data对象，数据绑定是用一个Mustache语法，就是变量名加双括号的语法，也可以绑定属性

.js 中的数据书写
	
	Page({
		data:{
			theName:'JACK'
		}
	})
	
需要注意的是所有的组件和属性要为小写

运算符绑定：

![](/images/django/6_24.png)

属性：

![](/images/django/6_25.png)


将 html 的各种标签，比如

	<div>、<p> 。。。 

全部变成一个标签 

	<view>

语法
	
	wx:if
	wx:for
	{{}}
		不主张用js控制页面的逻辑，而是用 wxml 控制

<br/>	

# .wxss

<br/>
具备CSS的大部分属性

新增尺寸单位

有全局样式和局部样式

<br/>

# .json

<br/>

	工具配置 project.config.json
	项目配置 app.json
	页面配置 <page name>.json

<br/>

# 小程序的入口，配置，样式

<br/>

	全局入口 app.js
	全局配置 app.json
	全局样式 app.wxss

<br/>

# 小程序的配置

<br/>

## 全局配置 app.json

```python
	{
	  "pages": [
		"pages/index/index",
		"pages/logs/logs"
	  ],
	  "window": {
		"backgroundTextStyle": "light",
		"navigationBarBackgroundColor": "#fff",
		"navigationBarTitleText": "WeChat",
		"navigationBarTextStyle": "black"
	  },
	  "sitemapLocation": "sitemap.json"
	}
```

### pages 配置

保存每一个路径，并且，第一个路径是小程序的首页，千万不要弄错了

### window 配置

文字类型、导航条背景颜色、导航条的文字、导航条的背景颜色等

### tabBar 配置

这个是只小程序下面有多个页面可以切换，当你输入 tabBar 的时候，它们会自动补全

![](/images/django/6_1.png)

```python
{
  "pages": [
    "pages/index/index",
    "pages/logs/logs"
  ],
  "window": {
    "backgroundTextStyle": "light",
    "navigationBarBackgroundColor": "#fff",
    "navigationBarTitleText": "WeChat",
    "navigationBarTextStyle": "black"
  },
  "sitemapLocation": "sitemap.json",
  "tabBar": {
    "list": [{
      "pagePath": "pages/index/index",
      "text": "首页",
      "iconPath": "",
      "selectedIconPath": ""
    },
    {
      "pagePath": "pages/logs/logs",
      "text": "日志",
      "iconPath": "",
      "selectedIconPath": ""
    }
    ]
  }
}
```

![](/images/django/6_3.png)

## 页面配置 <page>.json

页面配置只能配置全局配置中的window配置项内容

如果和全局配置冲突，会以页面配置为主

有一个比较特殊的配置项，是是否开启下拉刷新

以 index.json 为例

```python
{
  "enablePullDownRefresh": true
}
```

<br/>

# 小程序的逻辑层

<br/>

小程序的逻辑层也就是 .js 文件

## 小程序注册逻辑

集中在 app.js 里面

### globalData

小程序的全局数据

	userInfo 用来保存昵称
	onLaunch 生命周期回调函数，是小程序刚打开的时候，自动执行的
	
app函数是注册全局唯一的小程序，并且只能调用一次，接受一个对象作为函数参数

#### 使用 globalData

微信小程序在JavaScript文件中声明的变量和函数只在该文件中有效；不同的文件中可以声明相同名字的变量和函数，不会互相影响。如果希望在各个页面之间共同使用某些信息，并且可以对共享数据进行修改设置，以便于其他页面根据数据变化进行对应的调整，最好使用全局数据globalData。

globalData在app.js文件中app()全局应用实例中：

	// app.js
	App({
	  globalData: 1
	})
	
由于它具有共享属性，对于它的设置和修改，需要分两方面来说明。

第一，在app.js文件中，怎样设置和修改。设置其实跟在其他js文件中设置data值是一样的：

	//app.js
	App({
		globalData: {
			name: '张三',
			age: 18
		}
	})

你可以根据自己的需求，设置任何数据。在app.js中读取globalData，使用this就可以了：

	//app.js
	App({
		globalData: {
			name: '张三',
			age: 18
		},
		onLoad: function(){
			console.log(this.globalData.name);
		}
	})
	
同样的，如果我们需要在app.js中修改globalData，只需要给对应的变量重新赋值就可以了：

	//app.js
	App({
		globalData: {
			name: '张三',
			age: 18
		},
		onLoad: function(){
			this.globalData.name = '李四'
		}
	})
	
第二，在其他页面读取以及修改globalData。不论是读取还是修改，首先需要在应用的页面js文件中，引用app()实例，所以在js文件中，第一句要写上这句：

	var app = getApp();
	
然后，我们来看怎么获取globalData：

	//index.js
	var app = getApp();
	Page({
		onLoad: function() {
			console.log(app.globalData.name);
		}
	})
	
下面看下在其他页面怎么设置或者修改globalData。这里需要用到全局函数 getApp()：

	//index.js
	var app = getApp();
	Page({
		onLoad: function() {
			getApp().globalData.name = "王二麻子";
	　　　　 getApp().globalData.favorite = "集邮";
		}
	})
	
这样，就可以在index.js文件中，重新设置或者修改globalData的数据了。上面的例子，我们修改了globalData的name值，并且添加了一个集邮的爱好的属性。

## 页面注册逻辑

### page函数

注册一个页面

接受一个对象作为函数参数

### 页面数据

	data属性
		data:{
			messgae:"hello"
		}
		访问：this.data.message
		修改：this.setData({})
	全局数据
		使用APP函数，可以注册一个全局唯一的数据
		获取全局唯一的APP实例
			const app = getApp()
			var data = app.globalData
			
### 页面生命周期

![](/images/django/6_4.jpg)

在页面周期里面，主要参与有两个线程，一个是 view thread，另一个是 appservice thread.

其中 view 可以看作视图线程，appservice 可以看作逻辑线程。

当页面启动得时候，这两个线程也会同时启动。

首先是逻辑线程，会依次调用 onLoad、onShow，然后进入等待状态（waiting notify），等待视图线程完成初始化。

当视图线程完成初始化，就会通知（Notify）逻辑线程，并且拿到初始化数据（send initial data），接着便是第一次预渲染（first render）

在第一次渲染结束后，视图线程就会进入 ready 状态，并且通知（notify）逻辑线程，告诉它：我已经准备好了。

这个时候逻辑线程就会进入 onReady 生命周期函数，并且进入 active 状态。

当，视图线程进入 ready 状态，逻辑线程进入 active 状态，我们就可以说，这个页面已经完成初始化了。

在逻辑层进入 active 的状态后，就会不断地监听数据的变化。

当数据发生变化的时候，它会把新的数据发给视图线程，视图线程，拿到新的数据，会做出相应，渲染新的页面给用户。

如果说，在某一个时刻，页面被切换到后台了，那么逻辑线程就会进入 onHide 生命周期函数。

如果说，切换到后台的页面，被重新切换回来，那么就会触发 onShow 生命周期函数。

最后，用户把小程序退出的时候，逻辑线程又会触发 onUnload 生命周期函数。

### 生命周期回调函数

![](/images/django/6_5.png)

![](/images/django/6_6.png)

<br/>

# 小程序的视图层

<br/>

视图层代码的后缀是 .wxml 文件

## 数据绑定

	语法： {{}}

在小程序的逻辑层里面有这样的代码

```python
Page({
	data:{
		message:"hello Mima"
	}
})
```

在视图层我们直接

	<view> {{message}} </view>
	
上面的就能显示了。

## 列表渲染

语法 ：wx:for

在逻辑层中

```python
Page({
	data:{
		array:[{
			message:'foo',
		},{
			message:'bar'
		}]
	}
})
```

	<view wx:for ="{{array}}">
		{{index}}:{{item.message}}
	</view>
		index 是微信约定俗称的，就是第几个的意思，微信会自动排列
		item 也是微信约定俗成，在 array 里面的元素，都是 item
		
```python
<view wx:for="{{array}}">
    NO.{{index}} item,message:{{item.message}}
</view>
```

就会出现

	NO1 item，message：foo
	NO2 item，message：bar
	
![](/images/django/6_26.png)

## 条件渲染

语法：wx:if 、 wx:elif 、 wx:else

	<view wx:if="{{length > 5}}"> 1 </view>
	<view wx:elif="length > 2"> 2 </view>
	<view wx:else> 3 </view>

![](/images/django/6_27.png)

hidden也可以控制元素的显示和隐藏，那么两者有什么区别呢

if在切换时框架会有个局部渲染的过程，确保条件块在渲染可以销毁便于进行进行渲染，hidden组件始终都会渲染。只是通过hidden属性，我们可以控制元素在视图上的显示和隐藏。所以说wx-if 有更高的切换消耗，而hidden有更高的初始化消耗，如果频繁切换显示的话是hidden更好

## 绑定事件

生命周期回调事件是不需要管理的，当生命周期回调事件发生的时候，小程序会自动调用

但是，绑定事件，是需要认为管理的。

![](/images/django/6_7.png)

绑定事件的写法是以key、value的形式存在的

key以bind或catch开头，然后跟上事件的类型

视图层

	<view id='tapTest' data-hi='WeChat' bindtap='tapName'>Click me! </view>
	
逻辑层

	page({
		tapname:function(event){
			console.log(event)
		}
	})

## 模板引用


![](/images/django/6_28.png)

模板标签template

在template中定义了一个代码片段，那么属性代表了template的名称，在外面通过一个is属性声明我们想要的是哪个模板，这里的is也可以动态的进行数据绑定，data属性，向模板传递数据信息

需要注意的是模板拥有自己的作用域，只能通过data属性去传入。

除了模板引用，微信还提供了文件引用

## 文件引用

![](/images/django/6_29.png)

### import include

![](/images/django/6_30.png)

所引用的模板文件，它的模板只能去渲染它对应的模板的内容

import作用域的概念：

只能引用目标文件所定义的的template模板，如果说目标文件嵌套了其他的文件的template模板，是不会被引用到的，避免了引用模板死循环的问题

![](/images/django/6_31.png)

和import不同的是，include是把目标文件内除了template代码块之外的所有代码引进来

![](/images/django/6_32.png)

以上就是import和include的区别

<br/>

# 响应式像素

<br/>

![](/images/django/6_33.png)

wxss和css的样式定义很像，但对css做了一些补成

![](/images/django/6_34.png)

![](/images/django/6_35.png)

设备像素：设备显示的最小物理单位，这些物理单位是显示屏上一个个点，这些点是固定不变的。

CSS像素：是外部编程概念，是css样式代码中所使用的逻辑像素。

PPI: 每英寸所拥有的像素数，数值越高，说明我们的显示屏越能以更高的密度去显示图像。公式：

![](/images/django/6_36.png)

DPR：指的是手机某一方向上设备像素和css像素之比

在做APP移动端开发，会使用rem,rem是根据HTML的根元素的font-size大小来适配的，但是wxss不能直接去操作HTML的样式属性，所以rem的适配方案就失效了。所以微信团队基于此推出了rpx,它规定了屏幕宽度为750个rpx，我们可以根据屏幕宽度进行自适应。其实rpx实现原理和rem原理是一样的，而且rpx最终也是转换成了rem。

## 样式

外联样式的引入：@import

![](/images/django/6_37.png)

如果样式index.wxss样式和外联样式有冲突，会采用index.wxss中的样式

内联样式：

![](/images/django/6_38.png)

内联样式可以绑定样式

所以一般来说，把静态样式写入class属性中，动态样式写入内联中绑定数据

## 选择器：

wxss支持的选择器：

![](/images/django/6_39.png)

## 优先级

![](/images/django/6_40.png)

慎用important，修改插件样式是可能会用到

WXML的学习就到这里了，从这里我们可以看到和HTML，CSS，前端的思想是一致的，在数据绑定这一模块和Vue是非常相似的。

# 逻辑层 js 视图层 wxml + wxss 总结

逻辑层主要负责数据、行为、路由

视图层主要负责结构、渲染、交互

逻辑层和视图层是通过数据绑定和事件回调进行互动的

<br/>

# 小程序提供的能力和常用API

<br/>

## 微信API

### 网络请求

#### HTTP请求

网络请求都是异步的

	wx.request({
		url:'',
		data:{},
		header:{},
		method:'POST',
		success:function(res){
			console.log(res.data)
		},
		fail:function(res){}
	})
	
![](/images/django/6_8.png)

我们放置这样一个按钮

	<button bindtap="testNet">Test Net</button>
	
然后逻辑是这样的

	testNet:function(event){
		wx.request({
		  url: 'http://www.imooc.com',
		  method:'GET',
		  header:{},
		  success:function(res){
			console.log(res.data)
		  },
		  fail:function(res){
			console.log('request failed')
		  }
		})
	  },
	  tapName:function(event){
		console.log(123)
	},
	
但是，我们可能会请求失败，可能的原因是不合法的域名，所以我们要勾选下面的选项：

![](/images/django/6_9.png)

这样我们就能正确的请求数据了。

##### 网络请求的异步特性

假设我们将代码改成这样

	testNet:function(event){
		var data = 'undefined'
		wx.request({
		  url: 'http://www.imooc.com',
		  method:'GET',
		  header:{},
		  success:function(res){
		  console.log('in success')
			data = res.data
		  },
		  fail:function(res){
			console.log('request failed')
		  }
		})
		console.log('data',data)
	  },
	  tapName:function(event){
		console.log(123)

最后输出的内容是

	data:undefined
	in success
	
我们可以看出是先打印的 data 后来又打印 success 函数的内容

因为 success 和 fail 的机制这样的，只有请求成功或者失败之后，才会调用相关的函数，在不满足的情况下，是不调用的。

代码都是直接运行下去的，直到成功或者失败才会运行函数。

#### 文件上传、下载

上传文件

	wx.uploadFile({
		url:'upload',
		filePath:'example.jpg',
		name:'file',
		formData:{
			'user':'test'
		},
		success(res){
			console.log(res.data)
		}
	})
	
![](/images/django/6_10.png)

下载文件

	wx.downloadFile({
		url:'download',
		header:{},
		filePath:'example.jpg',
		success(res){
			console.log(
				res.statusCode
			)
		}
	})
	
![](/images/django/6_11.png)

#### *Task（网络任务对象）

异步任务提交以后，需要对任务进行操作的时候，使用Task对象

作用

	中断任务
	触发回调函数
	关闭连接
	其中Task的类别如下：
		RequestTask
		UploadTask
		DownloadTask
		SocketTask
		

### 本地存储

本地存储可以理解为本地缓存。

就是把数据存储在本地缓存中指定的key中，数据存储生命周期跟小程序本身一致。

一共有四个API

	wx.setStorage
	wx.getStorage
	wx.removeStorage
	wx.clearStorage
	
在 wxml 添加

	<button bindtap="testStroage">test storage</button>
	
然后 .js 如下：

	testStroage:function(event){
		wx.setStorage({
		  key: 'test',
		  data: 'data',
		})
		wx.getStorage({
		  key: 'test',
		  success: function(res) {
			var data = res.data
			console.log('data from storage',data)
		  },
		})
	},
	
里面的那些函数（getStorage、setStorage）都是异步的。

当然，缓存也有同步的，比如

	wx.getStorageSysc(key)
	
### 文件系统

有一个全局的文件管理器，通过这个管理器，我们就能增删改查，以及对文件夹的操作。

获取全局唯一的文件管理器

	var fs = wx.getFileSystemManager()
	
对文件进行操作的API有很多，完全覆盖编程语言对文件的各种操作。

	fs.saveFile
	fs.writeFile
	fs.readFile
	fs.removeSavedFile
	fs.appendFile # 向文件追加内容
	
文件夹的操作

	fs.mkdir
	fs.rmdir
	fs.isDirectory
	fs.isFile

## 开放能力

### 用户数据

头像、昵称等公开信息——wx.getUserInfo()

openid等敏感数据

	微信会为每一个用户赋予一个唯一的openid
	
![](/images/django/6_12.png)

这个流程图全程都是加密的。

### 推送数据

基于微信的通知渠道，小程序框架为开发者提供了可以高效触达用户的消息。

![](/images/django/6_13.png)

### 运营消息

帮助小程序进行迭代优化

登录小程序管理后台或者进入小程序数据助手

## 基础组件

视图容器

	view
	scroll-view
	swiper
	cover-view
	
基础内容

	text
	icon
	rich-text
	
表单、导航

	button
	form
	input
	
WeUI-WXSS

WeUI是一套同微信原生视觉体验一致的基础样式库

微信官方设计团队为微信内网页和微信小程序量身设计

包括button、view等众多元素

![](/images/django/6_14.png)

### 得到WeUI

在 Github 上搜索 weui-wxss 获取源码

我们也可以直接在手机上预览，扫描下面的二维码。

![](/images/django/6_15.png)

具体的使用方法，如下

在github上搜索相关的内容，然后下载下来

用微信web开发者工具打开dist目录（请注意，是dist目录，不是整个项目）。 

![](/images/django/6_16.png)

我们把里面的 weui.wxss 拷贝到我们自己的项目，以我的为例

我在整个项目中，新建了一个文件夹，叫做 thirdparty

![](/images/django/6_17.png)

我们在app.wxss引入下面的代码

	@import 'thirdparty/weui.wxss';
	
#### 插入相应的组件

我们在github中下载下来的那个文件夹中，可以看到examples文件，里面存在大量的组组件源码。

我在项目中新增了一个文件夹，并且创建了相应的文件（demogrid）

![](/images/django/6_18.png)

我们在 app.json 中配置文件路径

![](/images/django/6_19.png)

![](/images/django/6_20.png)

然后将相应的代码复制到相应的文件，比如 .wxml 和 .js 文件的内容，复制到相应的后缀名文件里面

![](/images/django/6_23.png)

<br/>

# 小程序对接Django

<br/>

## 本地开发对接

小程序只能跟制定的域名进行网络通信

小程序必须使用 https 发起网络请求

## 远程部署测试

### 使用git远端同步代码

![](/images/django/6_21.png)

git的仓库

	coding.net
	oschina开源中国
	码云
	
我们通过 git 仓库就能同步服务器代码，而不需要修改之后传到服务器上

### 管理依赖环境

我们把我们自己用的库写在 requirements.txt

等我们下次用的时候直接

	pip install -r requirements.txt
	
例如：
	
	Django==2.0
	numpy==1.15

<br/>

# 小程序的发布流程

<br/>

![](/images/django/6_22.png)














