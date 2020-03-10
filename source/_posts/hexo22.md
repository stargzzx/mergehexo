---
title: hexo | 使用 markdown 语法使得图片块不能正确解析
date: 2020-03-09 22:41:48
categories:
- hexo
tags:
- hexo
---
这个问题真是搜遍全网，也没找到最正确的解决方案，最后，我看了一个官方文档，才解决。

<!-- more -->

<br/>

# 问题描述

<br/>

我原来使用

	<% img path %>

来放置图片（我把图片放在 /source/images/ 这个目录中）

但是，近期，发现有的图片不能得到正确解析。

其具体的表现形式是

上面的图片块有的可以解析成 html 图片，但是，有的无法解析，就直接是上面的图片块代码。

<br/>

# 解决方案

<br/>

搜遍了全网得知是和一个叫做 hexo-renderer-marked 的模块（hexo 插件，在 hexo 的 node_modules）有关。

[hexo-renderer-marked](https://github.com/hexojs/hexo-renderer-marked)

里面有一句话

	![text](/path/to/image.jpg) becomes <img src="/blog/path/to/image.jpg" alt="text">

所以，应该是的按照这个语法

所以，我将原来的图片代码块，变成了这个

	![](path to image)

因为，我的文章非常多，大概 599 篇，所以，我写了下面的代码帮助我修改。

你可以根据自身情况来修改下面的代码。

唉，还需要进步啊，这个问题，我搞了一晚上，耽误了不少正事！！！


```python
import glob

paths = glob.glob(r"./*.md")
print(len(paths))

for f_p in paths:
    print(f_p)
    f = open(f_p, 'r')
    alllines = f.readlines()
    f.close()

    f = open(f_p, 'w+')
    for eachline in alllines:
        if "{% img" in eachline:
            path = eachline.split(' ')[2]
            eachline = f"![]({path})"
            f.writelines(eachline)
            f.writelines('\n')
        else:
            f.writelines(eachline)
    f.close()
```