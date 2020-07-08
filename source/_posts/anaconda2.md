---
title: anaconda | 新创建环境后 pip 出错
date: 2020-07-08 22:20:57
categories:
- [python,中间件,anaconda]
tags:
- anaconda
---
突然有一天登录集群，然后创建虚拟环境，接着使用 pip 然后出现了这个错误

	ModuleNotFoundError: No module named 'pip._internal.cli.main'

<!-- more -->

## 参考资料

- [参考资料](https://www.pythonf.cn/read/104406)
- [pip升级时报错\-\-\- No module named 'pip.\_internal'](https://blog.csdn.net/qq_21437451/article/details/81490932)
- [常见问题及解决办法](https://zhuanlan.zhihu.com/p/122821278)

## 解决错误

先说一下，环境如下

- ubuntu16.04
- python3.7

整个错误大概是这样的

	Traceback (most recent call last):
	  File "/data/yhh/.env/ttsr/bin/pip", line 6, in <module>
	    from pip._internal.cli.main import main
	ModuleNotFoundError: No module named 'pip._internal.cli.main'

然后我们修改上面的文件

	vim /data/yhh/.env/ttsr/bin/pip

初始内容如下：

```python
# -*- coding: utf-8 -*-
import re
import sys

from pip._internal.cli.main import main

from pip._internal import main

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```

上面的参考资料我试过，都不行，但是，经过参考资料的总结，我最后得到了我能行的方案。

所以，如果，我下面的方案不行，请你看看参考资料

```python
import re
import sys

from pip._internal import main

from pip._internal import main

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```
