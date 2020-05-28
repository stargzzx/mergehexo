---
title: vue | 引入 less
date: 2020-05-28 16:44:13
categories:
- [网站设计,前端设计,vue]
tags:
- vue
---
CSS 是一门非程序式语言，没有变量、函数、SCOPE（作用域），需要书写大量看似没有逻辑的代码，不方便维护及扩 展，不利于复用，尤其对于非前端开发工程师来讲，往往会因为缺少 CSS 编写经验而很难写出组织良好且易于维护的 CSS 代码。为了方便前端开发的工作量，出现了sass和less.

Less 扩充了 CSS 语言,增加了诸如变量、混合(mixin)、运算、函数等功能。 Less 既可以运行在服务器端(Node.js 和 Rhino 平台)也可以运行在客户端(浏览器)。

<!-- more -->

## 安装

    npm install less less-loader --save

## 配置

编辑 build 中的 `webpack.base.conf.js` 文件，修改 `module>rules` 添加：

```js
{ 
    test: /\.less$/,
    loader: "style-loader!css-loader!less-loader",
},
```

![](/images/vue/6_0.png)

## 使用

less 的使用一共有三种方式。

- 直接使用
- 引用
- 全局引用

### 直接使用

```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>
<script>
export default {
  name: 'app'
}
</script>
<style>
#app {
  color: #2c3e50;
}
</style>
```

其中，style标签内应写入的是该组件使用的样式（如果标签内不加scoped属性，则标签内的样式可以跨组件复用）。

将less写入单组件，只需要在style标签内加上属性

```vue
<style lang='less'>
@mainColor: #2c3e50;
#app {
  color: @mainColor;
}
</style>
```

使用这种方式需要注意：在单组件内定义的less变量，作用域仅限于该组件，无法跨组件使用。

### 引用

第二种使用方式就是将样式写入.less文件，通过

定义一个 base.less

然后在另一个 index.less 中引用。

```less
@import url('../../common/base.less');
```

上面有时候也可以写成

```less
@import '../../common/base.less';
```

如果，index.less 和 index.vue 同一级目录，那么在 index.vue 可以这样引入 less.

```vue
<style src="./index.less"></style>
```

这在一定程度上缓解了方法1带来的多组件共用less变量的问题，但是并没有从根本上解决问题。当共用的组件很多时，我们只能每个文件都import一次。这时我们就可以考虑使用全局less变量。


注意，当你 less 文件出错的时候，使用 @import 就会出错，这个问题困扰我好久了。

### 全局引用

这个我没用过，我先放一下全局引用的链接吧，我个人觉得，目前，我还用不到。

- [教程：在vue-cli中使用less的三种方式：写入单文件、引入和全局变量](https://honey3go.github.io/2017/11/16/%E6%95%99%E7%A8%8B-%E5%9C%A8vue-cli%E4%B8%AD%E4%BD%BF%E7%94%A8less%E7%9A%84%E4%B8%89%E7%A7%8D%E6%96%B9%E5%BC%8F%EF%BC%9A%E5%86%99%E5%85%A5%E5%8D%95%E7%BB%84%E4%BB%B6%E3%80%81%E5%BC%95%E5%85%A5%E5%92%8C%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F/)

文中说无法热更新，但是，我在其他地方貌似看到了解决方案，但是，由于不用，我就没验证，感兴趣的同学可以研究一下研究一下。




