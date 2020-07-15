---
title: gradle | 加载项目中的本地jar文件
date: 2020-07-15 15:58:45
categories:
- [java,基础,gradle]
tags:
- gradle
---
如果你使用的是 `gradle` 作为包管理工具，那么，你导入本地包的方式，也应该遵循 `gradle` 而不是自己随便导入。

<!-- more -->

<br/>

# 参考资料

<br/>

- [使用gradle加载项目中的本地jar文件](https://blog.csdn.net/KingBoyWorld/article/details/78644311?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase)
- [Gradle加载本地jar包](https://blog.csdn.net/m1213642578/article/details/52763130?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)


<br/>

# 背景

<br/>

- IDEA

<br/>

# 正文

<br/>

## 加载单独的jar包

在项目底下添加`libs`目录,将`jar`包仍进`libs`目录

`build.gradle`配置如下:

	dependencies { compile files('libs/ojdbc-14.jar')}

![](/images/gradle/0_0.png)

点击 `gradle` 的 `clean`、`build` 「或许也可以不点击」。

一般在右侧，就会出现相应的包。

如果没有出现，请点击一下图中刷新位置。「另：图中 `build.gradle` 里面写错了，应该是 `libs`」

## 加载某个目录的jar包

在自己的 Gradle 项目里建立一个名为 `libs` （这个名字可以自己定义，不一定非要叫这个名字）的文件夹，把自己本地的 jar 包拷贝到这个文件夹中。

build.gradle配置如下:

	dependencies { compile fileTree(dir:'libs',include:['*.jar'])}
