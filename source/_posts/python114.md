---
title: python | 什么是 pypy
date: 2020-05-26 16:52:19
categories:
- python
- 进阶
- pypy
tags:
- pypy
---
公司由于对代码运行效率有要求，所以，将部分项目的编译器转到 pypy 中，所以，我也将开启一份关于 pypy 的专栏，来分享我所收集到的知识。

<!-- more -->

python是一种编程语言。但这种语言有多种实现，而且与其他语言不同，python并没有一个专门的机构负责实现，而是由多个社区来实现。

其中使用c语言开发的叫做python，在于别的语言开发的python对比时为避免歧义通常称为CPython。

同样的，使用java开发的叫做JPython，使用.net开发的叫做IronPython。

而PyPy与CPython的不同在于，别的一些python实现如CPython是使用解释执行的方式，这样的实现方式在性能上是很凄惨的。而PyPy使用了JIT(即时编译)技术，在性能上得到了提升。

## cpython 和 pypy

`CPython`：是用C语言实现Pyhon，是目前应用最广泛的解释器。最新的语言特性都是在这个上面先实现，基本包含了所有第三方库支持，但是CPython有几个缺陷，一是(GIL)全局锁使Python在多线程效能上表现不佳，二是CPython无法支持JIT（即时编译），导致其执行速度不及Java和Javascipt等语言。于是出现了Pypy。

`Pypy`：是用Python自身实现的解释器。针对CPython的缺点进行了各方面的改良，性能得到很大的提升。最重要的一点就是Pypy集成了JIT。但是，Pypy无法支持官方的C/Python API，导致无法使用一些重要的第三方库。这也是现在Pypy没有被广泛使用的原因吧。

## Python的解释执行：

由于Python是动态编译的语言，和C/C++、Java或者Kotlin等静态语言不同，它是在运行时一句一句代码地边编译边执行的，而Java是提前将高级语言编译成了JVM字节码，运行时直接通过JVM和机器打交道，所以进行密集计算时运行速度远高于动态编译语言。 

PyPy，它使用了JIT（即时编译）技术，混合了动态编译和静态编译的特性，仍然是一句一句编译源代码，但是会将翻译过的代码缓存起来以降低性能损耗。相对于静态编译代码，即时编译的代码可以处理延迟绑定并增强安全性。绝大部分 Python代码都可以在PyPy下运行，但是PyPy和CPython有一些是不同的。

## 解释执行与编译执行的区别

对于我们平时写的代码，一般计算机是没办法直接识别的，需要相应的编译器将其编译层机器代码（一些计算机可以直接识别的二进制代码），机器才能执行。

编译执行，顾名思义，要先编译再执行，这里需要有一个编译器，来将我们的代码全部编译成机器代码，然后进行执行。因为先整体进行编译，所以这里会生成编译后的机器代码。

解释执行，则是需要一个解释器，它会将我们的一句句解释成机器代码来执行，可以认为是，解释一句，执行一句。在这个过程中，不会生成中间文件。

通过对比发现，编译执行，只需要编译一次，多次运行。而解释执行的话，每运行一次程序，都要经过解释器的解释过程。

针对优缺点，可以从以下几个方面分析。

- 从启动效率来看，解释执行不需要进行编译操作，而编译执行，要经过编译过程。解释执行启动速度更快。

- 从运行效率来看，因为编译执行只需要编译一次，以后再运行就无需编译，而解释执行每次都要经过解释过程，所以编译执行效率更高。

- 从内存使用方面来看，编译执行需要生成编译后的机器码文件，而解释执行时逐句解释执行，所以解释执行对内存占用更少。

- 从跨平台的角度来看，因为解释执行每次可以根据不同的平台进行解释，例如js在linux和windows都可以运行，而C语言在windows下编译后的文件，只能在windows下才行执行。

- 对于大型项目来说，比较注重运行效率，核心代码一般都是采用编译执行的语言。而对于一些简单的操作，可以考虑使用解释执行的语言。

## pypy 的劣势

Python的应用范围非常广，至少要分为：

- 科学计算
- 脚本运维
- 业务系统

三个方向。其中科学计算主要依赖C扩展，而C扩展基本都是为CPython设计，PyPy不能很有效率地执行原生C扩展，对Python代码的性能提升也没有很关键的作用，因此这个领域主要还是CPython。

脚本运维的特点是对性能没有很大需求，而且一般执行一次马上退出，来不及JIT预热，因此基于JIT的PyPy也没有优势，更不要说CPython是Linux发行版的一部分，使用原生Python非常简单这件事了。

而对于纯Python编写的业务系统，比如Web网站等等，PyPy有很好的性能提升。这也是PyPy发展的主要的方向。

