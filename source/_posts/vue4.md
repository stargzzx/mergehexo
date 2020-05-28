---
title: vue | 在 MacBook 上部署 web 服务
date: 2020-05-27 23:07:39
categories:
- [网站设计,前端设计,vue]
- [MacBook]
tags:
- vue
---
先说一下我自己的需求，前后端分离，vue 作为前端架构。

但是，这是我第一次自行部署，所以在这里记录一下。

<!-- more -->

<br/>

# 生成

<br/>

运行

    npm run build

这一步是将你写的代码进行打包，其打包的过程是 vue 自己弄的，不需要管。

这一步成功之后，在项目中就会生成一个 `dist` 目录，里面就存着生成的项目代码。

## 异常

如果运行 `npm run build` 出现 `npm ERR! missing script:` 这种错误。

那么，我们先查看文件 `package.json` 是否有

```json
"scripts": {
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "build": "node build/build.js"
  },
```

### 情况一：

可就是好不到，什么原因呢？最后一看路径不对，vue init webpack my-project时，自己又建立一个文件夹,取不到对应的package.json中的dev.

cd 到对应文件夹，重新运行npm run dev 就OK了。

### 情况二：

还有一种情况，打开的是当前文件夹，但是文件夹package.js里的scripts确实没有dev,

输入vue init webpack  将package.json中丢失的：

```json
"dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
"start": "npm run dev",
"build": "node build/build.js"
```

重新下载过来，然后在npm intall 安装依赖，最后 npm run dev即可

也适用于解决`start, build`丢失。

### 情况三：

运行命令   `npm run dev` 出现     `npm ERR! missing script: dev`  的错误

这是因为vue 版本问题，使用 npm run serve 来运行项目。

<br/>

# 开启 MacBook web 服务

<br/>

Mac OS X 自带了 

- Apache
- PHP

我们只需要简单的启动它就行了。

## 开启 apache

开启Web服务器的方法有两种：

- 通过在terminal终端直接运行Apache的启动命令来打开：sudo apachectl start
    - 这个我试了没用，或者说，这两个步骤都需要搞
- 打开“系统设置偏好（System Preferences）” -> “共享（Sharing）” -> “Web共享（Web Sharing）”

![](/images/vue/4_0.png)

启动后，我们在浏览器输入 `http://localhost` 会发现,页面出现

    it works
    
Apache服务器默认的web根目录在：`/Library/WebServer/Documents`

Apache的配置文件在：`/etc/apache2`

我们

    cd /Library/WebServer/Documents
    ls

出现

- PoweredByMacOSX.gif
- PoweredByMacOSXLarge.gif
- index.html.en

其中，`index.html.en` 的内容就是

    <html><body><h1>It works!</h1></body></html>

下一步，我们将项目中  `dist` 里面的内容，迁移到 `/Library/WebServer/Documents` 中。

运行

    sudo mv ~/项目路径/dist/* ./
    sudo rm index.html.en 

再次用浏览器访问

    http://localhost

出现

![](/images/vue/4_1.png)

附录

- 停止 Apache：sudo apachectl stop
- 重启 Apache：sudo apachectl restart

关于如何开启 php ，你可以查看下面的博文。

- [10分钟教会你Mac上搭建Web服务器](https://blog.csdn.net/ismilesky/article/details/53894259)