---
title: vue | vue 实战
date: 2020-05-27 19:22:48
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

- [包学会之浅入浅出Vue.js：结业篇](https://cloud.tencent.com/developer/article/1020416)

<br/>

# 列表组件quiList.vue

<br/>

本节我们主要要完成这样一个列表功能，每一行的列表是一个组件，列表内可能出现按钮组件或者箭头组件，点击按钮组件可以自定义事件，同时可以根据不同的参数来决定当前列表是带按钮的列表or带箭头的列表。

<div style="width: 50%;padding-left: 25%">

![](/images/vue/3_0.png)

</div>

首先看看quiList.vue

```html
//quiList.vue
<template>
  <div class="qui-list">
    <span class="list-tips">{{tipsText}}</span>
    <qui-btn v-on:btnClickEvent="btnClickEvent" :msg=msg class="small"></qui-btn>
  </div>
</template>

<script>
  import quiButton from '../components/quiButton.vue'
  export default{
    props:{
      msg: {
        default: '下载'
      },
      tipsText: {
        default: '默认的文案'
      }
    },
    components: {
      'qui-btn': quiButton
    }，
    methods:{
      btnClickEvent:function(){
          alert('按钮点击事件')
      }
    }
  }
</script>
```

上面的知识点基本上就是我们之前学过的，只不过记住`quiList`本身是一个组件，而在这个组件里面，我们又引入了按钮组件`quiButton`，也就是组件内引用组件，实际上就是组件的嵌套，注意到这里`:msg=msg`的使用，这里冒号表示绑定的是一个变量`msg`，然后这个属性通过`props`暴露出去(本身在按钮中就暴露了msg给列表组件使用)，借用下面一张图理解下：

![](/images/vue/3_1.png)

至于点击事件，也是我们之前学习过的事件的绑定。`现在引入一个新问题，是否有一个参数，可以决定列表组件的右侧是放置按钮组件呢？还是箭头组件`。

<br/>

# 动态组件

<br/>

Vue中提供了一些特定关键字`:is`和特定的结构`<component>`来生成动态组件，让我们修改下`script`里面的内容先：

```js
<script>
  import quiButton from '../components/quiButton.vue'
  import quiArrow from '../components/quiArrow.vue'
  export default{
    props:{
      msg: {
        default: '下载'
      },
      tipsText: {
        default: '默认的文案'
      },
      currentView:{
        default: 'qui-btn'
      }
    },
    components: {
      'qui-btn': quiButton,
      'qui-arrow': quiArrow
    },
    methods: {
      clickEvent: function () {

      }
    }
  }
</script>
```

首先我们先`Import`多一个箭头组件，在`components`中添加一个自定义标签‘`qui-arrow`’，注意到我们多了一个`currentView`的自定义属性，默认值是`qui-btn`，现在再看看`template`标签里面写什么：

```html
<template>
  <div class="qui-list">
    <span class="list-tips">{{tipsText}}</span>
    <component :is="currentView" v-on:btnClickEvent="clickEvent" :msg=msg class="small"  keep-alive></component>
  </div>
</template>
```

我们把`qui-btn`标签去掉了，取而代之的是一个`component`标签，这是`Vue`自带的一个标签，可以把它当作一个容器，这个容器可以用来装按钮，也可以用来装箭头。决定这个容器装的是哪个组件的关键代码在于：`is="currentView"`，当`currentView`的值为`qui-btn`的时候，这个容器就是按钮组件，当它是`qui-arrow`的时候，就是箭头组件。而我们刚才给这个变量定义的默认值是`qui-btn`。

`keep-alive`关键字保持这个组件在内存中是常驻的，由于动态组件可能需要动态切换，这样保持组件活跃可以减少组件变化时候的内存消耗。

可以看到我们的`component`上还保留着按钮的点击事件和`msg`信息，这些没有关系，只要箭头组件中不出现同样的变量就不会发生冲突。

```html
<qui-list tipsText="自定义文案，默认右边是按钮" msg="弹层"></qui-list>
<qui-list v-on:btnClickEvent="test"></qui-list>
<qui-list ref="child1" tipsText="最右边是箭头" currentView="qui-arrow"></qui-list>
```

使用列表组件的时候，只需要给暴露出来的`currentView`指定一个值，就可以决定右侧是按钮还是箭头了。注意最后一个`qui-list`上有一个`ref`的属性，这个属性代表组件集合，当页面中有很多组件的时候，可以通过几种方法来获取对应的某个组件的信息：

```js
console.log(this.$children[0].msg);//通过数组获取
console.log(this.$refs.child1.msg);//通过对象集合获取
```

其实关于动态组件，不一定要用：`is+component`来实现，在Vue中有一个指令叫做`v-if / v-else / v-else-if`，统称判断指令，配合展示指令`v-show`，可以根据指定的值来决定对应的组件是否应该展示，另外这种做法我不展示了，就当做一个作业吧，有兴趣的还是建议实战一下，毕竟我们也只是教大家入门学习，后面还是希望大家能够自己去扩展学习。

<br/>

# 生命周期

<br/>

这里简单讲一下什么是组件的生命周期，上面我们通过`refs`来获取组件对象的信息，那么在什么时候或者说哪个时机点去做这件事呢，组件从引用到调用到销毁（比较少操作）有以下几个关键回调函数：

```js
<script>
  export default {
    components: {
      'qui-list': quiList
    },
    beforeCreate:function(){},//组件实例化之前
    created:function(){},//组件实例化了
    beforeMount:function(){},//组件写入dom结构之前
    mounted:function(){//组件写入dom结构了
      console.log(this.$children);
      console.log(this.$refs);
    },
    beforeUpdate:function(){},//组件更新前
    updated:function(){},//组件更新比如修改了文案
    beforeDestroy:function(){},//组件销毁之前
    destroyed:function(){}//组件已经销毁
  }
</script>
```

所以要想使用`refs`的内容，就需要在组件写入`dom`之后才能开始调用哦！

<br/>

# 我还需要学什么

<br/>

目前为止，我们三篇文章已经学了大部分的关于组件和路由的知识，当然这并不是Vue的全部，只是相对于其他的知识点，这些可以算是一个垫脚石，看懂了这些，对后面其他API的应用，帮助很大。下面我列举一些其他的，后续大家可以去官网查看资料的一些关键点，其实都不难，只要有一些小小的项目demo实践，你会发现Vue也不过如此。

## 过渡

过渡其实就是CSS3动画，transition这些，只是写CSS3变成好像在写JS一样，有点类似于greenSock的一些思想。

## 指令

目前为止我们学习了一些常用指令，像v-on，v-bind，v-for，还有几个常用的像刚才提到的判断指令和v-show指令，还有v-model指令（主要用于input等表单组件）。当知道指令作用的时候，学习起来其实并不难。

## Render

渲染这个方法是我觉得应该用心去学习的，它可以方便我们写出更好的面向对象的组件，而学习它的成本在于这个接口更接近于原生JS代码的使用。如果有需要，后续也可以写一篇关于Render的文章。

<a href="/zip/vue/1_0.zip">下载源码</a>

还是建议下载源码来看。