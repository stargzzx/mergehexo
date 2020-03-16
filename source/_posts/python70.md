---
title: python | 进度条
date: 2020-01-27 20:38:23
categories:
- python
tags:
- python
- 进度条
- progressbar
---
进度条非常简单，我们只需要安装一个库文件就好了。

	progressbar

<!-- more -->

<br/>

# 安装 progressbar 

<br/>

	pip install progressbar

代码表示

```python
bar = Bar('进度如下：', max=len(file_names))
for file in file_names:
	处理代码
    bar.next()
bar.finish()
```

这个代码逻辑已经非常清晰了，就不再多做解释了。
