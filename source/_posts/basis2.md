---
title: 递归
date: 2018-06-14 20:47:09
categories:
- 电脑相关
- 编程基础
tags:
- basis
- 递归
- python
- algorithm
---
递归这个东西，我真是饱受其害，废了很多功夫说实话我没有搞清，只能一点点的记录了。

<!-- more -->

首先应用引用《编程之美》中的一句话：

	To iterate is human,to recurse divine. 
	迭代者为人，递归者为神。 
	– L. Peter Deutsch
	
其次，我要吐槽国内那些技术博客或者其它讲解，我觉得有些误人子弟。因为，那些博文都有头没尾或者干脆就毫无可行的观点。很不幸的是，往往一个人写完相关的技术博客，很多人也就实行粘贴复制，导致所有的博文都好像雷同一样，一搜索全是一样的。

让我深感无奈。

## 递归的真相

好了，在这里我还要从大多数博文那样，从阶乘入手，看下述代码：

```python
def F(num):
    if num == 1:
        return 1
    return num * F(num - 1)

F(5)
```

### 错误的示例

很多人的博文对于上述的函数传递都会有下述的流程：

```python
F(5)                        # 第 1 次调用使用 5
5 * F(4)                    # 第 2 次调用使用 4
5 * (4 * F(3))              # 第 3 次调用使用 3
5 * (4 * (3 * F(2)))        # 第 4 次调用使用 2
5 * (4 * (3 * (2 * F(1))))  # 第 5 次调用使用 1 
5 * (4 * (3 * (2 * 1)))     # 从第 5 次调用返回
5 * (4 * (3 * 2))           # 从第 4 次调用返回
5 * (4 * 6)                 # 从第 3次调用返回
5 * 24                      # 从第 2 次调用返回
120                         # 从第 1 次调用返回
```

很多书籍也是这么解读的，其实我认为是错误的，又或者是具有二义性。起先，我认为这个流程是对的，直到我在书写完用递归画树状图，才对这个流程产生不解。因为，按照它的传递思路，是不可能写出树状图的。

百般思索后，终于想起数据结构中的一句话：

	任何递归都可以用迭代 + 栈的形式完成。
	
真是一句话提醒梦中人。我又查了很多资料，大概可以总结出这么一句话：

	递归函数像栈一样，都是先进后出。
	
2018/7/23 日继续添加

	平时使用的递归函数实际上用到了提供的函数系统栈
	递归过程可以看做递归函数依次进入函数栈的处理过程
	所有用递归函数可以做的过程一定可以用非递归的方式实现

### 正确的示例

所以，我认为正确的流程是：

先画一张图：

![](/images/basis/2.png)

正如栈一样，函数F(5) 发现自己递归了一个 F(4),所以就把F(5)压入栈中，然后在自己上面添加一个F(4),最后递归到F(1)，发现F(1)有值，所以在依次从栈中取出那些数。最后成功。

```python
F(1) = 1
F(2) = 2 * F(1) = 2
F(3) = 3 * F(2) = 6
F(4) = 4 * F(3) = 24
F(5) = 5 * F(4) = 120
```

上述我认为才是递归的合理流程，这样我才觉得递归真的理解了。

所以我觉得，三流的程序员写递归很苦恼，但最后能写出来，二流的程序员，很苦恼，不会写递归，一流的程序员会写递归，还能很快的写出来。

## 利用递归书写树状图

### 异常记录 RecursionError: maximum recursion depth exceeded in comparison

```python
num = [1, 2, 3]
label = ['A', 'B']

def createTree():
		#print(label)
        if len(label) == 0:
                return
        data = {label[0]: {}}
        for i in range(len(num)):
                if i == 2:
                        data[label.pop(0)][i] = createTree()
                else:
                        data[label[0]][i] = 'YES'
        return data

print(createTree())
```

上面的异常是递归过深，说明没有触碰到停止条件。

去掉上面注释掉的那句话，发现 label 根本没有改变，一直都是 ['A','B']

这个就是关于全局变量之类的问题了，详情，请看我另一篇文章。

[python 全局变量](https://benpaodewoniu.github.io/2018/06/11/python13/)

事实上

data[label.pop(0)][i] = createTree()

中label.pop(0) 是改变了 label 的值，但是可能后面跟着递归，所以没有立即改变，而递归后的 label 因为没有改变，所以它一直是 ['A','B']

我写下下面的代码进行验证：

```python
num = [1, 2, 3]
label = ['A', 'B']

def createTree():
        if len(label) == 0:
                return
        data = {label[0]: {}}
        for i in range(len(num)):
                if i == 2:
                        best = label.pop(0)
                        data[best][i] = createTree()
                else:
                        data[label[0]][i] = 'YES'
        return data

print(createTree())
```

答案和成品的答案是一样的，所以也验证了我之前的猜想。

### 最后的成品 1

```python
num = [1, 2, 3]
label = ['A', 'B']

def createTree():
        if len(label) != 0: #当标签里面还有元素的时候
                best = label[0]	#创建一个新的字典
                data = {best: {}}
        if len(label) == 0:	#如果 label 的长度为零，则说明标签已经没有元素了
                return
        del (label[0])
        for i in range(len(num)):
                if i == 2:
                        data[best][i] = createTree()
                else:
                        data[best][i] = 'YES'
        return data

print(createTree())
```

输出结果：

```python
{'A':
     {0: 'YES',
      1: 'YES',
      2:
         {'B':
              {0: 'YES',
               1: 'YES',
               2: None}
          }
      }
 }
```

### 最后的成品 2

```python
num = [1, 2, 3]
label = ['A', 'B']

def createTree():
        if len(label) != 0: #当标签里面还有元素的时候
                best = label[0]	#创建一个新的字典
                data = {best: {}}
        if len(label) == 0:	#如果 label 的长度为零，则说明标签已经没有元素了
                return
        label.pop(0)
        for i in range(len(num)):
                if i == 2:
                        data[best][i] = createTree()
                else:
                        data[best][i] = 'YES'
        return data

print(createTree())
```

输出结果：

```python
{'A':
     {0: 'YES',
      1: 'YES',
      2:
         {'B':
              {0: 'YES',
               1: 'YES',
               2: None}
          }
      }
 }
```

成品 1 和成品 2 只是在删除 label 元素的时候会有不同。

成品 1 是：del (label[0])

成品 2 是：label.pop(0)

### 最后的成品 3

代码：

```python
num = [1, 2, 3]
label = ['A', 'B']
def change(label):	#这个是
        label = label[0:]
        return label

def createTree(label):
        if len(label) != 0:
                best = label[0]
                data = {best: {}}
        if len(label) == 0:
                return
        del (label[0])
        for i in range(len(num)):
                if i == 2:
                        data[best][i] = createTree(change(label))
                else:
                        data[best][i] = 'YES'
        return data

print(createTree(label))
```

输出结果：

```python
{'A':
     {0: 'YES',
      1: 'YES',
      2:
         {'B':
              {0: 'YES',
               1: 'YES',
               2: None}
          }
      }
 }
```

### 成品 4

```python
num = [1, 2, 3]
label = ['A', 'B']

def createTree():
        if len(label) == 0:
                return
        best = label[0]
        data = {best: {}}
        label.pop(0)
        for i in range(len(num)):
                if i == 2:
                        data[best][i] = createTree()
                else:
                        data[best][i] = 'YES'
        return data

print(createTree())

```

输出结果：

```python
{'A':
     {0: 'YES',
      1: 'YES',
      2:
         {'B':
              {0: 'YES',
               1: 'YES',
               2: None}
          }
      }
 }
```

为什么会有这么多的成品呢，原因是，在我奋战了很长时间，我遇到了很多异常，比如递归深度过深，字典 key 错误，但不管怎么说，我最后成功了，于是有了成品 3 的代码。

但是，一个好的程序员是不能满足正确答案的，还要记录为什么会出错，所以，我改成品 3 的代码，让它有意识的出现我之前遇到的错误，满以为可以记录下来，最后竟发现，所有的成品的是对的，竟然成了优化代码了。在成品中，4 是最好的代码块。

## 递归中有别的函数

```python

def test(i):
    print(i)

def test1(i):
    if i == 0:
        return
    test1(i - 1)
    test(i)

test1(5)

	# 1
	# 2
	# 3
	# 4
	# 5
```

正如逻辑思考的那样，递归中如果有别的函数，也一样压入栈中。

