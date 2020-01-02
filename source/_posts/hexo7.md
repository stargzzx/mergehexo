---
title: hexo next 代码块高亮
date: 2019-10-06 17:33:08
categories:
- hexo
tags:
- hexo
- 高亮
---
早就想配置我的博客了，没有一点颜色，难看死了，这次配置的是代码块高亮。
<!--more-->
# 原文参考
[next主题设定代码高亮格式](https://blog.csdn.net/u011240016/article/details/79422448)
[如何让hexo代码高亮？](https://www.zhihu.com/question/37052642)
[hexo中next主题代码高亮无法正常显示，如何解决？](https://www.zhihu.com/question/51705387)
# 正文内容
新版的Next主题卡得很严，记录如下。

首先需要动的地方有：

	主题的_config.yml文件
	站点的_config.yml文件
	代码块的语言标注
	
### 站点配置文件

在站点的配置文件中，搜索hightlight:

	highlight:
	  enable: true
	  line_number: true
	  auto_detect: true
	  tab_replace:
	  
文字自动检测默认不启动，所以改成true使其起作用。

### 主题配置文件

再到主题的配置文件：

找到：

highlight_theme: normal，注释显示有五种显示主题可用，分别是：

	normal
	night
	night eighties
	night blue
	night bright
	
我选择的是night

### 代码块
此时，还不能解决高亮问题，虽然背景会变，但是文字并没起作用。

有的博文建议用反码代码块包裹，然后注明代码

	``` [language] [title] [url] [link text] code snippet ```
	
但是，这个在我的版本中并不适用，我最后选择的还是之前的标签

	{% codeblock %}{% endcodeblock %}
	
并且，我也没有注明代码类型，但是，最后却能显示高亮，在此记录一下，可能是版本问题，或者有默认选项。
总之，我的代码有高亮了。