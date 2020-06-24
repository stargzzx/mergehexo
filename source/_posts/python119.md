---
title: python | 对字典的操作总结
date: 2020-06-24 11:15:07
categories:
- python
- 进阶
tags:
- python
---
这个总结了一下对字典的操作源码已经应用场景。

<!-- more -->

有的方法也在下面的博文的字典专栏中有说明，请查看。

- [python详解](https://benpaodewoniu.github.io/2018/06/03/python4/)

<br/>

# 内嵌套字典

<br/>

假设存在一堆数据

	data = {
		"list": [
			{'currency':'btc','type':'trade','balance':'0'},
			{'currency':'btc','type':'frozen','balance':'0'},
			{'currency':'eth','type':'trade','balance':'0'},
			{'currency':'eth','type':'frozen','balance':'0'},
			{'currency':'eos','type':'trade','balance':'0'},
			{'currency':'eos','type':'frozen','balance':'0'},
		]
	}

最后，我们想变成

	buf = {
		'btc':{'trade':0,'frozen':0},
		'eth':{'trade':0,'frozen':0},
		'eos':{'trade':0,'frozen':0},
	}

完整的代码如下:

```python
 buf = {}
for d in data["data"]["list"]:
    currency = d["currency"]
    currency_data = buf.setdefault(currency, {})
    currency_data[d["type"]] = float(d["balance"])
```

这里面的亮点是

	setdefault 的应用

