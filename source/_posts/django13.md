---
title: 微信小程序——图片的备份
date: 2019-08-22 15:29:59
categories:
- 微信小程序
tags:
- Django
- 微信小程序
---
微信小程序——图片的备份

<!-- more -->

<br/>

# Django后台实现

<br/>

## 获取文件内容

通过
	
	request.FILES
	
获取的。

调用上面的函数，就会的得到 key-value对象

	key = 文件名
	value = 文件（二进制）
	
## Django文件操作

通过 OS 标准库

## 编写图片上传，下载，删除代码

{% codeblock %}
import os
from django.http import Http404, HttpResponse, FileResponse,JsonResponse
from backend import settings
import utils.response
from django.views import View
import hashlib
class ImageView(View,utils.response.CommonResponseMixin):
    def get(self,request):
        md5 = request.GET.get('md5')
        imgfile = os.path.join(settings.IMAGES_DIR, md5 + '.jpg')
        print(imgfile)
        if os.path.exists(imgfile):
            data = open(imgfile, 'rb').read()
            return HttpResponse(content=data, content_type='image/jpg')
            # return FileResponse(open(imgfile, 'rb'), content_type='image/jpg')
        else:
            return Http404()
    def post(self,request):
        files = request.FILES
        response = []
        for key,value in files.items():
            content = value.read()
            md5 = hashlib.md5(content).hexdigest() # 表示为十六进制
            path = os.path.join(settings.IMAGES_DIR,md5+'.jpg')
            with open(path,'wb') as f:
                f.write(content)
            response.append({
                'name':key,
                'md5':md5
            })
        message = 'post method success'
        response = self.wrap_json_response(data=response,code=utils.response.ReturnCode.SUCCESS,message=message)
        return JsonResponse(data=response,safe=False)

    def delete(self,request):
        md5 = request.GET.get('md5')
        img_name = md5 + '.jpg'
        path = os.path.join(settings.IMAGES_DIR,img_name)
        if os.path.exists(path):
            os.remove(path)
        else:
            message = 'file(%s) is not found' % img_name
        message = 'put method success'
        response = self.wrap_json_response(message=message,code=utils.response.ReturnCode.SUCCESS)
        return JsonResponse(data=response, safe=False)
{% endcodeblock %}

其中 utils.response 文件内容如下：

{% codeblock %}
class ReturnCode:
    SUCCESS = 0
    FAILED = -100
    RESOURCES_NOT_EXISTS = -1
    UNAUTHORIZED = -500
    BROKEN_AUTHORIZED_DATA = -501
    WRONG_PARMAS = -101
    @classmethod
    def message(cls, code):
        if code == cls.SUCCESS:
            return 'success'
        elif code == cls.FAILED:
            return 'failed'
        elif code == cls.UNAUTHORIZED:
            return 'unauthorized'
        elif code == cls.WRONG_PARMAS:
            return 'wrong params'
        else:
            return ''

class CommonResponseMixin(object):
    @classmethod
    def wrap_json_response(cls, data=None, code=None, message=None):
        response = {}
        if not code:
            code = ReturnCode.SUCCESS
        if not message:
            message = ReturnCode.message(code)
        if data:
            response['data'] = data
        response['result_code'] = code
        response['message'] = message
        return response
{% endcodeblock %}

关于这个有一个非常需要注意的一点就是，在这个 CommonResponseMixin 类的 wrap_json_response 方法里面有一个 cls ，它是接受传过来的 request 的，必须存在，否则会报错

具体的情况，可以参考这篇文章

[python @classmethod 的使用场合](https://benpaodewoniu.github.io/2019/08/23/python58/)

<br/>

# 小程序测的实现

<br/>

## WeUI的Upload控件

### wx.chooseImage

从本地相册选择图片或使用相机拍照。

![](/images/django/13_0.png)

注：文件的临时路径，在小程序本次启动期间可以正常使用，如需持久保存，需在主动调用 wx.saveFile，在小程序下次启动时才能访问得到。

success返回参数说明：

![](/images/django/13_1.png)

File 对象结构如下:

![](/images/django/13_2.png)

{% codeblock %}
wx.chooseImage({
    count: 1, // 默认9
    sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
    sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
    success: function (res) {
          // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
        var tempFilePaths = res.tempFilePaths
    }
})
{% endcodeblock %}

这里尤其要注意的是 tempFilePath可以作为img标签的src属性显示图片

{% codeblock %}
// 选择图片上传
chooseImage: function(e) {
var that = this;
wx.chooseImage({
  sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
  sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
  success: function(res) {
	// 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
	that.setData({
	  needUploadFiles: that.data.needUploadFiles.concat(res.tempFilePaths)
	});
  }
})
},
{% endcodeblock %}

![](/images/django/13_3.gif)

这里有一个非常奇怪的点是：

	needUploadFiles: that.data.needUploadFiles.concat(res.tempFilePaths)
	
将括号里面的 tempFilePaths 换成 tempFilePath

会导致不能使用。

## 小程序的文件上传和下载

### wx.uploadFile

{% codeblock %}
wx.chooseImage({
  success: function(res) {
    var tempFilePaths = res.tempFilePaths
    wx.uploadFile({
      url: 'http://example.weixin.qq.com/upload', 
      //仅为示例，非真实的接口地址
      filePath: tempFilePaths[0],
      name: 'file',
      formData:{
        'user': 'test'
      },
      success: function(res){
        var data = res.data
        //do something
      }
    })
  }
})
{% endcodeblock %}

这里要说明一下：

	上传服务器地址须为https，官方实例有误。
	客户端得是 HTTPS POST 请求。
	PC端也可上传文件，腾讯已经优化。
	小程序后台需要配置相应地址。
	
### wx.downloadFile

下载文件资源到本地，客户端直接发起一个 HTTP GET 请求，返回文件的本地临时路径。使用前请先阅读说明。

OBJECT参数说明：

![](/images/django/13_4.png)

注：文件的临时路径，在小程序本次启动期间可以正常使用，如需持久保存，需在主动调用 wx.saveFile，才能在小程序下次启动时访问得到。

success返回参数说明：

![](/images/django/13_5.png)

返回值：

>基础库 1.4.0 开始支持，低版本需做兼容处理

返回一个 downloadTask 对象，通过 downloadTask，可监听下载进度变化事件，以及取消下载任务。

#### wx.downloadTask

downloadTask 对象的方法列表：

![](/images/django/13_6.png)

onProgressUpdate 返回参数说明：

![](/images/django/13_7.png)

wxml：

{% codeblock %}
<button bindtap='down_file'>下载</button>//用来触发下载函数的按钮
<view><image src='{{img_l}}' bindtap='preview_img'></image></view>
{% endcodeblock %}

下载的资源在服务器中支持：

{% codeblock %}
page({
      data:{
        img_l:''
        },
preview_img:function(){//图片预览函数
    wx.previewImage({
      current: this.data.img_l, // 当前显示图片的http链接
      urls: this.data.img_l // 需要预览的图片http链接列表
    })
  },
  down_file:function(){
    var _this = this;
    const downloadTask=wx.downloadFile({
      url: 'http://127.0.0.1/m_pro/min_img/1517295297785-2018-01-30.jpg', //仅为示例，并非真实的资源
      success: function (res) {
        // 只要服务器有响应数据，就会把响应内容写入文件并进入 success 回调，业务需要自行判断是否下载到了想要的内容
       console.log(res)
       if(res.statusCode === 200){
          _this.setData({
            img_l:res.tempFilePath //将下载的图片临时路径赋值给img_l,用于预览图片
          })
       }
      }
    })
    downloadTask.onProgressUpdate((res) => {
      console.log('下载进度', res.progress)
      console.log('已经下载的数据长度', res.totalBytesWritten)
      console.log('预期需要下载的数据总长度', res.totalBytesExpectedToWrite)
    })
  }
})
{% endcodeblock %}

### wx.request

发起 HTTPS 网络请求。

![](/images/django/13_8.png)

#### object.method 的合法值

![](/images/django/13_9.png)

#### object.dataType 的合法值

![](/images/django/13_10.png)

#### object.responseType 的合法值

![](/images/django/13_11.png)

#### object.success 回调函数

![](/images/django/13_12.png)

data 参数说明

最终发送给服务器的数据是 String 类型，如果传入的 data 不是 String 类型，会被转换成 String 。转换规则如下：

	对于 GET 方法的数据，会将数据转换成 query string（encodeURIComponent(k)=encodeURIComponent(v)&encodeURIComponent(k)=encodeURIComponent(v)...）
	对于 POST 方法且 header['content-type'] 为 application/json 的数据，会对数据进行 JSON 序列化
	对于 POST 方法且 header['content-type'] 为 application/x-www-form-urlencoded 的数据，会将数据转换成 query string （encodeURIComponent(k)=encodeURIComponent(v)&encodeURIComponent(k)=encodeURIComponent(v)...）

示例代码

{% codeblock %}
wx.request({
  url: 'test.php', //仅为示例，并非真实的接口地址
  data: {
    x: '',
    y: ''
  },
  header: {
    'content-type': 'application/json' // 默认值
  },
  success (res) {
    console.log(res.data)
  }
})
{% endcodeblock %}

## 具体应用

### .wxml

{% codeblock %}
<view class="page">
  <view class="page__hd">
    <view class="page__title">图片备份</view>
  </view>
  <view class="page__bd">
    <view class="weui-cells">
      <view class="weui-cell">
        <view class="weui-cell__bd">
          <view class="weui-uploader">
            <view class="weui-uploader__hd">
              <view class="weui-uploader__title">待上传图片</view>
            </view>
            <view class="weui-uploader__bd">
              <view class="weui-uploader__files" id="uploaderFiles">
                <block wx:for="{{needUploadFiles}}" wx:key="*this">
                  <view class="weui-uploader__file" data-type="UploadView" bind:longpress="longTapConfirm" data-id="{{item}}">
                    <image class="weui-uploader__img" src="{{item}}" mode="aspectFill" />
                  </view>
                </block>
              </view>
              <view class="weui-uploader__input-box">
                <view class="weui-uploader__input" bindtap="chooseImage"></view>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <view class="page__bd page__bd_spacing button-sp-area">
      <button class="weui-btn" type="primary" bindtap='uploadFiles'>确认上传</button>
      <button class="weui-btn" type="primary" bindtap='downloadFile'>下载图片</button>
      <button class="weui-btn" type="primary" bindtap='deleteBackup'>删除图片</button>
    </view>



    <!-- 已上传图片 -->
    <view class="weui-cells">
      <view class="text-center">已备份图片</view>
      <view class="weui-cell" wx:for="{{downloadedBackupedFiles}}">
        <image class="" src="{{item}}" mode="aspectFill" data-index="{{index}}" data-type="DownloadedView" bindlongtap="longTapConfirm" />
      </view>
    </view>
    <view class='text-center' wx:if="{{downloadedBackupedFiles.length == 0}}">暂无</view>
  </view>
</view>
{% endcodeblock %}

![](/images/django/13_13.png)

### .js

{% codeblock %}
const app = getApp()
const imageUrl = app.globalData.serverUrl + app.globalData.apiVersion + '/service/image'

Page({
  data: {
    // 需要上传的图片
    needUploadFiles: [],
    // 已下载的备份图片
    downloadedBackupedFiles: [],
  },

  // 选择图片上传
  chooseImage: function(e) {
    var that = this;
    wx.chooseImage({
      sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
      sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
      success: function(res) {
        // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
        that.setData({
          needUploadFiles: that.data.needUploadFiles.concat(res.tempFilePaths)
        });
      }
    })
  },

  // 上传图片文件
  uploadFiles: function() {
    for (var i = 0; i < this.data.needUploadFiles.length; i ++){
      var filePath = this.data.needUploadFiles[i]
      wx.uploadFile({
        url: app.globalData.serverUrl + app.globalData.apiVersion + '/service/image',
        filePath: filePath,
        name: 'test',
        success: function(res){
          console.log(res)
        }
      })
    }
  },

  // 下载图片
  downloadFile: function (imgItem) {
    console.log(1234)
    var that = this
    wx.downloadFile({
      url: app.globalData.serverUrl + app.globalData.apiVersion + '/service/image' + '?md5=' + '041ed8828b50f618eda06f880c4817c3', //这个是固定的，实际的时候需要动态,比如采用传入参数的形式   
      success: function(res){
        var tmpPath = res.tempFilePath  //获取临时链接
        var newDownloadedBackupedFiles = that.data.downloadedBackupedFiles
        // 得到下载图片数组
        newDownloadedBackupedFiles.push(tmpPath)
        // 向新图片数组中加入图片路径
        that.setData({
          downloadedBackupedFiles: newDownloadedBackupedFiles //更新
        })
      }
    })
  },

  // 删除图片
  deleteBackup: function(imgItem){
    wx.request({
      url: app.globalData.serverUrl + app.globalData.apiVersion + '/service/image' + '?md5=' + '041ed8828b50f618eda06f880c4817c3',
      method: 'DELETE',
      success: function(res){
        console.log(res.data)
        wx.showToast({
          title: '删除成功',
        })
      }
    })
  }
});
{% endcodeblock %}

![](/images/django/13_14.gif)

关于后台 Django 的书写，在开头就已经给出。