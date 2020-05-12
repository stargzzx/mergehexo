---
title: npm | run 'npm audit fix' to fix them, or 'npm audit' for details
date: 2020-03-07 10:43:01
categories:
- 网站设计
- npm
tags:
- npm
---
出现这个问题的背景我交代一下。

我将 hexo 进行多终端写作的时候，MacBook 也是其中一个终端，但是，我 npm 的时候，总是出错，其在最后出现了这个。

<!-- more -->

我是按照他给我的提示进行修复的。

执行

	npm audit fix

然后其又出现提示

	use `npm audit fix --force` to install breaking changes; or refer to `npm audit` for steps to fix these manually

说实话，这个没懂

我就直接按照提示

	npm audit fix --force

然后就出现

	fixed 6 of 6 vulnerabilities in 11466 scanned packages

然后我 npm 就没有错误了。
