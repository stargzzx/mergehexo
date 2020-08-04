---
title: java | nexus 私有仓库
date: 2020-07-15 10:29:41
categories:
- [java,进阶,nexus]
tags:
- nexus
---
公司需要创建 java 的私有仓库，所以，把构建过程在这里记录一下。

<!-- more -->

<br/>

# 参考资料

<br/>

- [搭建私有Maven仓库](https://www.gcssloop.com/gebug/maven-private)
- [自建 Maven 仓库](https://juejin.im/post/5dabcdc66fb9a04dde1479ae)
- [Maven上传本地jar到Nexus](https://www.jianshu.com/p/95c72f33d580)

<br/>

# 背景信息

<br/>

- MacBook
- java 14

公司的运维小哥，成功在 ubuntu16.04 上部署。

<br/>

# 下载 nexus、安装

<br/>

[下载地址](https://www.sonatype.com/download-oss-sonatype)

根据自己的平台下载相应的版本。

下载后是一个压缩包文件，然后解压缩后，会出现两个文件。

|目录|	备注|
|---|---|
|nexus-x.x.x-xx|程序目录:	这个文件夹是存放应用程序的|
|sonatype-work|	项目目录:这个文件夹是存放仓库和设置等相关内容的，如果备份数据，只用备份这个文件夹即可|

上面的两个文件夹无需安装，所以，要找一个好的路径放置他们，另外，`nexus-x.x.x-xx` 下面简写 	`nexus`。

初次配置，我们只用关注如下几个文件即可：

|文件|	备注|
|---|---|
|./nexus-3.8.0-02/bin/nexus|	运行程序|
|./nexus-3.8.0-02/bin/nexus.rc|	用户配置|
|./nexus-3.8.0-02/etc/nexus-default.properties|	端口配置|

初期需要了解的文件就这三个，在 Linux 系统上使用 root 用户直接运行可能会警告，因此可以配置一下`nexus.rc`文件。至于端口号，默认是 8081，如果对这个没特殊要求，默认即可。

至于 `./nexus-3.8.0-02/bin/nexus` 是主要的运行程序，建议将 bin 目录配置到环境变量中，这样就可以在任意位置启动和停止该程序了，否则只有在 bin 目录下才能调整。

ps：在 `.bash_profile` 配置。

<br/>

# 运行

<br/>

在命令行工具中输入启动命令：

	nexus start

如果一切顺利，在等待几十秒到一两分钟之后就可以查看我们的仓库了，如果出错了，可以使用 run 命令来查看具体的出错原因：

`run` 命令相当于 `debug` 模式，会输出所有的日志信息

	nexus run

当然，Nexus 还有很多其他命令(例如:停止、重启、查看状态等)：

	nexus {start|stop|run|run-redirect|status|restart|force-reload}

<br/>

# 查看

<br/>

在输入 `nexus start` 命令后，稍微等待一两分钟，就可以查看仓库了，如果在本机有图形化界面，直接在浏览器中输入 `http://localhost:8081`即可查看，如果修改了端口号，后面写对应的端口号即可。如果是运行在服务器上，则在其他电脑上输入`http://{服务器ip}:{port}` IP 和对应的端口号。如果运行成功，则会看到类似如下界面

![](/images/nexus/0_0.png)

<br/>

# 仓库配置

<br/>


经过上面的步骤，我们就有了一个空仓库，但是这个仓库还还需要进行一些基础的配置。

## 账号配置

一开始 `sign in` 的时候，会弹出一个框，告诉你 `password` 存在哪里。

ps:我最开始输出密码的时候，后面跟着一个 `%`,我把 `%` 去掉之后，密码才对。

然后，登录进去后，我们需要重新设置密码。

## 创建用户

![](/images/nexus/0_1.png)

### 填写用户信息

![](/images/nexus/0_2.png)

填写具体的用户信息，其中比较重要的部分已经用红字标记出来了。

注意：

- 注意用户权限，默认应该是有两个权限，管理员权限和所有用户(包括未登录用户)权限。
- Roles 左侧为目前的权限组，右侧为当前用户拥有的权限组，可以通过 [>] 和 [<] 按钮来调整用户权限。
- 在创建完新的用户后记得删除最初的管理员账号或者修改管理员账号的密码。
- 用户所拥有的权限，是根据其拥有的权限组决定的，只要拥有 nx-admin 权限组，就拥有所有的管理权限，要注意该权限的分配。

## 创建仓库

仓库也是在设置中进行创建的，如下

![](/images/nexus/0_3.png)

![](/images/nexus/0_4.png)

我们用 maven 仓库即可，可以看到，仓库主要有三种类型：

- `hosted`： 本地仓库，我们一般使用这种类型的仓库。
- `proxy`： 代理仓库，用于代理其他远程仓库。
- `group`： 仓库组，用来合并多个 hosted / proxy 仓库。

在一般情况下，我们创建 hosted 类型的 maven 仓库。

### 填写仓库信息

![](/images/nexus/0_5.png)

如果没有特殊需求，直接填写一个名称然后点击创建即可。

注意：

- Version policy(版本模式) 有三种模式 Release(发布)、Snapshot(快照)、Mixed(混合)。
- Layout policy(布局模式) 有两种模式 Strict(严格模式)、Permissive(宽松模式)。
- Deployment policy(部署模式) 有三种模式 Allow redploy(允许重新部署)、Disable redploy(不允许重新部署)、Read-only(只读)。

一般情况下按照默认配置即可，需要注意的是，部署策略一般情况下请使用默认配置 Disable redploy(不允许重新部署)，例如，v1.0.0 版本的组建上传后，修改后再次以 v1.0.0 版本进行上传会上传失败，这样可以保证版本上传后不会被误覆盖掉，如果确定之前 v1.0.0 版本是错误上传的，需要重新上传，可以手动删除后再次上传，这样是最稳妥的。

在创建完成后就可以在仓库列表中查看到新创建的仓库了。

![](/images/nexus/0_6.png)

## 用户权限配置

用户权限配置相对来说稍微复杂一点，如果开发人员较少，又都是老手，不会存在小白删库的情况下，可以给所有人都分配一个管理账号或者共用一个管理员账号即可。

如果开发人员众多，又比较复杂，则需要仔细的控制每一个用户的权限，一般来说，给普通用户分配公共仓库的查看、上传、读取的权限，不要给编辑和删除的权限。 下面就带大家了解一下权限的详细设置方式：

Nexus 默认就有一个游客账号(anonymous)，在默认状态下拥有浏览所有仓库的权限，即在不登陆的情况下，可以查看所有仓库。

![](/images/nexus/0_7.png)

该账号的权限分配十分重要，它意味这赋予所有未登录用户的权限，如果将管理员权限分配给了该账号，就意味着所有未登录用户都可以使用管理员权限，如果没有特殊情况，不要随意修改该账号的默认权限。

如果你不希望未登录用户查看，可以选择取消启用未登录账号，如下：

![](/images/nexus/0_8.png)

然而，如果我们希望游客可以查看和使用一部分仓库，另外部分仓库又不希望被未登录用户看到，那么就需要详细进行设置了。

既然要修改权限，就要了解 Nexus 的权限管理方式，首先与权限有关的有如下几种：

![](/images/nexus/0_9.png)

它们的关系如下：

![](/images/nexus/0_10.jpg)

注意：

- 用户是通过持有不同的角色(权限组)来确定自己的权限管理范围的。
- 角色(权限组)可以由基础权限和其他基础权限组来构成。

上面是 Nexus 支持的权限构成方式，在一般情况下我喜欢按照下面这种方式组织自己的权限。

![](/images/nexus/0_11.jpg)

我把用户分为四类(超级管理员、仓库管理员、普通用户、游客)，并创建了四种类型的角色(权限组)，分别与之对应，这样在添加新用户的时候只需要赋予他对应的角色(权限组)即可。

### 游客权限配置

创建角色(权限组)

![](/images/nexus/0_12.png)

创建游客角色

其他的角色(权限组)也按照该方式进行创建即可，假设我们想把 gcssloop-central 这个仓库作为公开仓库，将这个仓库的查看和读取权限给予游客角色。

![](/images/nexus/0_13.png)

赋予游客用户权限

![](/images/nexus/0_14.png)

基础的设置方式就如上所示，只是不同的用户角色，拥有不同的权限而已。

### 普通用户权限配置

![](/images/nexus/0_15.png)

### 管理员权限配置

![](/images/nexus/0_16.png)

上面是一些常见的权限分配，比较粗略，如果有需要的话，可划分的更加细致，在创建完角色(Role)后，把对应的角色分配给对应的用户，该用户便可以获得对应的权限，超级管理员的角色(nx-admin)是默认就有的(拥有所有仓库的管理权限，以及修改设置的权限等)，它无需自己配置权限内容，只用分配给超级管理员用户使用即可。

<br/>

# 仓库备份

<br/>

为了保证数据的安全，一定要对仓库数据进行备份，这样在出现意外情况的时候可以快速的恢复仓库状态，将影响降到最低。

## 单仓库备份方案

仓库备份其实很简单，<b>Nexus 的所有信息都存储在 sonatype-work 文件夹中，单一仓库需要定时的备份该文件夹，一天至少备份一次以上，在服务器上运行的可以自己弄一些定时脚本来帮助自动化备份</b>。

恢复备份需要先关闭 nexux，让备份目录覆盖当前工作目录，之后重新启动 nexus 即可。

## 多仓库同步方案

可以在多个的电脑(服务器)上搭建多个相同版本的仓库，以其中一个为主仓库，定时同步 sonatype-work 文件夹到其他备份仓库。

## 多仓库同时上传

在拥有多个仓库的情况下，可以在上传脚本中配置多个仓库的上传方案，这样在发布的时候，分别发布到不同的仓库中，脚本的详细信息见下文。

## 多仓库部署方案

我自己使用的是多仓库方案，同时部署多个仓库，这样在其中一个仓库出现问题的时候迅速切换到其他仓库，可以在不影响开发的情况下对该仓库进行修复。

个人建议，直接把三个平台(Windows、Linux、OSX)的对应的同一版本都下载保存下来，这样方便以后部署，防止因为版本不同导致出现问题。

我自己在内网服务器(Winsows Server)上部署了一套系统，公网服务器(Linux)上部署了一套系统，又在自己的个人电脑(OXS)上部署了一套系统，也算是在所有平台上都部署过了，部署过程大致上是相同的，具体可以见上文。

如果是多仓库部署的话，首先要部署并设置好一个仓库，之后部署时直接将设置完成仓库的 sonatype-work 文件夹复制后后续的部署平台上，这样就可以避免重复的设置，所有仓库的设置就可以保证是一致的。

我自己使用的是多个仓库，并绑定了域名解析，这样在一个仓库出现问题的时候，只用将域名暂时解析到正常的仓库即可。

<br/>

# 手动上传

<br/>

登陆之后，点击 「upload」

![](/images/nexus/0_19.png)

点击之后，选择要上传的 仓库。

![](/images/nexus/0_20.png)

- Extension: 文件的后缀
- 红框里的一定要勾选

上传完之后，再次访问该仓库，就会出现

![](/images/nexus/0_21.png)

然后，我们就要配置 `build.gradle`

![](/images/nexus/0_22.png)

它的这个地址获取在

![](/images/nexus/0_23.png)

这里面有两个填写仓库地址的地方，一定不要填错地方。

然后，就可以配置

	implementation 'com.futu:ftapi4j:1.0.0'

<br/>

# 上传脚本

<br/>

仓库地址可以从下面这个地方获得：

![](/images/nexus/0_17.png)

## 编写上传脚本

### gradle.properties

该文件放置在 项目下，和 `build.gradle` 同一级。

这个文件是，`gradle` 默认读取的，里面用于放置参数，然后 `gradle` 执行的时候，会读取这些参数。

```gradle
USERNAME=admin
PASSWORD=密码
GROUP_ID=com.bixi
ARTIFACT_ID=ant
VERSION=1.0.0
URL_LOCAL=http://localhost:8081/repository/jant/
```

### push.gradle

这个也放在目录下面，和 `build.gradle` 同一级目录。

```gradle
apply plugin: 'maven'

task uploadArchives(type: Upload) {
    group = 'upload'
    configuration = configurations.archives
    uploadDescriptor = true
    repositories {
        mavenDeployer {
            repository(url: URL_LOCAL) {
                authentication(userName: USERNAME, password: PASSWORD)
            }
            pom.version = VERSION
            pom.artifactId = ARTIFACT_ID
            pom.groupId = GROUP_ID
        }
    }
}
```

上传脚本 `push.gradle` 可以放置到任何地方，项目中、本地的指定位置、甚至是网络上，这样在使用的时候只需要添加一下引用即可。

### build.gradle

这个在某一位置（放在后面）添加一下 `push.gradle` 的引用。

```gradle
apply from: "push.gradle"
```

因为是同一级，所以，直接引用即可。

在配置完善后同步一下项目，就可以打开 `gradle` 命令菜单看到多出来了 `upload` 选项。

<div style="width: 50%;padding-left: 25%">

![](/images/nexus/0_18.png)

</div>

- [自建 Maven 仓库](https://juejin.im/post/5dabcdc66fb9a04dde1479ae)

如果，我写的不够清晰，可以看看上面的链接。

<br/>

# 依赖配置

<br/>

## 添加仓库地址

在需要使用该 Library 的项目的根 build.gradle 中配置仓库地址(例如)：

	allprojects {
	    repositories {
	        jcenter()
	        maven {
	            // 配置用户名和密码
	            credentials { username 'GcsSloop' password 'xxxxxx' }
	            // 配置仓库地址（获取方式见上文）
	            url "http://localhost:8081/repository/gcssloop-central/"
	        }
	    }
	}

## 添加具体Library

在需要添加该项目的Module的对应 build.gradle 下添加依赖。

	// <包名>:<项目名>:<版本号>，例如：
	compile 'com.gcssloop.demo:uploader:1.0.0'
