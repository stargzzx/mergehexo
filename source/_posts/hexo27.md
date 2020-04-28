---
title: hexo | 上传 PDF、PPT 等资源
date: 2020-04-28 17:33:07
categories:
- hexo
tags:
- hexo
---
有时候，我们想上传一些文件，不是图片之类的，但是，想展示出来。比如

- PPT
- PDF

并且，还希望有一个可以下载的链接。

<!-- more -->

<br/>

# hexo-pdf

<br/>

现在网上大部分都流行使用 `hexo-pdf` 这个插件。

但是，自己用过后，发现，并不能展示，所以，这个方法放弃。

<br/>

# 静态方法

<br/>

我们直接传入资源，并且使用 `html` 标签展示。

比如

我们在 `source` 下创建 `pdf` 和 `ppt` 文件夹，然后把资源上传上去。

## 提供下载

	<a href="/ppt/3.pptx">下载PPT</a>
	<a href="/pdf/3.pdf">下载PDF</a>

## 展示 pdf

	<object data="/pdf/3.pdf" type="application/pdf" width="100%" height="877px">

这样可以达到一个很好的效果。

## BUG

这里面有一个小 BUG，就是在 `object` 后面的内容显示不出来，所以，我们要把

	<object data="/pdf/3.pdf" type="application/pdf" width="100%" height="877px">

放在文章的最后面。