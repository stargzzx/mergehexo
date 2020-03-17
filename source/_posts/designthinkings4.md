---
title: 设计模式 | 单例模式
date: 2020-03-14 11:36:42
categories:
- 设计思想
tags:
- 设计思想
- 单例模式
---
我们经常碰到这样一个问题，有一个资源，我们想让其就一个对象接触。

比如，一个文件，我们只允许对该文件创建一个对象，而不允许创建多个该文件的资源。也就是，对于该文件，我们只能 new 有且只有一个对象，即便是在不同的地方调用，我们使用的也是最开始 new 的对象。

那么这个时候就可以用单例模式。

在讲述这个例子的时候，我会使用 java 和 python 分别实现。

<!-- more -->

<br/>

# 参考资料

<br/>

[单例模式](https://www.imooc.com/learn/112)

<br/>

# 单例的分类

<br/>

单例一共有两种模式

- 懒汉模式
- 饿汉模式

<br/>

# 饿汉模式

<br/>


## java 实现

以下为要创建的文件

- Singleton.java
- test.java

###  Singleton.java

```java
public class Singleton {

    // 1 将构造方法私有化，不允许外部创建
    private Singleton(){
    }
    // 2 创建唯一的类实例，并且私有化，不允许外部直接访问
    // 静态表示，该实例不用 new 而是作为一个类属性
    private static Singleton singleton = new Singleton();

    // 3. 创建一个静态类方法，获得 singleton 实例
    public static Singleton getInstance(){
        return singleton;
    }
}
```

为什么要叫这个模式为 饿汉 呢？

是因为，下面的这个
	
	private static Singleton singleton = new Singleton();

在类加载的时候就已经创建好了（static），所以，实例一直存在，等着被调用。

所以，就好像是饿着的人一直等着吃饭一样。

### test.java

```java
public class test {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();

        if(singleton1==singleton2){
            System.out.println("是同一个实例");
        }
    }
}
```

## python实现

文件

- singleton.py

```python
class Singleton(object):

    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance

singleton1 = Singleton()
singleton2 = Singleton()

if __name__ == '__main__':
    print(singleton1 == singleton2)
```

<br/>

# 懒汉模式

<br/>

懒汉模式只有在调用的时候才会产生实例。

## java 实现

文件

- Singleton.java
- test.java

### Singleton.java

```java
public class Singleton {

    // 1 将构造方法私有化，不允许外部创建
    private Singleton() {
    }

    // 2 创建唯一的类实例，并且私有化，不允许外部直接访问
    // 静态表示，该实例不用 new 而是作为一个类属性
    private static Singleton singleton;

    // 3. 创建一个静态类方法，获得 singleton 实例
    public static Singleton getInstance() {
        if (singleton == null) {
            singleton = new Singleton();
        }
        return singleton;
    }
}
```

### test.java

```java
public class test {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();

        if(singleton1==singleton2){
            System.out.println("是同一个实例");
        }
    }
}

```

## python 实现

文件

- singleton.py

```python
class Singleton(object):
    __instance = None

    # 初始化时，如果存在对象，就直接返回这个对象，不存在就不管，也不new它
    def __init__(self):
        if Singleton.__instance:
            self.get_instance()

    # 实际的对象创建发生在调用get_instance的时候
    @classmethod
    def get_instance(cls):
        if not cls.__instance:
            cls.__instance = Singleton()
        return cls.__instance

if __name__ == '__main__':
    singleton1 = Singleton.get_instance()
    singleton2 = Singleton.get_instance()
    print(singleton1 == singleton2)
```

<br/>

# 懒汉和饿汉的区别

<br/>

- 饿汉模式加载慢，运行时快，懒汉反之
- 饿汉模式是线程安全的，懒汉模式是线程不安全的

## 线程安全一说

懒汉模式，假设有多个线程同时进入这个类，因为都没创建，导致，可能创建多个对象，所以线程不安全。

Python内置有一个全局锁会保证只进入一个线程调用类的方法，所以没有这个问题，但如果其他语言实现就要考虑这个问题，比如java就应该用synchronized锁住代码块确保线程安全。这一问题也是造成Python的多线程不是真正的多线程的原因。

<br/>

# 单例模式再次更新(非常重要) 只实验了 python

<br/>

## 懒汉模式

请看如下的代码

```python
class Singleton(object):
    __instance = None

    # 初始化时，如果存在对象，就直接返回这个对象，不存在就不管，也不new它
    def __init__(self, name):
        self.name = name
        if Singleton.__instance:
            self.get_instance()

    # 实际的对象创建发生在调用get_instance的时候
    @classmethod
    def get_instance(cls, *args):
        if not cls.__instance:
            cls.__instance = Singleton(*args)
        return cls.__instance

    def get_name(self):
        print(self.name)


if __name__ == '__main__':
    singleton1 = Singleton.get_instance(1)
    singleton1.get_name()
    singleton2 = Singleton.get_instance(2)
    singleton2.get_name()
    singleton2.name = 2
    singleton1.get_name()
    print(singleton1 == singleton2)
```

其打印出的东西如下：

    1
    1
    2
    True

## 饿汉模式

```python
class Singleton(object):

    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance

    def __init__(self, name):
        self.name = name

    def get_name(self):
        print(self.name)


if __name__ == '__main__':
    singleton1 = Singleton(1)
    singleton1.get_name()
    singleton2 = Singleton(2)
    singleton2.get_name()
    singleton2.name = 3
    singleton1.get_name()
    print(singleton1 == singleton2)
```

    1
    2
    3
    True

## 不使用单例模式

```python
class Singleton(object):

    def __init__(self, name):
        self.name = name

    def get_name(self):
        print(self.name)


if __name__ == '__main__':
    singleton1 = Singleton(1)
    singleton1.get_name()
    singleton1.name = 3
    singleton1.get_name()
```

    1
    3
