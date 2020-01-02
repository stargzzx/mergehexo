---
title: 在运用 numpy 时出现的异常
date: 2018-06-20 10:54:03
categories:
- python
tags:
- python 第三方库
- python
- 异常
- numpy
---
这里记载了运用 numpy 出现的异常，以及解决方案。
 <!-- more -->
## TypeError: only size-1 arrays can be converted to Python scalars
{% codeblock %}
 
import numpy as np
import math
data = np.mat(np.array([[1],[2],[3]]))
print(math.exp(data)) 
 
{% endcodeblock %}
因为 math.exp() 只能对单一数据做处理，而不能对矩阵做处理，所以可以改为：
{% codeblock %}
 
import numpy as np
import math
data = np.mat(np.array([[1],[2],[3]]))
data = np.mat([math.exp(a) for a in data]).T
print(data)
	# [[ 2.71828183]
		[ 7.3890561 ]
		[20.08553692]]
	# 最后面的那个 T，是为了转置成为向量
 
{% endcodeblock %}
