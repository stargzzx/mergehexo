---
title: 在同一电脑下建立多个 ssh key
date: 2020-01-04 22:44:37
categories:
- ssh
tags:
- ssh
- ssh key
---
这个非常重要，所以，需要写一下，其内容正如标题所展示的那样。

<!--more-->

先说一下我的需求，因为之前我创建了一个 ssh key 添加到公司的项目下面，用来 push 代码，但是，我如果按照正常的方式再建立 ssh key 的话，就会覆盖原来的 ssh key，并且，如果将原来的 ssh key 添加到 github 上，就会提示说，这个 ssh key 已经被使用。

所以，我现在的需求是，在一台电脑上生成多个 ssh key 用来添加到 github 上。

ok，开始我们的操作。

我们使用下面的命令来创建 ssh key。

    ssh-keygen -t rsa -C "邮箱地址" -f ~/.ssh/存储ssh key 的文件名
        比如，我第二个是
        ssh-keygen -t rsa -C "1929926967@qq.com" -f ~/.ssh/rsa_hexo

其实，那个 邮箱地址 可以随便填写，甚至你可以直接填写字段可以，比如 woniu 后面的 -f 是指定哪个文件来保存这个文件

如果，你是用下面的命令来生成 ssh-key

    ssh-keygen -t rsa -C "邮箱地址"

会在接下来的提示中（第一个提示）让你选择存储文件

但是，经过我的测试，如果按照这种方式，你的 ssh-key 的那个保存文件，不知道保存到哪里去了，所以，为了稳妥，还是开始就指定保存的文件。

接下来还有两个提示，直接 enter 就好了。

然后我们会看见在 ~/.ssh 下面有几个文件，其中两个是原来的 ssh-key 的文件，另外两个是你刚才生成的 ssh-key 存储文件。

win 中 ssh 存储的地方是

	C:\Users\Administrator\.ssh

Administrator 这个是登录者的名称

按照上面的生成规则，生成的两个文件名叫做

    rsa_hexo
    rea_hexo.pub

我们只需要把 rsa_hexo.pub 下面的文件添加到github上的 ssh-key setting 就好了。

![](img)

但是，现在仅仅只是生成了 ssh-key ，还得做一下区分，下面的方法是我自创的，完全是权宜之举，我相信还有更好的方法，如果我找到更好的方法，我会来更新的。

你还需要在 ~/.ssh 下创建一个叫 config 的文件

    touch config

然后在 config 文件中配置一下信息。

我的 config 内容如下

    # bbsx
    #Host github.com
    #HostName github.com
    #PreferredAuthentications publickey
    #IdentityFile ~/.ssh/id_rsa
    # hexo
    Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_hexo_mac

    # 配置文件参数
    # Host : Host可以看作是一个你要识别的模式，对识别的模式，进行配置对应的的主机名和ssh文件
    # HostName : 要登录主机的主机名
    # User : 登录名
    # IdentityFile : 指明上面User对应的identityFile路径

因为，每次连接 github，是使用一个 ssh-key 连接的，所以，config 里面有两个 ssh-key 的配置，对于我来说，一个是公司，一个是我私人的。

如果我要上传公司代码，就要注释掉我私人的ssh-key，反之亦然。

然后，我们在 ~/.ssh 下使用下面的命令来测试 git 是否能连接成功。

    ssh -T git@git.com  // 或者其他域名地址

如果使用上面的 config 的配置，使用这个命令，会出现

    Hi benpaodewoniu! You've successfully authenticated, but GitHub does not provide shell access.

如果反过来注释就会出现

    Hi TaylorChen! You've successfully authenticated, but GitHub does not provide shell access.

之所以会出现下面这个情况，就看是哪个github的用户添加的 ssh-key。

后面的纯粹是我琢磨的，你执行完上面的如何可以上传，就可以了，不行再使用下面的。

配置 git 信息，进入你要配置的项目中。

我是 cd blog

然后执行下面的命令

    git config user.name <Git注册用户名>
    git config user.email <Git注册邮箱>

这个命令是在该项目下配置 git 信息，只针对该项目有用。

如果添加了 -global 则会对所有的项目都统一配置。

建议只使用单项目配置。

感谢大家观看，目前，只是权宜之计，很麻烦，如果要上传不同的项目，还得先切换 ssh-key。在接下来的日子里，我会查询相关资料，一次配置就好了，请各位期待。    
