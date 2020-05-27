---
title: vue | 安装 VUE
date: 2020-03-19 17:39:48
categories:
- 网站设计
- 前端设计
- vue
tags:
- vue
---
VUE 安装中有很多需要注意的点。

<!-- more -->

<br/>

# npm 安装

<br/>

这里假设你已经通过 npm 安装完 vue 了。

然后初始化一个 vue 项目。

    vue init webpack my-project-name
        创建一个基于 webpack 模板的新项目

然后在安装的过程中，出现了很多选项

    ? Project name my-project 项目命名
    ? Project description A Vue.js project 项目描述
    ? Author runoob <test@runoob.com>  作者描述
    ? Vue build standalone 构建方式 下面详解
    ? Install vue-router? 安装vue的路由插件 建议 Y
    ? Use ESLint to lint your code? 建议选择 ‘N’  ESLint检测 下面详解
    ? Set up unit tests? 是否安装单元测试 建议选 N
    ？Setup e2e tests with Nightwatch? 是否安装E2E测试框架NightWatch（E2E，也就是End To End，就是所谓的“用户真实场景”。）建议 N
    ？Should we run `npm install` for you after the project has been created? (recommended) (Use arrow keys) 选项有三个（上下箭头选择，回车即为选定）建议 : yes use npm
    
## 构建方式

    Vue build 
    ❯ Runtime + Compiler: recommended for most users 
      Runtime-only: about 6KB lighter min+gzip, but templates (or any Vue-specific HTML) are ONLY allowed in .vue files - render functions are required elsewhere
  
两个选择（上下箭头选择，回车即为选定）建议选择 : Runtime + Compiler:recommended for most users

这里推荐使用1选项，适合大多数用户的。

## ESLint检测

ESLint 是一个语法规则和代码风格的检查工具，可以用来保证写出语法正确、风格统一的代码。

建议选择 ‘N’ 因为选择 ‘Y’ 在做调试项目时,控制台会有很多 黄色警告 提示格式不规范,但其实并不影响项目

这个强烈建议选 N ，如果选择 Y ，其采用的是 strict code ,正常人写法都会出错。

如果不符合代码规范，那么会导致连编译都通不过。

我反正被这个坑死了。

<br/>

# 使用

<br/>

    cd project
    npm install
    npm run dev

 