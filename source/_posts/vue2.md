---
title: vue | vue 之 组件篇
date: 2020-05-27 19:22:43
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

- [包学会之浅入浅出Vue.js：升学篇](https://cloud.tencent.com/developer/article/1020338)

<br/>

# 需求背景

<br/>

组件库是做UI和前端日常需求中经常用到的，把一个按钮，导航，列表之类的元素封装起来，方便日常使用，调用方法只需直接写上`<qui-button></qui-button>`或者`<qui-nav></qui-nav>`这样的代码就可以，是不是很方便呢，接下来我们将要完成以下页面：

![](/images/vue/2_0.png)

这是我们组件库的首页，包含三个子页面，按钮页面、列表页面、导航页面；点击进去子页面会由路由来配置。先看我们的目录结构：

![](/images/vue/2_1.png)

- pages目录存放我们的页面，包括首页和三个子页面；
- components目录存放我们的具体组件，包括按钮组件，箭头组件，列表组件和导航组件（组件和页面其实是一样的文件类型，只是由于功能不一样，我们就叫不同的称呼）

先看路由配置的代码吧！

<br/>

# 路由配置

<br/>

```vue
import Vue from 'vue'
import Router from 'vue-router'
// 引用页面模板->供路由使用
import index from '../pages/index.vue'
import pageQuiButton from '../pages/pageQuiButton.vue'
import pageQuiList from '../pages/pageQuiList.vue'
import pageQuiNav from '../pages/pageQuiNav.vue'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'index',
      component: index
    },
    {
      path: '/btn',
      name: 'btn',
      component: pageQuiButton
    },
    {
      path: '/list',
      name: 'list',
      component: pageQuiList
    },
    {
      path: '/nav',
      name: 'nav',
      component: pageQuiNav
    }
  ]
})
```

有了上一篇的分析之后，这里应该很容易看出来几个路由地址

- 首页：`http://localhost:8080/#/`
- 按钮子页：`http://localhost:8080/#/btn`
- 列表子页：`http://localhost:8080/#/list`
- 导航子页：`http://localhost:8080/#/nav`

具体每一页的内容分别对应每一页的.vue文件，不知大家是否还记得入口页`App.vue`，这个文件承载着一些公用的元素，还有就是一个路由容器，我们的首页`index.vue`到时候也是挂载在路由容器中的，看看`App.vue`的代码。

入口页App.vue

```vue
<template>
  <div id="app">
    <h1 class="page-title"><a href="#/">开发组件库</a></h1>
    <router-view></router-view>
  </div>
</template>

<script>
  export default {
    name: 'app'
}
</script>

<style scoped>
  @import './assets/css/App.css';
</style>
```

简单分析一下入口页的代码，h1标签是一个公用元素，也就是说到时候每个子页面都会带着这个h1，他的作用就是方便我们快速回到首页，子页面的内容会注入到`router-view`中。这里值得关注的地方是style标签，我们可以在style标签里面直接写样式，也可以直接引入一个样式文件，scoped关键字表示这个样式是私有的，也就是说，即使两个组件写着一样的`#app{}`样式也不会冲突，程序会加上命名空间，这也就是为什么在script标签中有个name参数。

首页index.vue

```vue
<template>
  <div class="mod-module mod-parallel">
    <div class="img-list type-full">
      <div class="img-box">
        <p class="img-item">
          <a class="page-link" href="#/btn">按钮</a>
        </p>
      </div>
      <div class="img-box">
        <p class="img-item">
          <a class="page-link" href="#/list">列表</a>
        </p>
      </div>
      <div class="img-box">
        <p class="img-item">
          <a class="page-link" href="#/nav">导航</a>
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
 @import './css/index.css';
</style>
```

首页的代码也是非常简单，和我们平时写html差不多，就是几个跳转链接跳到对应的子页面，程序运行的时候，会将`<template>`标签里面的内容都注入到App.vue页面中的`router-view`标签中，从而实现无刷新的路由跳转。

从下面的内容开始，我们的知识将会深入一些。我们先不急着看其他几个子页面，因为子页面里面只是引用对应的组件，所以我们先从组件开始入手。

按钮组件quiButton.vue

```vue
<template>
  <button class="qui-btn">
    <span>{{msg}}</span>
  </button>
</template>

<script>
  export default {
        data:function(){
            return {
                msg:'下载'
            }
        }
  }
</script>
<style scoped>
  @import './css/reset.import.css';
  @import './css/qui-btn.import.css';
</style>
```

按钮组件很简单，就是一个正常的button标签，script标签中暴露这个组件的data属性（data是Vue的属性值，不是乱写的）。当按钮组件被初始化的时候，msg自定义属性会被绑定到`<span>`标签中的`{{msg}}`中，两个花括号用来绑定属性，这种写法学过模版化前端代码的人应该都比较熟悉。这里需要注意一个地方，如果不是组件的话，正常data的写法可以直接写一个对象，比如`data：{ msg ： ' 下载 ' }`，但由于组件是会在多个地方引用的，JS中直接共享对象会造成引用传递，也就是说修改了msg后所有按钮的msg都会跟着修改，所以这里用function来每次返回一个对象实例。

这就是一个非常简单的按钮组件，结构、样式+文案。

这时候问题来了，按钮中的文案我希望可以异化，不能每次都初始化一个叫做“下载”文案的按钮吧，希望可以以属性的方式来使用，比如这样子写就可以改变我们的按钮文案：

```vue
<qui-btn msg="确定" class="small"></qui-btn>
```

没问题，属性的接口暴露只需要写在prosp里面就可以了，如下所示修改下script标签的内容：

```vue
<script>
  export default {
    props: {
      msg: {
        default: '下载'
      }
    }
  }
</script>
```

把属性写在props里面，就可以暴露给其他页面调用了，在组件中，props是专门用来暴露组件的属性接口的，这里给了一个默认值‘下载’，后面我们要使用的话，只需要`<btn msg="确认"></btn>` 就可以修改按钮的默认文案了。

我们在上一篇文章的开头就讲了Vue是数据驱动模式的，当我在btn结构写上msg="确认"的时候，对应script里面的msg属性就会自动修改了。

<br/>

# 按钮事件

<br/>

按钮总少不了点击事件吧，那在Vue中怎么绑定事件呢，用methods属性，看下代码：

```vue
<template>
  <button class="qui-btn" v-on:click="btnClickEvent">
    <span>{{msg}}</span>
  </button>
</template>

<script>
  export default {
    props: {
      msg: {
        default: '下载'
      }
    },
    methods: {    //绑定事件的关键代码
      btnClickEvent: function(){
        alert(this.msg);
      }
    }
  }
</script>
```

methods属性中可以写任何的自定义函数，写完之后绑定的方式也很简单，在button上写关键字`v-on:click`，把对应的事件写上就可以了，以上代码实现的就是点击按钮弹出按钮中的文案，`v-XXX`是Vue里的一些关键字，叫做指令，我们后面会慢慢学到更多的指令；`v-on:click`可以缩写为`@click`，当然还有其他的事件比如`v-on:tab`等等；

<br/>

# 使用按钮组件pageQuiButton.vue

<br/>

现在我们大致做了一个按钮组件了，那么怎么调用它呢，去到我们的pageQuiButton子页面。

```vue
//pageQuiButton.vue
<template>
  <div id="pageQuiButton">
    <!--使用-->
    <qui-btn msg="确定" class="small"></qui-btn>
  </div>
</template>
<script>
  import quiBtn from '../components/quiButton.vue' /*引用*/
  export default {
    name: 'pageQuiButton',
    components: {
      'qui-btn': quiBtn /*注册自定义标签*/
    }
  }
</script>
```

从script开始解析，首先引入我们的组件赋值给变量quiBtn，使用时候直接将quiBtn作为对象的一部分写进components属性，这是Vue用来存储引用组件的关键字，同时对应我们自定义的标签 `"qui-btn"`，完成这些操作之后，我们就可以在template中使用自定义的按钮组件`<qui-btn>`上面也说了用msg属性来自定义按钮的文案。完成之后，我们就可以在页面中看到具体效果，点击按钮弹出对应的文案。

![](/images/vue/2_2.png)

上述我们将按钮事件写成默认的`alert(this.msg)`，如果有些按钮想要异化怎么办。之前说了msg属性可以支持自定义，那么按钮的点击事件如何支持自定义呢？

```vue
//pageQuiButton.vue
//监听子组件的事件
<qui-btn v-on:btnClickEvent="doSth" msg="我可以点击" ></qui-btn>
```

上面的代码在引用组件的时候，注册了一个事件，这个btnClickEvent事件是之前我们在按钮组件中绑定到按钮的click事件中的，然后我们给这个事件一个自定义的方法doSth，同时，在script中声明这个自定义的方法如下：

```vue
//pageQuiButton.vue
//页面中引用子组件并监听子组件的事件
<script>
  import quiBtn from '../components/quiButton.vue'
  export default {
    name: 'pageQuiButton',
    components: {
      'qui-btn': quiBtn
    },
    methods: {
      doSth: function(){
        alert('你点击了组件的click:btnClickEvent');
      }
    }
  }
</script>
```

专业一点的说，这种做法叫做监听，由引用方（暂且叫做父组件）监听子组件的内置方法；同时在子组件中，需要触发这个事件，以下是在子组件中的关键代码：

```vue
//quiButton.vue
//子组件中的代码
<script>
  export default {
    props: {
      msg: {
        default: '下载'
      }
    },
    methods: {
      btnClickEvent: function(){
        alert("先弹出默认的文案");
        this.$emit('btnClickEvent');//关键代码父组件触发自定义事件
      }
    }
  }
</script>
```

这里的关键代码就是`$emit`，也叫触发机制，父组件监听，子组件触发。如果觉得绕，以下描述可能会比较好理解：

>小B（子组件）有一个电话号码（子组件注册的事件），有一天小B把电话号码告诉了小A（父组件），让小A打电话给他，于是小A就拨打了小B的电话号码（监听），这时候整个沟通流程没有结束，必须要小B接听了电话（触发），两人之间才算完成了打电话这件事情。

完成这步之后，引用方（父组件）就可以给不同子组件调用不同的事件处理了：

```vue
<qui-btn v-on:btnClickEvent="doSth1" msg="确定" ></qui-btn>
<qui-btn v-on:btnClickEvent="doSth2" msg="取消" ></qui-btn>
<script>
/*这里只是简单展示*/
    methods: {
      doSth1: function(){
        alert('111');
      },
      doSth2: function(){
        alert('222');
      }
    }
</script>
```
<br/>

# 给按钮加图标

<br/>

有时候单纯的文案异化还不够，比如一些按钮是图标+文字类型的，而且图标还可能不一样，那应该怎么办呢？

![](/images/vue/2_3.png)

如果按钮组件的结构除了开发时候预设的那些dom结构之外，允许我们在调用的时候添加一些自己想要的结构，那是不是解决了呢，是的，Vue早就为我们考虑了这一点，他就是slot标签。

下面给我们的按钮组件加上一段结构

```vue
//quiButton.vue
<template>
  <button class="qui-btn" v-on:click="btnClickEvent">
    <slot name="icon"></slot><!--重点在这里-->
    <span>{{msg}}</span>
  </button>
</template>
```

加入了关键字slot并赋予一个name值之后，我们再看看引用如何使用

```vue
//pageQuiButton.vue
<qui-btn msg="下载" class="with-icon">
  <img slot="icon" class="ico" src="xxx.png" />
</qui-btn>
```

img上有个关键字`slot="icon"`对应组件中的`name="icon"`，渲染的时候，会将img整个替换掉组件中的对应name的`<slot>`标签，其实很好理解，slot的翻译是插槽的意思，相当于把img这块内容插到一个名叫icon的插槽里面去。

<br/>

# 中场休息一下

<br/>

学到这里，我们已经学会了用props给按钮自定义文案，用on和emit给按钮自定义点击触发，用slot给按钮添加一些自定义结构。当你回头去翻文档的时候，你会发现props，事件，slot这三样刚好就是学习组件通讯中最最最关键的三个环节。将这三个环节以实际案例解析出来后，好像也没有那么难了吧！

上述我们已经讨论了如何制作一个按钮组件，以及如何使用我们的按钮组件。

![](/images/vue/2_4.png)

接下来我们通过制作一个导航组件，来了解Vue中对于for循环的巧妙使用。

<br/>

# 导航组件quiNav.vue

<br/>

![](/images/vue/2_5.png)

我们将完成这样一个导航组件，点击导航中的tab，可以给当前tab加上一个active类，同时切换底部的黄色滑条，并且输出当前tab的文案，同时支持自定义事件。

由于在现实项目中，我们导航的tab个数是不定的，所以制作组件的时候，我们希望可以暴露一个属性来支持导航的tab个数，而tab的长相和应用其实是一样的，那么这时候我们可以用一个for循环来输出每一个tab。先看看关键代码：

```vue
//quiNav.vue
<template>
  <div class="qui-nav nav-type-1">
    <a v-for="(item, index) in items" ><!--关键代码v-for-->
      <span class="nav-txt">{{item.text}}</span>
    </a>
  </div>
</template>

<script>
  export default {
    data:function(){
      return {
        items:[
          {
            text: '首页',
            active : true
          },
          {
            text: '列表',
            active : false
          },
          {
            text: '关于',
            active : false
          },
          {
            text: '招聘',
            active : false
          }
        ]
      }
    }
    }
  }
</script>
```

该段代码的关键地方在于a标签上`v-for`关键字（还记得我们在前面说过的v-on绑定事件吗，v-XXX关键字是Vue预留的）可以把它理解为js中的for in 循环，items是我们在data里面定义的对象（还记得为什么data要写在function中返回吗？）。`v-for="(item,index) in items"`暴露了item和index两个接口，这是Vue提供的，代表items中的每一项以及该项对应的下标，接着我们就可以在标签中使用绑定`｛｛item.text｝｝`了。

这段代码理解了之后，我们再延伸一个动态添加class的概念。我们希望每个tab都有默认的class类名(比如`nav-item`类)，在点击每个tab的时候，当前tab添加active类，其他的tab删除这个active类。在Vue怎么实现呢？

<br/>

# 动态类名

<br/>

```vue
//quiNav.vue
<template>
  <div class="qui-nav nav-type-1">
    <a v-for="(item, index) in items" :class="[commonClass,item.active ? activeClass : '']" >
      <span class="nav-txt">{{item.text}}</span>
    </a>
  </div>
</template>

<script>
  export default {
    data:function(){
      return {
        commonClass:'nav-item',
        activeClass:'active',
        items:[
            ...//数据
        ]
      }
    }
  }
</script>
```

在template中添加了一句关键代码

	:class="[commonClass,item.active ? activeClass : '']"

`:class`给组件绑定一个class属性（类似jQuery中的attr方法），这里的写法是缩写，他的全拼应该是v-bind:（又一个v-XXX写法）。注意到最前面有个冒号，`：class=XXX`和`class=XXX`的区别在于不带冒号的是静态的字符串绑定，带冒号的是动态的变量绑定。我们给class绑定了一个数组，这个数组带有变量，先看commonClass，这个变量在data中定义了，然后数组的第二个元素是一个JS的三元运算符：`item.active?activeClass:''`，当每个item中的active值为true时，绑定activeClass变量对应的类，如果为false，则为空。最后的结果是当item.active为true时候，tab的class值为`'nav-item active'`，当为false，就只有`'nav-item'`。

上面的代码可以理解的话，那么我们切换tab的active类，就转换为修改每个item里面的active的值（再次体现数据驱动）。

那么问题来了，怎么去修改每个item里面的active值呢？没错，给每个tab绑定一个点击事件，当点击事件触发的时候，修改当前tab对应item的active值。于是代码变成了如下：

```vue
<template>
  <div class="qui-nav nav-type-1">
    <a v-for="(item, index) in items" :class="[commonClass,item.active ? activeClass : '']" v-on:click="navClickEvent(items,index)" >
      <span class="nav-txt">{{item.text}}</span>
    </a>
  </div>
</template>

<script>
  export default {
    data:function(){
      return {
        commonClass:'nav-item',
        activeClass:'active',
        items:[
          {
            text: '首页',
            active : true
          },
          ......
        ]
      }
    },
    methods:{
      navClickEvent:function(items,index){
        /*默认切换类的动作*/
        items.forEach(function(el){
          el.active = false;
        });
        items[index].active = true;
        /*开放用户自定义的接口*/
        this.$emit('navClickEvent',items,index);
      }
    }
  }
</script>
```

我们利用for循环给每个a标签绑定了一个click事件，对应methods中定义的navClickEvent，接收两个参数items和index（你也可以传人item和index，看个人代码喜好），然后当点击的时候，把items中的每个item.active置为false，把当前的tab的active值置为true，这样就可以动态切换active类了。最后再触发一次自定义事件（参考按钮制作自定义事件）。

以上就是我们导航组件的内容了，回想下我们做了啥？for循环输出每个tab，为每个tab绑定动态的class类名，同时在点击事件中动态切换类（底部的小黄条其实是利用active类做的CSS）

<br/>

# 小结

<br/>

回顾下我们这一篇章都学了什么内容。

- 页面路由的配置
- 按钮组件自定义属性props
- 按钮组件自定义事件 $on $emit
- 按钮组件自定义子块slot
- for循环实现导航组件
- 动态类名

上述内容已经基本上涵盖了组件的重要知识点，主要是父组件（页面）和子组件之间的调用和通讯（数据交互绑定），好好消耗一下我们会发现，其实Vue的总体逻辑思想和jQuery是一样的，毕竟最后都回归到javascript，只是由于代码设计角度的不同，我们可能看到和以前调用jQuery时候的写法不一致，但其实都有对方的影子在里面，相信理解了Vue的代码思想之后，以后我们学习React等其他类似的框架的时候，也会比较得心应手了。

<a href="/zip/vue/1_0.zip">下载源码</a>

还是建议下载源码来看。