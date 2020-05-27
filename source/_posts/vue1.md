---
title: vue | vue 之 路由篇
date: 2020-05-27 17:45:04
categories:
- 网站设计
- 前端设计
- vue
- 认知三篇
tags:
- vue
---
vue 的代码文档写的真的不忍直视，一言难尽。

而，现在网络上流行的教程都一言难尽，没有从核心-大局来进行讲解，后来，我看到了一个非常优秀的导学，所以，移植到我的博客中，供大家学习。

<!-- more -->

<br/>

# 参考资料

<br/>

- [包学会之浅入浅出Vue.js：开学篇](https://cloud.tencent.com/developer/article/1020337)

<br/>

# 什么是Vue.js

<br/>

不管你想不想了解，你只需要大概知道，`Vue`就是和`jQuery`一样是一个前端框架，它的中心思想就是数据驱动，像远古时代的老前辈jQuery是结构驱动，什么意思呢，以前我们写代码时常用`$('.dom').text('我把值改变了')`，这种写法先要获得结构，然后再修改数据更新结构，而Vue的做法直接就是`this.msg="我改变了"`，然后msg就会同步到某个结构上，视图管理抽象为数据管理，而不是管理dom结构了。不懂没关系，慢慢来。

还有一点必须要知道的是，Vue是国人写的，技术文档也妥妥的是中文，想到这我就有学习的动力。

<br/>

# 搭建环境

<br/>

可以看我之前的博文。

- [vue | 安装 VUE](https://benpaodewoniu.github.io/2020/03/19/vue0/)

- `npm install`

是安装项目所需要的依赖，简单理解就是安装一些必要的插件，需要等一段时间；

- `npm run dev`

是开始执行我们的项目了，一旦执行这个命令之后，等一小会，浏览器应该会自动帮你打开一个tab为http://localhost:8080/#/的链接，这个链接就是我们本地开发的项目主页了，如果没有，说明出错了。请移步到评论区回复吧。。。

(PS：开发完成后执行 `npm run build` 会编译我们的源代码生成最终的发布代码，在dist目录下)

<br/>

# 目录解析

<br/>

|目录/文件|	说明|
|---|---|
|build|	项目构建(webpack)相关代码|
|config|	配置目录，包括端口号等。我们初学可以使用默认的。|
|node_modules|	npm 加载的项目依赖模块|
|src|	这里是我们要开发的目录，基本上要做的事情都在这个目录里。里面包含了几个目录及文件：<br/>assets: 放置一些图片，如logo等。<br/>components: 目录里面放了一个组件文件，可以不用。<br/>App.vue: 项目入口文件，我们也可以直接将组件写这里，而不使用 components 目录。<br/>main.js: 项目的核心文件。|
|static|	静态资源目录，如图片、字体等。|
|test|	初始测试目录，可删除|
|.xxxx文件|	这些是一些配置文件，包括语法配置，git配置等。|
|index.html|	首页入口文件，你可以添加一些 meta 信息或统计代码啥的。|
|package.json|	项目配置文件。|
|README.md|	项目的说明文档，markdown 格式|

我们需要关注的仅仅只有

- package.json
    - 保存一些依赖信息
- config
    - 保存一些项目初始化配置
- build
    - 里面保存一些webpack的初始化配置
- index.html
    - 是我们的首页
- src目录
    - 我们的代码所在

index在很多服务器语言中都是预设为首页，像index.htm，index.php等，打开build目录中的`webpack.base.conf.js`，会看到这样的代码

```javascript
module.exports = {
  context: path.resolve(__dirname, '../'),
  entry: {
    app: './src/main.js'
  },
```

说明我们的入口js文件在src目录中的main.js，接下来我们就分析下这些初始化代码先；

<br/>

# 跟着代码走

<br/>

Vue的核心架构，按照官方解释和个人理解，主要在于

- 组件
- 路由

两大模块，只要理解了这两大模块的思想内容，剩下API使用就只是分分钟的事情了。所以在我的系列文中，会围绕组件和路由教大家开发一个前端组件库，这个过程也是我个人学习的练手项目，个人觉得一步步做下来之后，对Vue的理解就可以算是出师了，胜过读10遍书籍文档，那是后话了，先让我们看看最基本的Vue生成的默认代码吧！

## main.js

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
})
```

先是第一句

    import Vue from 'vue'

这句很好理解，就像你要引入`jQuery`一样，`vue`就是`jquery-min.js`，然后Vue就是`$`；然后又引入了`./App`文件，也就是目录中和main.js同级的App.vue文件；在Vue中引入文件可以直接用`import`，文件后缀名可以是`.vue`，这是Vue自己的文件类型，之前说的webpack会将js和css文件打包，同样的道理，在webpack中配置vue插件后（项目默认配置），webpack就可以将.vue类型的文件整合打包，这和nodeJs中require差不多的道理。

说回`App.vue`这个文件，这是一个视图（或者说组件和页面），想象一下我们的index.html中什么也没有，只有一个视图，这个视图相当于一个容器，然后我们往这个容器中放各种各样的积木（其他组件或者其他页面），App.vue中的内容我们后面说；

    import router from './router'

这句代码引入一段路由配置，同样的后边说（很快就说到的不用急）

接下来的 `new Vue`实例化，其实就相当于平时我们写js时候常用的init啦，然后声明`el：'#app'`，意思是将所有视图放在`id`值为`app`这个`dom元素`中，components表明引入的文件，即上述的App.vue文件，这个文件的内容将以`<App/>`这样的标签写进去`#app`中，总的来说，这段代码意思就是将App.vue放到`#app`中，然后以`<App/>`来指代我们的`#app`。

```javascript
import Vue from 'vue'
import App from './App'/*引入App这个组件*/
import router from './router'/*引入路由配置*/

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',/*最后效果将会替换页面中id为app的div元素*/
  router,/*使用路由*/
  template: '<App/>',/*告知页面这个组件用这样的标签来包裹着,并且使用它*/
  components: { App }/*告知当前页面想使用App这个组件*/
})
```

<br/>

# 单页面组件

<br/>

好了，现在打开我们的App.vue文件，在Vue中，官网叫它做组件，单页面的意思是结构，样式，逻辑代码都写在同一个文件中，当我们引入这个文件后，就相当于引入对应的结构、样式和JS代码，这不就是我们做前端组件化最想看到的吗，从前的asp、php也有这样的文件思想。

## App.vue

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

node端之所以能识别`.vue`文件，是因为前面说的webpack在编译时将.vue文件中的html，js，css都抽出来合成新的单独的文件。

单页面组件会在后面的实战中完整体现，这里先不做过多描述；

看到我们文件内分为三大部分，分别是`<template><script><style>`，很好理解结构，脚本，样式；

script就像node一样暴露一些接口，可以看到我们的template标签中除了一张图片之外就只有一行代码：`<router-view></router-view>`

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view></router-view>
  </div>
</template>
```

回看我们的浏览器页面中，图片是有了，可下面的文本和链接的代码写在哪里了呢？这里就要开始涉及路由了。


<div style="width: 50%;padding-left: 25%">

![](/images/vue/1_0.png)

</div>

<br/>

# 路由

<br/>

这里补充下路由的大致概念：传统的php路由是由服务器端根据一定的url规则匹配来返回给前端不同的页面代码，如以下地址

- https://isux.tencent.com/about
- https://isux.tencent.com/recruit

