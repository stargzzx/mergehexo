---
title: 组合模式
date: 2019-10-06 16:42:25
categories:
- 架构
- 单一
tags:
- 架构
- 组合模式
---
从MVC中得知了组合模式的存在，然后找了一个比较好的文章进行解读。

<!--more-->

<br/>

# 原文链接

<br/>

[组合模式](https://www.jianshu.com/p/c7f19e5310f9)

<br/>

# 原文内容

<br/>

## 简介

>Compose objects into tree structures to represent part-whole hierarchies.Composite lets clients treat individual objects and compositions of objects uniformly.
将对象组合成树形结构以表示 “部分-整体” 的层次结构，使得用户对单个对象和组合对象的使用具有一致性。

组合模式（Composite Pattern） 也称为 整体-部分（Part-Whole）模式，它的宗旨是通过将单个对象（叶子节点）和组合对象（树枝节点）用相同的接口进行表示，使得客户对单个对象和组合对象的使用具有一致性。

组合模式 一般用来描述 整体 与 部分 的关系，它将对象组织到树形结构中，最顶层的节点称为 根节点，根节点下面可以包含 树枝节点 和 叶子节点，树枝节点下面又可以包含 树枝节点 和 叶子节点。如下图所示：

![](/images/designthinkings/2_0.png)

由上图可以看出，其实 根节点 和 树枝节点 本质上是同一种数据类型（蓝色圆圈），可以作为容器使用；而 叶子节点 与 树枝节点 在语义上不属于同一种类型，但是在 组合模式 中，会把 树枝节点 和 叶子节点 认为是同一种数据类型（用同一接口定义），让它们具备一致行为。这样，在 组合模式 中，整个树形结构中的对象都是同一种类型，带来的一个好处就是客户无需辨别 树枝节点 还是 叶子节点，而是可以直接进行操作，给客户使用带来极大的便利。

组合模式 核心：借助同一接口，使叶子节点和树枝节点的操作具备一致性。

## 主要解决

当子系统与其内各个对象层次呈现树形结构时，可以使用 组合模式 让该子系统内各个对象层次的行为操作具备一致性。客户端使用该子系统内任意一个层次对象时，无须进行区分，直接使用通用操作即可，为客户端的使用带来了便捷。

注：如果树形结构系统不使用 组合模式 进行架构，那么按照正常的思维逻辑，对该系统进行职责分析，按上文树形结构图所示，该系统具备两种对象层次类型：树枝节点和叶子节点。那么我们就需要构造两种对应的类型，然后由于树枝节点具备容器功能，因此树枝节点类内部需维护多个集合存储其他对象层次（eg:List<Composite>,List<Leaf>），如果当前系统对象层次更复杂时，那么树枝节点内就又要增加对应的层次集合，这对树枝节点的构建带来了巨大的复杂性，臃肿性以及不可扩展性。同时客户端访问该系统层次时，还需进行层次区分，这样才能使用对应的行为，给客户端的使用也带来了巨大的复杂性。而如果使用 组合模式 构建该系统，由于 组合模式 抽取了系统各个层次的共性行为，具体层次只需按需实现所需行为即可，这样子系统各个层次就都属于同一种类型，所以树枝节点只需维护一个集合（List<Component>）即可存储系统所有层次内容，并且客户端也无需区分该系统各个层次对象，对内系统架构简洁优雅，对外接口精简易用。

## 优缺点

### 优点

组合模式 屏蔽了对象系统的层次差异性（树节点和叶子节点为不同类型），将客户代码与复杂的容器对象解耦，使得客户端可以忽略层次间的差异，使用一致的行为控制不同层次。

在 组合模式 可以很方便地增加 树枝节点 和 叶子节点 对象，并对现有类库无侵入，符合 开闭原则；

### 缺点

如果类系统（树形结构）过于庞大，虽然对不同层次都提供一致性操作，但客户端仍需花费时间理清类之间的层次关系；

组合模式 在具体实现上违背了设计模式 接口隔离原则 或 依赖倒置原则；

## 使用场景

系统对象层次具备整体和部分，呈树形结构，且要求具备统一行为（如树形菜单，操作系统目录结构，公司组织架构等）；

## 模式讲解

组合模式 主要包含三种角色：

抽象根节点（Component）：

	定义系统各层次对象的共有方法和属性，可以预先定义一些默认行为和属性；
	
树枝节点（Composite）：

	定义树枝节点的行为，存储子节点，组合树枝节点和叶子节点形成一个树形结构；

叶子节点（Leaf）：

	叶子节点对象，其下再无分支，是系统层次遍历的最小单位；

组合模式 在代码具体实现上，有两种不同的方式：

### 透明模式

透明模式：把组合（树节点）使用的方法放到统一行为（Component）中，让不同层次（树节点，叶子节点）的结构都具备一致行为；其 UML 类图如下所示：

![](/images/designthinkings/2_1.png)

透明组合模式 把所有公共方法都定义在 Component 中，这样做的好处是客户端无需分辨是叶子节点（Leaf）和树枝节点（Composite），它们具备完全一致的接口；缺点是叶子节点（Leaf）会继承得到一些它所不需要（管理子类操作的方法）的方法，这与设计模式 接口隔离原则 相违背。

透明组合模式 通用代码如下所示：

```python
package com.yn.design_pattern.composite.universal.transparent;

import java.util.ArrayList;
import java.util.List;

class Client {
    public static void main(String[] args) {
        // 来一个根节点
        Component root = new Composite("root");
        // 来一个树枝节点
        Component branchA = new Composite("---branchA");
        Component branchB = new Composite("------branchB");
        // 来一个叶子节点
        Component leafA = new Leaf("------leafA");
        Component leafB = new Leaf("---------leafB");
        Component leafC = new Leaf("---leafC");

        root.addChild(branchA);
        root.addChild(leafC);
        branchA.addChild(leafA);
        branchA.addChild(branchB);
        branchB.addChild(leafB);

        String result = root.operation();
        System.out.println(result);

    }

    // 抽象根节点
    static abstract class Component {
        protected String name;

        public Component(String name) {
            this.name = name;
        }

        public abstract String operation();

        public boolean addChild(Component component) {
            throw new UnsupportedOperationException("addChild not supported!");
        }

        public boolean removeChild(Component component) {
            throw new UnsupportedOperationException("removeChild not supported!");
        }

        public Component getChild(int index) {
            throw new UnsupportedOperationException("getChild not supported!");
        }
    }

    // 树节点
    static class Composite extends Component {
        private List<Component> mComponents;

        public Composite(String name) {
            super(name);
            this.mComponents = new ArrayList<>();
        }

        @Override
        public String operation() {
            StringBuilder builder = new StringBuilder(this.name);
            for (Component component : this.mComponents) {
                builder.append("\n");
                builder.append(component.operation());
            }
            return builder.toString();
        }

        @Override
        public boolean addChild(Component component) {
            return this.mComponents.add(component);
        }

        @Override
        public boolean removeChild(Component component) {
            return this.mComponents.remove(component);
        }

        @Override
        public Component getChild(int index) {
            return this.mComponents.get(index);
        }
    }
	
	//叶子节点
    static class Leaf extends Component {

        public Leaf(String name) {
            super(name);
        }

        @Override
        public String operation() {
            return this.name;
        }
    }
}
```

透明组合模式 中，由于 Component 包含叶子节点所不需要的方法，因此，我们直接将这些方法默认抛出UnsupportedOperationException异常。

### 安全模式

安全模式：统一行为（Component）只规定系统各个层次的最基础的一致行为，而把组合（树节点）本身的方法（管理子类对象的添加，删除等）放到自身当中；其 UML 类图如下所示：

![](/images/designthinkings/2_2.png)

安全组合模式 把系统各层次公有的行为定义在 Component 中，把组合（树节点）特有的行为（管理子类增加，删除等）放到自身（Composite）中。这样做的好处是接口定义职责清晰，符合设计模式 单一职责原则 和 接口隔离原则；缺点是客户需要区分树枝节点（Composite）和叶子节点（Leaf），这样才能正确处理各个层次的操作，客户端无法依赖抽象（Component），违背了设计模式 依赖倒置原则。

安全组合模式 的通用代码相对 透明组合模式 而言，需要进行如下修改：

	修改 Component 代码：只保留各层次公有行为：
	
```python
    // 抽象根节点
    static abstract class Component {
        protected String name;

        public Component(String name) {
            this.name = name;
        }

        public abstract String operation();
    }
```

	修改客户端代码：将树枝节点类型更改为 Composite 类型，以便获取管理子类操作的方法：
	
```python
class Client {
    public static void main(String[] args) {
        // 来一个根节点
        Composite root = new Composite("root");
        // 来一个树枝节点
        Composite branchA = new Composite("---branchA");
        Composite branchB = new Composite("------branchB");
        // 来一个叶子节点
        Component leafA = new Leaf("------leafA");
        Component leafB = new Leaf("---------leafB");
        Component leafC = new Leaf("---leafC");

        root.addChild(branchA);
        root.addChild(leafC);
        branchA.addChild(leafA);
        branchA.addChild(branchB);
        branchB.addChild(leafB);

        String result = root.operation();
        System.out.println(result);
    }
}
```

上述例子的运行结果如下：

	root
	---branchA
	------leafA
	------branchB
	---------leafB
	---leafC
	
运行结果显示：root 包含 branchA 和 leafC；branchA 包含 leafA 和 branchB；branchB 包含 leafB。

这样我们就使用 组合模式 模拟了一个树形结构系统。

问：透明组合模式 和 安全组合模式 都有各自的优点和缺点，那么我们应该优先选择哪一种呢？

答：既然 组合模式 会被分为两种实现，那么肯定是不同的场合某一种会更加适合，也即具体情况具体分析。透明组合模式 将公共接口封装到抽象根节点（Component）中，那么系统所有节点就具备一致行为，所以如果当系统绝大多数层次具备相同的公共行为时，采用 透明组合模式 也许会更好（代价：为剩下少数层次节点引入不需要的方法）；而如果当系统各个层次差异性行为较多或者树节点层次相对稳定（健壮）时，采用 安全组合模式

注：设计模式的出现并不是说我们要写的代码一定要遵循设计模式所要求的方方面面，这是不现实同时也是不可能的。设计模式的出现，其实只是强调好的代码所具备的一些特征（六大设计原则），这些特征对于项目开发是具备积极效应的，但不是说我们每实现一个类就一定要全部满足设计模式的要求，如果真的存在完全满足设计模式的要求，反而可能存在过度设计的嫌疑。同时，23种设计模式，其实都是严格依循设计模式六大原则进行设计，只是不同的模式在不同的场景中会更加适用。设计模式的理解应该重于意而不是形，真正编码时，经常使用的是某种设计模式的变形体，真正切合项目的模式才是正确的模式。

## 举个例子

例子：对于程序员来说，电脑是我们每天都要接触的。它的目录系统其实就是一个典型的树形结构，目录包含文件夹和文件，文件夹里面又可以包含文件夹和文件···下面我们就用代码来实现一个目录系统。

分析：目录系统有两个大的层次：文件夹，文件。其中，文件夹能容乃其他层次，为树枝节点；文件为最小单位，为叶子节点。由于目录系统层次较少，且树枝节点（文件夹）结构相对稳定，而文件其实可以有很多类型，所以这里我们选择使用 安全组合模式 来实现目录系统，可以避免为叶子类型（文件）引入冗余方法。

代码如下：

```python
class Client {
    public static void main(String[] args) {
        Folder diskC = new Folder("C:\\");
        Folder windows = new Folder("---Windows");
        Folder system32 = new Folder("------system32");
        File calcFile = new File("---------calc.exe");
        File pingFile = new File("---------ping.exe");

        diskC.addDir(windows);
        windows.addDir(system32);
        system32.addDir(calcFile);
        system32.addDir(pingFile);

        diskC.print();

    }

    //抽象根类：Component
    static abstract class Directory {
        protected String name;

        public Directory(String name) {
            this.name = name;
        }

        public abstract void print();
    }

    //叶子节点：Leaf
    static class File extends Directory {

        public File(String name) {
            super(name);
        }

        @Override
        public void print() {
            System.out.println(this.name + ": file");
        }
    }

    //树枝节点：Composite
    static class Folder extends Directory {
        private List<Directory> mDirs;

        public Folder(String name) {
            super(name);
            this.mDirs = new ArrayList<>();
        }

        @Override
        public void print() {
            System.out.println(this.name + ": Folder");
            for (Directory dir : this.mDirs) {
                dir.print();
            }
        }

        public boolean addDir(Directory dir) {
            return this.mDirs.add(dir);
        }

        public boolean removeDir(Directory dir) {
            return this.mDirs.remove(dir);
        }

        public Directory getDir(int index) {
            return this.mDirs.get(index);
        }
    }
}
```

客户端创建了一个磁盘 C:\，并再 C 盘下创建了 windows 目录，windows 目录下又创建了 system32 目录，system32 目录下包含两个文件 calc.exe 和 ping.exe。上面代码执行的结果如下：

	C:\: Folder
	---Windows: Folder
	------system32: Folder
	---------calc.exe: File
	---------ping.exe: File
