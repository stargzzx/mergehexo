---
title: python | selenium 和 driver 的关系
date: 2020-05-11 17:17:06
categories:
- python
- 模块
- selenium
tags:
- python
- selenium
---
selenium 并不能直接使用浏览器，而是使用浏览器的驱动来控制浏览器。

<!-- more -->

<br/>

# 参考资料

<br/>

- [中文文档](https://www.selenium.dev/documentation/zh-cn/getting_started/)

<br/>

# 浏览器驱动

<br/>

|浏览器|	支持的操作系统|	维护者|	下载	|
|---|---|---|---|
|Chromium/Chrome|	Windows/macOS/Linux|	谷歌|	[下载](https://chromedriver.storage.googleapis.com/index.html)|
|火狐|	Windows/macOS/Linux|	Mozilla|	[下载](https://github.com/mozilla/geckodriver/releases)|
|Edge|	Windows 10|	微软|	[下载](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/)|
|Internet Explorer|	Windows	Selenium| 项目组|	[下载](https://selenium-release.storage.googleapis.com/index.html)|
|Safari|	macOS El Capitan| 及更高版本|	苹果	内置|
|Opera|	Windows/macOS/Linux|	Opera|	[下载](https://github.com/operasoftware/operachromiumdriver/releases)|

<br/>

# google

<br/>

在这里我们只讲 google 相关的。

- Chrome
	- 常用的谷歌浏览器
- Chromium
	- 谷歌开源浏览器
