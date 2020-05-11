---
title: python | selenium 浏览器启动参数
date: 2020-05-11 14:47:59
categories:
- python
- 模块
- selenium
tags:
- python
- selenium
---
不同机器不同电脑不同情况需要设置的参数完全不同。

<!-- more-->

<br/>

# 参数设置

<br/>

	option = webdriver.ChromeOptions()

- [全部参数](https://peter.sh/experiments/chromium-command-line-switches/)

## 经典参数

|参数名称|作用|
|---|---|
|options.add_argument('window-size=1920x3000') |指定浏览器分辨率|
|chrome_options.add_argument('--disable-gpu') |不启用 GPU 谷歌文档提到需要加上这个属性来规避bug|
|options.add_argument('--hide-scrollbars')|隐藏滚动条, 应对一些特殊页面|
|options.add_argument('blink-settings=imagesEnabled=false') |不加载图片, 提升速度|
|options.add_argument('--headless') |浏览器不提供可视化页面. linux下如果系统不支持可视化不加这条会启动失败|
|options.add_argument('--no-sandbox')|以最高权限运行|
|option.add_argument("--disable-javascript") |禁用JavaScript|
|options.add_experimental_option('excludeSwitches', ['enable-automation'])|设置开发者模式启动，该模式下webdriver属性为正常值|



# 指定浏览器位置

	options.binary_location = r"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" 