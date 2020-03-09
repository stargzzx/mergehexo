---
title: 微信小程序————setData()方法的使用和注意事项
date: 2019-08-22 19:40:18
categories:
- 微信小程序
tags:
- Django
- 微信小程序
---
微信小程序————setData()方法的使用和注意事项

<!-- more -->

<br/>

# 参考资料

<br/>

[微信小程序————setData方法的使用和注意事项](https://benpaodewoniu.github.io/2019/08/23/django16/)

<br/>

# Page.prototype.setData(Object data, Function callback)

<br/>

setData 函数用于将数据从逻辑层发送到视图层（异步），同时改变对应的 this.data 的值（同步）。 

参数说明： 

![](/images/django/14_0.png)

Object 以 key: value 的形式表示，将 this.data 中的 key 对应的值改变成 value。

其中 key 可以以数据路径的形式给出，支持改变数组中的某一项或对象的某个属性，如 array[2].message，a.b.c.d，并且不需要在 this.data 中预先定义。

注意：

	直接修改this.data，而不调用this.setData()，是无法改变当前页面的状态的，会导致数据不一致
	仅支持可以JSON化的数据
	单次设置的数据不能超过1024KB，尽量避免一次设置过多的数据
	不要把data中的任何一项的value设为undefined，否则这一项将不能被设置，可能会有潜在的问题

{% codeblock %}
Page({

  /**
   * 页面的初始数据
   */
 data: {
    value:"初始值"    //定义一个变量value，赋值为：“初始化”

  },
   /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {

  }
  })
{% endcodeblock %}

在页面中显示：

![](/images/django/14_1.png)

在onLoad（）函数中调用setData（）

	onReady: function () {
		this.setData({
		  value:"调用setData（）修改后的值"
		})

	  }
	  
再次运行： 

![](/images/django/14_2.png)

在wx:request()中使用

代码：

{% codeblock %}
wx:wx.request({
      url: 'http://180.169.225.27:7011/ibeacon/mobile.asmx/getRequest',
      data:{userid:this.data.username},
      header: {
        'content-type': 'application/json'
      },
      method: 'POST',
      dataType: 'json',

      success: function(res) {

        console.log(JSON.parse(res.data.d));  
        var value = JSON.parse(res.data.d);
        this.setData({
          postData:value
        })

      },
      fail: function(res) {},
      complete: function(res) {},
    });
{% endcodeblock %}

这里会出现一个问题：直接在wx:request()的success回调函数中使用this.setData()会报这样一个错误：

![](/images/django/14_3.png)

因为这里的this是相对于wx:request()的当前对象，我们可以这样解决：因为这里的wx:rewuest()是在页面加载时调用，所以我们可以在onLoad（）中定义一个变量that ，将this赋值给that，那么此时的that代表相对于onLoad（）的当前对象，然后在success回调函数中直接使用that.setData()即可。

完整代码如下：

{% codeblock %}
//js代码
onLoad: function (options) {
    console.log(options);
    var that = this;
    var userid = options.userid;
    that.setData({
      username: userid
    })
    // this.data.username = JSON.stringify(options);
    console.log(this.data.username);

    wx:wx.request({
      url: 'http://180.169.225.27:7011/ibeacon/mobile.asmx/getRequest',
      data:{userid:this.data.username},
      header: {
        'content-type': 'application/json'
      },
      method: 'POST',
      dataType: 'json',

      success: function(res) {

        console.log(JSON.parse(res.data.d));  
        var value = JSON.parse(res.data.d);
        that.setData({
          postData:value
        })

      },
      fail: function(res) {},
      complete: function(res) {},
    });
  }
{% endcodeblock %}

	//wxml代码
	<view wx:for="{{postData}}">posno:{{item.posno}},TradeTime:{{item.TradeTime}}</view>

正常获取到服务器返回的数据： 

![](/images/django/14_4.png)

还有一个需要注意到地方就是：

如果在onReady（）函数中调用setData（）方法，那么通过这个方法设置的值只能刷新一次，若需要再次刷新，需要清除缓存。
