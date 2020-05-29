---
title: anaconda | 迁移虚拟环境
date: 2020-05-29 20:25:10
categories:
- [python,中间件,anaconda]
tags:
- anaconda
---
因为创建的虚拟环境是在各自的 `home` 下面，但是，`home` 的大小是固定的，现在使用的人太多，需要将环境进行迁移。

整个背景是 `ubuntu16.04`。

<!-- more -->

假设用户是 aaa ,那么

	cd ~
	ls -lah

可以发现有下面的文件和文件夹

	.conda
	.condarc

其中 `.conda` 是放置虚拟环境的，`.condarc` 是配置文件。

## .conda

这个文件夹里面存放着

- environments.txt
- envs
- pkgs

其中，`environment.txt` 是各个环境的指向。

	cat environment.txt
		/home/aaa/.conda/envs/py27
		/home/aaa/.conda/envs/py36

然后 `envs` 存放着我们弄的那些环境，`pkgs` 存放的是各种第三方库。

我们运行

	conda info

里面有一部分是

		  package cache : /Users/aaa/anaconda3/pkgs
                          /Users/aaa/.conda/pkgs
       envs directories : /Users/aaa/anaconda3/envs
                          /Users/aaa/.conda/envs

我们先做的第一步就是先把之前的那些环境迁移出去。

假设 `/data` 足够大，我们创建下面的路径

	/data/aaa/.conda

然后将

	mv ~/.conda/envs /data/aaa/.conda
	mv ~/.conda/pkgs /data/aaa/.conda

迁出去之后，我们要修改 `environment.txt` 将内容换成

	/data/aaa/.conda/envs/py27
	/data/aaa/.conda/envs/py36

然后 

	cd ~
	vim .condarc

修改配置信息，添加

	envs_dirs:
	  - /data/aaa/.conda/envs
	pkgs_dirs:
	  - /data/aaa/.conda/

再次执行 `conda info`

		  package cache : /Users/aaa/anaconda3/pkgs
                          /data/aaa/.conda/pkgs
       envs directories : /Users/aaa/anaconda3/envs
                          /data/aaa/.conda/envs

至此，迁移成功。
