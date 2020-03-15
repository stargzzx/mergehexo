---
title: coinex | 三类联动
date: 2019-12-07 20:12:26
categories:
- ant
tags:
- ant
- coinex
---
这个是做公司的项目的时候，找到的一个设计机制，我觉得挺牛逼的，虽然，应该但是，我还是记录一下。

<!-- more -->

{% codeblock %}
class A:

    def __init__(self):
        self.b = B(self)
        self.c = C(self)

    def get_c_index(self):
        return self.c.index


class B:

    def __init__(self, A: A):
        self.a = A

    def print_c_index(self):
        print(self.a.get_c_index())


class C:

    def __init__(self, A: A):
        self.a = A
        self.index = 3


if __name__ == '__main__':
    a = A()
    b = B(a)
    b.print_c_index()
{% endcodeblock %}

最终输出

	3

也就是说类 A 是连接B,C的初始化器，有这一点的特性我们最后可以做很多的事情。

