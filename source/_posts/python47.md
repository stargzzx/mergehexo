---
title: Jupyter 所遇到的错误
date: 2019-01-20 22:06:19
categories:
- python
tags:
- python
- Anaconda
- anaconda
- Jupyter notebook
---
如题。

<!-- more -->

## 参考资料

[jupyter notebook常见问题解决办法](https://zhuanlan.zhihu.com/p/34337292)

[2018-10-02 Jupyter Notebook environment anaconda3 error](https://www.jianshu.com/p/7d8ce818a923)

## "EnvironmentLocationNotFound: Not a conda environment: /home/xxx/.conda/envs/anaconda3 "

![](/images/python/47_0.png)

conda 版本4.4以后会出现，可以通过以下修复：

This is a confirmed bug in nb_conda for conda version >=4.4:

>The reason is the change of conda info --json output. Fresh installation of version 4.3.33 gives envs=[], version >=4.4 gives envs=[root_env_dir] (always non empty).


修改这个文件：~/anaconda3/pkgs/nb_conda-2.2.1-py36.0/lib/python3.6/site-packages/nb_conda/envmanager.py, 这里的(py36.0) 根据版本可能会有不同：

我的路径如下：E:\anaconda\anaconda3.7\Lib\site-packages\nb_conda

查找这一句:

{% codeblock %}
return {
    "environments": [root_env] + [get_info(env)
                                  for env in info['envs']]
}
{% endcodeblock %}

替换成
{% codeblock %}
return {
    "environments": [root_env] + [get_info(env) for env in info['envs']
                                  if env != root_env['dir']]
}
{% endcodeblock %}

重启 jupyter notebook server，错误提示消失。

## 浏览器页面不能弹出显示

出现这个原因是因为你没有指定一个浏览器来打开jupyter notebook，这就导致jupyter notebook不知如何进入网页，这种情况的出现一般因为电脑上存在两个浏览器导致的，所以只要把其中一个设为默认浏览器就可以了。

## 报错:ImportError: cannot import name 'ensure_dir_exists'
![](/images/python/47_1.jpg)

需要从终端手动更新 jupyter_core and jupyter_client如下

	conda update jupyter_core jupyter_client
	
## notebook启动后，浏览器中的页面出现空白

解决办法是：换个浏览器


