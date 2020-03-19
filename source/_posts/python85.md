---
title: python | PyExecJS
date: 2020-03-19 11:47:59
categories:
- python
tags:
- python
- PyExecJS
---
使用 python 执行 js 代码。

<!-- more -->

<br/>

# 参考资料

<br/>

[PyExecJS](https://pypi.org/project/PyExecJS/)

<br/>

# 安装

<br/>

	pip install PyExecJS

<br/>

# 使用

<br/>

```python
import execjs
execjs.eval("'red yellow blue'.split(' ')")
['red', 'yellow', 'blue']
ctx = execjs.compile("""
     function add(x, y) {
         return x + y;
     }
""")
ctx.call("add", 1, 2)
```

关于更多细节，请参考官方文档。