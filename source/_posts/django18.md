---
title: 微信小程序之综合实践——休闲娱乐
date: 2019-08-30 14:40:36
categories:
- [微信小程序]
- [python,框架,django]
tags:
- Django
- 微信小程序
---
微信小程序之综合实践——休闲娱乐

<!-- more -->

<br/>

# 功能

<br/>

	实现股票查询应用
	实现星座运势应用
	实现每日笑话应用
	
![](/images/django/18_0.png)

<br/>

# 小程序页面设计

<br/>

目录结构

![](/images/django/18_2.png)

## menu.wxml

```python
<!--pages/menu/menu.wxml-->
<view class="page">
  <view class="page__hd">
    <view class="weui-panel__hd">应用列表</view>
  </view>
  <view class="page__bd">
    <view class="weui-grids">
      <block wx:for="{{grids}}" wx:key="*this">
        <navigator url="" class="weui-grid" hover-class="weui-grid_active" data-index='{{index}}' bindtap='onNavigatorTap'>
          <image class="weui-grid__icon" src="../../resources/icons/cube.svg" />
          <view class="weui-grid__label">{{item.app.name}}</view>
        </navigator>
      </block>
    </view>
  </view>
</view>
```

## menu.js

```python
// pages/menu/menu.js

const app = getApp()

Page({

  /**
   * 页面的初始数据
   */
  data: {
    grids: [{
      "name": "应用1"
    }, {
      "name": "应用2"
    }], // 九宫格内容
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function(options) {
    this.updateMenuData()
  },

  /**
   * 请求后台，更新menu数据
   */
  updateMenuData: function() {
    var that = this
    wx.request({
      url: app.globalData.serverUrl + app.globalData.apiVersion + '/service/menu',
      success: function(res) {
        var menuData = res.data.data
        that.setData({
          grids: menuData
        })
      }
    })
  },

  onNavigatorTap: function(e) {
    var index = e.currentTarget.dataset.index
    var item = this.data.grids[index]
    console.log(item)
    if (item.app.application == 'weather') {
      console.log('-------------')
      wx.navigateTo({
        url: '../weather/weather',
      })
    } else if (item.app.application == 'backup-image') {
      wx.navigateTo({
        url: '../backup/backup',
      })
    } else if (item.app.application == 'stock'){
      wx.navigateTo({
        url: '../stock/stock',
      })
    } else if (item.app.application == 'constellation'){
      wx.navigateTo({
        url: '../service/service?type=constellation',
      })
    } else if (item.app.application == 'joke'){
      wx.navigateTo({
        url: '../service/service?type=joke',
      })
    }
  }
})
```

整个的目录结构如下：

![](/images/django/18_1.png)

这个请求的是 django 下面的 views 的 menu.py

## menu.py

```python
import os
import yaml
from django.http import JsonResponse
from backend import settings
import utils.response


def init_app_data():
    data_file = os.path.join(settings.BASE_DIR, 'app.yaml')
    with open(data_file, 'r', encoding='utf-8') as f:
        apps = yaml.load(f)
        return apps

def get_menu(request):
    global_app_data = init_app_data()
    published_apps = global_app_data['published']
    # return JsonResponse(data=published_apps, safe=False, status=200)
    response = utils.response.wrap_json_response(data=published_apps)
    return JsonResponse(data=response, safe=False)
```

而其中访问的 app.yaml 是

## app.yaml

```python
published:
  - app:
      category: life
      application: weather
      name: 天气
      publish_date: 2018-10-01
      url: /service/weather
      desc: this is a weather app.
  - app:
      category: life
      application: backup-image
      name: 图片备份
      publish_date: 2018-10-02
      url: /service/image
      desc: this is a backup image app.
  - app:
      category: life
      application: stock
      name: 股票
      publish_date: 2018-10-03
      url: /service/stock
      desc: this is a stock app.

  - app:
      category: life
      application: constellation
      name: 星座运势
      publish_date: 2018-10-03
      url: /service/constellation
      desc: this is a constellation app.

  - app:
      category: life
      application: joke
      name: 笑话
      publish_date: 2018-10-03
      url: /service/joke
      desc: this is a joke app.
```

而在第三方接口，即 thirdparty 下的 juhe.py 的内容如下

## juhe.py

```python
import json
import time
import requests

from utils import proxy



def constellation(cons_name):
    '''
    :param cons_name: 星座名字
    :return: json 今天运势
    '''
    key = '638590d043a54639f3560b5381f5c4f0'
    api = 'http://web.juhe.cn:8080/constellation/getAll'
    types = ('today', 'tomorrow', 'week', 'month', 'year')
    params = 'consName=%s&type=%s&key=%s' % (cons_name, types[0], key)
    url = api + '?' + params
    print(url)
    response = requests.get(url=url, proxies=proxy.proxy())
    data = json.loads(response.text)
    return {
        'name': cons_name,
        'text': data['summary']
    }


def stock(market, code):
    '''
    沪深股票
    :param market: 上交所 = sh, 深交所 = sz
    :param code: 股票编号
    :return:
    '''
    key = 'f887b09847c9bcde9801ca7aaa86513e'
    api = 'http://web.juhe.cn:8080/finance/stock/hs'
    params = 'gid=%s&key=%s' % (market + code, key)
    url = api + '?' + params
    print(url)
    response = requests.get(url=url, proxies=proxy.proxy())
    data = json.loads(response.text)
    data = data.get('result')[0].get('data')
    response = {
        'name': data.get('name'),
        'now_price': data.get('nowPri'),
        'today_min': data.get('todayMin'),
        'today_max': data.get('todayMax'),
        'start_price': data.get('todayStartPri'),
        'date': data.get('date'),
        'time': data.get('time')
    }
    response['is_rising'] = data.get('nowPri') > data.get('todayStartPri')
    sub = abs(float(data.get('nowPri')) - float(data.get('todayStartPri')))  # 差值
    response['sub'] = float('%.3f' % sub)
    return response


def history_today():
    key = '6c6b318d983b6b4ac8cc5cda0da92155'
    api = 'http://api.juheapi.com/japi/toh'
    month = time.localtime().tm_mon
    day = time.localtime().tm_mday
    params = 'v=1.0&month=%d&day=%d&key=%s' % (month, day, key)
    url = api + '?' + params
    response = requests.get(url=url, proxies=proxy.proxy())
    data = json.loads(response.text)
    result_list = data.get('result')
    result = []
    for item in result_list:
        result.append({
            'title': item.get('title'),
            'content': item.get('des')
        })
    return result


def weather(cityname):
    '''
    :param cityname: 城市名字
    :return: 返回实况天气
    '''
    key = '9a3e1fa6cb79d69f1594af5cb219a469'
    api = 'http://v.juhe.cn/weather/index'
    params = 'cityname=%s&key=%s' % (cityname, key)
    url = api + '?' + params
    print(url)
    response = requests.get(url=url, proxies=proxy.proxy())
    data = json.loads(response.text)
    print(data)
    result = data.get('result')
    sk = result.get('sk')
    response = {}
    response['temperature'] = sk.get('temp')
    response['wind_direction'] = sk.get('wind_direction')
    response['wind_strength'] = sk.get('wind_strength')
    response['humidity'] = sk.get('humidity')  # 湿度
    response['time'] = sk.get('time')
    return response

if __name__ == '__main__':
    data = weather('深圳')
```

而我们用 views 中的 service.py 来调用上面的函数

## service.py

```python
import os
import json
import random
from django.http import JsonResponse

from backend import settings
from utils.response import CommonResponseMixin, ReturnCode
import thirdparty.juhe


constellations = ['白羊座', '金牛座', '双子座', '巨蟹座', '狮子座', '处女座', '天秤座', '天蝎座', '射手座', '摩羯座', '水瓶座', '双鱼座']
popular_stocks = [
    {
        'code': '000001',
        'name': '平安银行',
        'market': 'sz'
    },
    {
        'code': '000002',
        'name': '万科A',
        'market': 'sz'
    },
    {
        'code': '600036',
        'name': '招商银行',
        'market': 'sh'
    },
    {
        'code': '601398',
        'name': '工商银行',
        'market': 'sh'
    }
]

all_jokes = []

def stock(request):
    data = []
    for stock in popular_stocks:
        result = thirdparty.juhe.stock(stock['market'], stock['code'])
        data.append(result)
    response = CommonResponseMixin.wrap_json_response(data=data, code=ReturnCode.SUCCESS)
    return JsonResponse(data=response, safe=False)
    pass


def constellation(request):
    data = []
    for c in constellations:
        result = thirdparty.juhe.constellation(c)
        data.append(result)
    response = CommonResponseMixin.wrap_json_response(data=data, code=ReturnCode.SUCCESS)
    return JsonResponse(data=response, safe=False)
    pass


def joke(request):
    global all_jokes
    if not all_jokes:
        all_jokes = json.load(open(os.path.join(settings.BASE_DIR, 'jokes.json'), 'r'))
    limits = 10
    sample_jokes = random.sample(all_jokes, limits)
    response = CommonResponseMixin.wrap_json_response(data=sample_jokes)
    return JsonResponse(data=response, safe=False)
```

views 下的 urls.py 的内容如下：

## urls.py

```python
from django.urls import path

from .views import weather, menu, image, service

urlpatterns = [
    # path('', weather.helloworld)
    path('weather', weather.WeatherView.as_view()),
    path('menu', menu.get_menu),
    path('image', image.ImageView.as_view()),
    path('image/list', image.ImageListView.as_view()),
    path('stock', service.stock),
    path('constellation', service.constellation),
    path('joke', service.joke)
]
```

然后我们就以股票的页面为例：

## stock.wxml

```python
<view class="weui-panel weui-panel_access">
    <view class="weui-panel__hd">股票：</view>
    <view class="weui-panel__bd">
      <view class="weui-media-box weui-media-box_text" wx:for='{{stockData}}' wx:key="*this">
        <view class="weui-media-box__title weui-media-box__title_in-text" wx:if='{{item.is_rising}}'>{{item.name}} (+ {{item.sub}})</view>
        <view class="weui-media-box__title weui-media-box__title_in-text" wx:else>{{item.name}} (-{{item.sub}})</view>
        <view>当前：{{item.now_price}}</view>
        <view class="weui-media-box__info">
          <view class="weui-media-box__info__meta">最高：{{item.today_max}}</view>
          <view class="weui-media-box__info__meta">最低：{{item.today_min}}</view>
          <view class="weui-media-box__info__meta weui-media-box__info__meta_extra">开盘：{{item.start_price}}</view>
        </view>
      </view>
    </view>
  </view>
```

## stock.js

```python
// pages/stock/stock.js

const app = getApp()

Page({

  /**
   * 页面的初始数据
   */
  data: {
    stockData: []
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    this.updataStockData()
  },

  updataStockData: function(){
    var that = this
    wx.showLoading({
      title: '加载中',
    })
    wx.request({
      url: app.globalData.serverUrl + app.globalData.apiVersion + '/service/stock',
      success: function(res){
        that.setData({
          stockData: res.data.data
        })
        wx.hideLoading()
      }
    })
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {

  },

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {

  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {

  },

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {

  },

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {

  },

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {

  },

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {

  }
})
```







