---
title: 命令行运行 gradle 项目
date: 2020-07-30 13:52:26
categories:
- [java,基础,gradle]
tags:
- gradle
---
之前都是在 IDEA 上直接运行 gradle 项目的，现在，需要在命令行中运行。

<!-- more -->

进入项目的根目录，也就是有

- build.gradle

的那层目录，运行

	gradle build

会生成一个 `build` 的文件夹。

然后

	cd build/libs

会发现里面有一个 `jar` 文件，这个文件便是通过 `gradle` 的配置文件，将项目进行打包，比如，我们的 `jar` 文件叫做 `ant-1.0.jar`。

然后我们执行

	java -jar ant-1.0.jar 「后面跟着参数」

就能顺利执行了！