---
title: hexo | 添加侧边的社交方式
date: 2020-03-08 13:51:07
categories:
- hexo
tags:
- hexo
---
我想把我的微信和qq的联系方式都贴上。

<!-- more -->

找到 next 的 _config.yml 这个配置文件，有一个


	social:
	  WeChat: li*** || weixin
	  QQ: 150631*** || qq
	  GitHub: https://github.com/be***** || github
	  E-Mail: mailto:1506***@qq.com || envelope

	social_icons:
	  enable: true
	  icons_only: false
	  transition: false

在这里你可以选择你想要添加的联系方式。

|| 之后是在图标库中对应的图标。注意空格就行。

关于相关的图标名称你可以在下面的网站中找到，不需要下载，只需要在 || 之后添加相应的名称就好了。

[Font Awesome](https://fontawesome.com/icons?from=io)

但是，如果上面直接添加的话，用户体验不好，应该连接成一个文章，这个，以后我再修改吧。

ok，我已经换上文章链接了。

