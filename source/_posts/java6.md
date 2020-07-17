---
title: lombok | 简介和使用
date: 2020-07-17 11:02:49
categories:
- [java,第三方库,lombok]
tags:
- lombok
---
在使用的过程中，明明已经安装了 lombok 为什么 idea 中，代码显示找不到该方法。

原因是没有安装 lombok 的插件。

<!-- more -->

先说一下环境，我是在 MacBook 中。

<br/>

# 参考资料

<br/>

- [官方网站](https://projectlombok.org/)

<br/>

# IDEA 安装插件

<br/>

整个过程没有上面教程中那么繁琐。

	Preferences -> Plugins

可以看到右边有两个选项

- Marketplace
- Installed

我们查看 Installed 搜索一下，确实没有 `lombok`。

然后，我们在 `Marketplace` 搜索安装，然后重启 `Idea` 即可。

<br/>

# gradle 引入

<br/>

	repositories {
		mavenCentral()
	}
	dependencies {
		compileOnly 'org.projectlombok:lombok:1.18.12'
		annotationProcessor 'org.projectlombok:lombok:1.18.12'
		
		testCompileOnly 'org.projectlombok:lombok:1.18.12'
		testAnnotationProcessor 'org.projectlombok:lombok:1.18.12'
	}

<br/>

# 使用

<br/>

## 注解类型

- @Data
- @Setter
- @Getter
- @Log4j
- @AllArgsConstructor
- @NoArgsConstructor
- @EqualsAndHashCode
- @NonNull
- @Cleanup
- @ToString
- @RequiredArgsConstructor
- @Value
- @SneakyThrows
- @Synchronized

## 注解详解

### @Data

注解在 `类` 上；提供类所有属性的 get 和 set 方法，此外还提供了`equals`、`canEqual`、`hashCode`、`toString` 方法。

![](/images/lombok/0_0.jpg)

### @Setter

注解在 `属性` 上；为单个属性提供 `set` 方法; 注解在 类 上，为该类所有的属性提供 `set` 方法， 都提供默认构造方法。

![](/images/lombok/0_1.jpg)

<br/>

![](/images/lombok/0_2.jpg)

### @Getter

注解在 `属性` 上；为单个属性提供 `get` 方法; 注解在 类 上，为该类所有的属性提供 `get` 方法，都提供默认构造方法。

![](/images/lombok/0_3.jpg)

<br/>

![](/images/lombok/0_4.jpg)

### @Log4j

注解在 `类` 上；为类提供一个 属性名为 `log` 的 `log4j` 日志对象，提供默认构造方法。

![](/images/lombok/0_5.jpg)

### @AllArgsConstructor

注解在 `类` 上；为类提供一个全参的构造方法，加了这个注解后，类中不提供默认构造方法了。

![](/images/lombok/0_6.jpg)

### @NoArgsConstructor

注解在 `类` 上；为类提供一个无参的构造方法。

![](/images/lombok/0_7.jpg)

### @EqualsAndHashCode

注解在 `类` 上, 可以生成 `equals`、`canEqual`、`hashCode` 方法。

![](/images/lombok/0_8.jpg)

### @NonNull

注解在 `属性` 上，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常，也会有一个默认的无参构造方法。

![](/images/lombok/0_9.jpg)

### @Cleanup

这个注解用在 `变量` 前面，可以保证此变量代表的资源会被自动关闭，默认是调用资源的 `close()` 方法，如果该资源有其它关闭方法，可使用 `@Cleanup(“methodName”)` 来指定要调用的方法，也会生成默认的构造方法

![](/images/lombok/0_10.jpg)

### @ToString

这个注解用在 `类` 上，可以生成所有参数的 `toString` 方法，还会生成默认的构造方法。

![](/images/lombok/0_11.jpg)

### @RequiredArgsConstructor

这个注解用在 `类` 上，使用类中所有带有 `@NonNull` 注解的或者带有 `final` 修饰的成员变量生成对应的构造方法。

![](/images/lombok/0_12.jpg)

### @Value

这个注解用在 `类` 上，会生成含所有参数的构造方法，`get` 方法，此外还提供了`equals`、`hashCode`、`toString` 方法。

![](/images/lombok/0_13.jpg)

### @SneakyThrows

这个注解用在 `方法` 上，可以将方法中的代码用 `try-catch` 语句包裹起来，捕获异常并在 `catch` 中用 `Lombok.sneakyThrow(e)` 把异常抛出，可以使用 `@SneakyThrows(Exception.class)`的形式指定抛出哪种异常，也会生成默认的构造方法。

![](/images/lombok/0_14.jpg)

### @Synchronized

这个注解用在 `类方法 `或者 `实例方法` 上，效果和 `synchronized` 关键字相同，区别在于锁对象不同，对于类方法和实例方法，`synchronized` 关键字的锁对象分别是类的 `class` 对象和 `this` 对象，而 `@Synchronized` 的锁对象分别是 `私有静态 final 对象 lock` 和 `私有 final 对象 lock`，当然，也可以自己指定锁对象，此外也提供默认的构造方法。

![](/images/lombok/0_15.jpg)
