---
title: hexo | 增加打赏和统计访客次数
date: 2020-03-08 12:15:50
categories:
- hexo
tags:
- hexo
---
因为看见大家的博客都有这个功能，所以，我也加上。

next 的版本是 7.7.2

<!-- more -->

<br/>

# 统计访客

<br/>

next 的配置文件 _config.yml


	busuanzi_count:
	  enable: true
	  total_visitors: true
	  total_visitors_icon: user
	  total_views: true
	  total_views_icon: eye
	  post_views: true
	  post_views_icon: eye

<br/>

# 开启打赏

<br/>

next 的配置文件 _config.yml


	reward_settings:
	  # If true, reward will be displayed in every article by default.
	  enable: true
	  animation: true
	  comment: 请我喝杯咖啡吧～

	reward:
	  wechatpay: /images/icon/wechat.jpeg
	  alipay: /images/icon/aipay.jpeg

和网上教程不一样的是，网上的图片是放在 themes ,我的位置是在项目下的 images 中。