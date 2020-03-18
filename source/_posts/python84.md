---
title: python | PyMuPDF
date: 2020-03-18 20:32:30
categories:
- python
tags:
- python
- PyMuPDF
---
站在巨人的肩膀上，不重复造轮子， PyMuPDF 是处理 PDF 文件的库。

<!-- more -->

<br/>

# 资料

<br/>

[官方文档](https://pymupdf.readthedocs.io/en/latest/)

<br/>

# 安装

<br/>

	pip install PyMuPDF

<br/>

# 教程

<br/>

## 加载文件

```python
import fitz
doc = fitz.open(self.pdf_file)
```

很奇怪，虽然我们安装的是 PyMuPDF ，但是我们 import 的是 fitz 。




