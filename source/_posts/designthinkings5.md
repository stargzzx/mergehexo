---
title: 设计模式 | 工厂模式
date: 2020-03-14 18:26:41
categories:
- 设计思想
tags:
- 设计思想
- 工厂模式
---
工厂模式也是 23 个基础模式之一。

这篇博客将详细的介绍一下工厂模式。

<!-- more -->

<br/>

# 参考资料

<br/>

[模式的秘密——工厂模式](https://www.imooc.com/learn/261)
[工厂模式](https://www.runoob.com/design-pattern/factory-pattern.html)

<br/>

# 基础知识

<br/>

- 实例化对象，用工厂方法代替 new
- 工厂模式包括
	- 工厂方法模式
	- 抽象工厂模式
		- 抽象工厂模式是工厂方法模式的扩展

<br/>

# 工厂模式

<br/>

## 实现

我们将创建一个 Shape 接口和实现 Shape 接口的实体类。下一步是定义工厂类 ShapeFactory。

FactoryPatternDemo，我们的演示类使用 ShapeFactory 来获取 Shape 对象。它将向 ShapeFactory 传递信息（CIRCLE / RECTANGLE / SQUARE），以便获取它所需对象的类型。

这图画的是真的丑。。。

```mermaid
stateDiagram
    FactoryPatternDemo --> ShapeFactory
    ShapeFactory --> shape
    state FactoryPatternDemo {
        main
    }
    state ShapeFactory {
        getshape
    }
    
    shape --> circle
    shape --> rectangle
    shape --> square

    state shape {
        draw
    }
    state circle {
        draw
    }
    state rectangle {
        draw
    }
    state square {
        draw
    }
```

### java 实现


#### 步骤一

- Shape.java

创建一个接口:

```java
public interface Shape {
   void draw();
}
```

#### 步骤二

- Rectangle.java

创建实现接口的实体类。

```java
public class Rectangle implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Rectangle::draw() method.");
   }
}
```
- Square.java

```java
public class Square implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Square::draw() method.");
   }
}
```

- Circle.java

```java
public class Circle implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Circle::draw() method.");
   }
}
```

#### 步骤三

创建一个工厂，生成基于给定信息的实体类的对象。

- ShapeFactory.java

```java
public class ShapeFactory {
    
   //使用 getShape 方法获取形状类型的对象
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
}
```

#### 步骤 4

使用该工厂，通过传递类型信息来获取实体类的对象。

- FactoryPatternDemo.java

```java
public class FactoryPatternDemo {
 
   public static void main(String[] args) {
      ShapeFactory shapeFactory = new ShapeFactory();
 
      //获取 Circle 的对象，并调用它的 draw 方法
      Shape shape1 = shapeFactory.getShape("CIRCLE");
 
      //调用 Circle 的 draw 方法
      shape1.draw();
 
      //获取 Rectangle 的对象，并调用它的 draw 方法
      Shape shape2 = shapeFactory.getShape("RECTANGLE");
 
      //调用 Rectangle 的 draw 方法
      shape2.draw();
 
      //获取 Square 的对象，并调用它的 draw 方法
      Shape shape3 = shapeFactory.getShape("SQUARE");
 
      //调用 Square 的 draw 方法
      shape3.draw();
```

#### 步骤五

执行程序，输出结果：

```python
Inside Circle::draw() method.
Inside Rectangle::draw() method.
Inside Square::draw() method.
```

### python 实现

python 没有 java 的接口模式，并且，由于其不是强类型语言，所以实现起来非常容易。

下面的这个例子，出现在

[Design_pattern_of_python](https://github.com/w392807287/Design_pattern_of_python)

python 的实现原理非常简单，就是需要什么对象，传入相关的判断条件就好了。

```python
class ChinaGetter:
    """A simple localizer a la gettext"""
    def __init__(self):
        self.trans = dict(dog="小狗", cat="小猫")

    def get(self, msgid):
        """We'll punt if we don't have a translation"""
        try:
            return self.trans[msgid]
        except KeyError:
            return str(msgid)


class EnglishGetter:
    """Simply echoes the msg ids"""
    def get(self, msgid):
        return str(msgid)


def get_localizer(language="English"):
    """The factory method"""
    languages = dict(English=EnglishGetter, China=ChinaGetter)
    return languages[language]()

# Create our localizers
e, g = get_localizer("English"), get_localizer("China")
# Localize some text
for msgid in "dog parrot cat bear".split():
    print(e.get(msgid), g.get(msgid))
```

