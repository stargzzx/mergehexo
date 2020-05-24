---
title: python | 内部类和内部方法
date: 2020-05-23 22:49:46
categories:
- python
- 进阶
tags:
- python
---
经常在项目中看到这方面的代码，一开始很疑惑，所以，在这里进行总结。

<!-- more -->

<br/>

# 参考资料

<br/>

- [Python内部类，内部类调用外部类属性，方法](https://www.cnblogs.com/liuqingzheng/p/9504210.html)
- [python 装饰器、内部函数、闭包简单理解](https://www.cnblogs.com/starsea/p/5427021.html)

<br/>

# 内部方法

<br/>

```python
def test(*args):
    def add(*args):         #  显示的调用外部函数的参数
        return args
    return add(*args)       #  返回内部函数的直接调用
 
运行结果如下:
test(1,2,3)
(1,2,3)   
```

内部函数直接引用外部函数参数，外部函数test显示的返回内部函数add的调用。

当需要在函数内部多次执行复杂任务时，内部函数非常有用，从而避免了循环和代码的堆叠重复。

<br/>

# 内部类

<br/>

## Python中内部类

```python
class MyOuter:
    age=18
    def __init__(self,name):
        self.name=name

    class MyInner:
        def __init__(self,inner_name):
            self.inner_name=inner_name

out=MyOuter('lqz')
inner=out.MyInner('lqz_inner')
print(inner.inner_name)
```

## 内部类调用外部类的类属性和类方法

注意是类属性和类方法，不是对象属性和对象的绑定方法

```python
class MyOuter:
    age=18
    def __init__(self,name):
        self.name=name
    @classmethod
    def outer_class_method(cls):
        print('我是外部类的类方法')

    class MyInner:
        def __init__(self,inner_name):
            self.inner_name=inner_name
        def inner_method(self):
            print('我是内部类的对象方法')
            MyOuter.outer_class_method()

out=MyOuter('lqz')
inner=out.MyInner('lqz_inner')
inner.inner_method()
```

## 内部类调用外部对象的对象属性和方法

需要在内部类构造的时候，把对象传过来

```python
class MyOuter:
    age=18
    def __init__(self,name):
        self.name=name
    @classmethod
    def outer_class_method(cls):
        print('我是外部类的类方法')
    def outer_obj_method(self):
        print('我是外部类对象的绑定方法')

    class MyInner:
        def __init__(self,inner_name,obj):
            self.inner_name=inner_name
            self.obj=obj
        def inner_method(self):
            print('我是内部类的对象方法')
            MyOuter.outer_class_method()
            self.obj.outer_obj_method()

out=MyOuter('lqz')
inner=out.MyInner('lqz_inner',out)
inner.inner_method()
```