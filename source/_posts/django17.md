---
title: 微信小程序 传值取值的几种方法总结
date: 2019-08-23 21:51:55
categories:
- 微信小程序
tags:
- 微信小程序
- 传参
---
微信小程序 传值取值的几种方法总结。
<!-- more -->
# 原文链接
[微信小程序 传值取值的几种方法总结](https://www.jb51.net/article/103175.htm)
小程序里常见的取值有以下几种，一个完整的项目写下来，用到的概率几乎是100%。

	列表index下标取值
	页面传值
	form表单取值

# 列表index下标取值
实现方式是：data-index="{{index}}"挖坑及e.currentTarget.dataset.index来填坑即可
## 生成值

	<image src="../../../images/icon_delete.png" /><text>删除</text>
	
在删除图标与文字添加data-index="{{index}}"自定义属性以及绑定点击事件bindtap="delete"

	<view data-index="{{index}}" bindtap="delete"><image src="../../../images/icon_delete.png" /><text>删除</text></view>

实现delete方法，取到index下标值。
{% img /images/django/17_0.png %}

	delete: function (e) {
	  var index = parseInt(e.currentTarget.dataset.index);
	  console.log("index" + index);
	}
	
如果不使用e.currentTarget而使用e.target会怎样？
将会导致仅点中<view>才能输出index值，点子元素<image>或<text>将输出NaN。
{% img /images/django/17_1.png %}
那target有什么用呢，用于区分子元素与外部元素要分别处理时，比如换用户头像的场景，点击头像本身预览大图，而头像所在的点整一行，将是切换头像。
{% img /images/django/17_2.png %}
关于二者区别的详情说明，请见文档：https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxml/event.html
## 取出值
试图从index数据中找出相应元素删除地址
{% codeblock %}

// 找到当前地址AVObject对象
var address = that.data.addressObjects[index];
// 给出确认提示框
wx.showModal({
  title: '确认',
  content: '要删除这个地址吗？',
  success: function(res) {
    if (res.confirm) {
      // 真正删除对象
      address.destroy().then(function (success) {
        // 删除成功提示
        wx.showToast({
          title: '删除成功',
          icon: 'success',
          duration: 2000
        });
        // 重新加载数据
        that.loadData();
      }, function (error) {
 
      });
    }
  }
})

{% endcodeblock %}
# 页面传值
从收货地址列表页中传地址id到编辑页面，以读取原地址供修改之用。
address/list页面实现以下代码
{% codeblock %}

<view class="container" data-index="{{index}}" bindtap="edit"><image src="../../../images/icon_edit.png" /><text>编辑</text></view>
 
edit: function (e) {
  var that = this;
  // 取得下标
  var index = parseInt(e.currentTarget.dataset.index);
  // 取出id值
  var objectId = this.data.addressObjects[index].get('objectId');
  wx.navigateTo({
    url: '../add/add?objectId='+objectId
  });
},

{% endcodeblock %}
address/add页面实现onLoad(options)方法，从url路径中获取objectId

	onLoad: function (options) {
	  var objectId = options.objectId
	}
	
然后就是访问网络以及渲染页面了。
{% img /images/django/17_3.png %}
# form表单取值
## 方式一

	通过<form bindsubmit="formSubmit">与<button formType="submit">标签配合使用
	
布局如下：
{% codeblock %}

<form bindsubmit="formSubmit">
  <input name="detail" placeholder="详情地址" />
  <input name="realname" placeholder="收件人姓名" />
  <input name="mobile" placeholder="手机号码" type="number"/>
  <button formType="submit" type="primary">Submit</button>
</form>

{% endcodeblock %}
js取值：
{% codeblock %}

formSubmit: function(e) {
  // detail
  var detail = e.detail.value.detail;
  // realname
  var realname = e.detail.value.realname;
  // mobile
  var mobile = e.detail.value.mobile;
}

{% endcodeblock %}
## 方式二

	通过<input bindconfirm="realnameConfirm">实现
	
{% codeblock %}

// 实现相应多个**Confirm方式
detailConfirm: function(e) {
  var detail = e.detail.value;
}
realnameConfirm: function(e) {
  var realname = e.detail.value;
}
mobileConfirm: function(e) {
  var mobile = e.detail.value;
}

{% endcodeblock %}
通过方式一与方式二的对比可以看出，虽然同样都能实现取值的目标，但是它们的使用场景有所不同，前者适合与提交大量表单项时，比如用户完善个人资料，收货地址填写；而后者适合只做一两个表单项时，比如快递单号录入，绑定手机号码。
如果需要类似ajax即时响应的，应该选用后者，

	因为input能使用<input bindinput="bindInput" />
	
来实现即时取到值，比如商品搜索框输入手机关键字，应出现iPhone7，Mate8等候选词这样的场景。
# 小结
列表index下标取值，页面传值，form表单传值，第1种无时无刻在用，第2种也很常用，只是小程序页面一般会较少，我现在这个项目也就是12个page，第3种相对用得少些，因为手机端毕竟不是生产力工具，用在注册页，评论页等。



