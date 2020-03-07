---
title: numpy 和 pandas 的区别详解
date: 2018-12-17 20:56:33
categories:
- python
tags:
- python
- python 第三方库
- pandas
- numpy
---
如题，另外，这篇是我大段 copy 的。

<!-- more -->

[numpy和pandas入门](https://zhuanlan.zhihu.com/p/27624814)

数据分析绝对绕不过的三个包是numpy、scipy和pandas。numpy是Python的数值计算扩展，专门用来处理矩阵，它的运算效率比列表更高效。scipy是基于numpy的科学计算包，包括统计、线性代数等工具。pandas是基于numpy的数据分析工具，能更方便的操作大型数据集。

numpy和pandas

## numpy

numpy的数据结构是n维的数组对象，叫做ndarray。Python的list虽然也能表示，但是不高效，随着列表数据的增加，效率会降低。

我们首先载入numpy包，因为它是第三方工具，所以每次使用前必须在代码中载入。as是命名为别名，方便调用，np是numpy约定俗成的简写。

{% img /images/python/39_0.png %}

创建数组使用numpy中的array函数，新手要记住加np。我们将系统自带的列表list转换成了numpy中的数组。

{% img /images/python/39_1.png %}

嵌套列表会被转换为一个多维数组，它也可以被称为矩阵。

array数组需要注意的是，它内部的元素必须为相同类型，比如数值或者字符串。可以用dtype查询其类型，不用加括号。

{% img /images/python/39_2.png %}

numpy的数据类型比较丰富，包括int8，int16，int32等，不过这块更接近计算机底层，数据分析用不太到。当我们想转换数据格式时，可以用astype函数。

{% img /images/python/39_3.png %}

数组的计算非常方便，不要大量的循环即可批量运算。

{% img /images/python/39_4.png %}

数组索引和列表相同，通过方括号和数字即可选择，也可直接赋值。

{% img /images/python/39_5.png %}

可以通过递归对多维数组进行筛选，如果省略了后面的索引，则返回次一级的维度，这一点和list一样。

{% img /images/python/39_6.png %}

numpy除了上述的基础操作之外，还有reshape、T转置、ufunc、sort等函数，功能强大，大家可以自行查阅文档学习，这里我不再花费时间讲解。对于数据分析师，pandas是一个更常用的包，在抽象概念上它更接近我们熟悉的excel和sql，也是最主要的分析工具。

## pandas

pandas有两个主要的数据结构，Series和DataFrame，记住大小写区分，后续使用中不多提醒。Series类似于一维数组，和numpy的array接近，由一组数据和数据标签组成。数据标签有索引的作用。

{% img /images/python/39_7.png %}

加载pandas包，通过Series函数生成一个对象。我们很明显地看到，在jupyter上它的样式不同于array，它是竖着的。右边是我们输入的一组数据，左边是数据的索引，即标签。数据标签是pandas区分于numpy的重要特征。

索引不一定是从0开始的数字，它可以被定义。

{% img /images/python/39_8.png %}

索引的概念有点像SQL的主键，不过它的功能更强大，分析师能够很轻松的通过索引选取一个数据或者一组数据。
{% img /images/python/39_9.png %}

index函数可以显示Series的索引。Series和array一样，通过方括号选取数据，当要选取多个数据时，应该用列表表示多个索引，所以第三个案例嵌套了两层方括号。

如果数据是一个字典，也能直接通过这个字典创建Series。

{% img /images/python/39_10.png %}
此时，字典的key就是Series的索引。

{% img /images/python/39_11.png %}

Series有自动对齐索引的功能，当自定义的索引qinqin和字典队员不上时，会自动选择NaN，即结果为空，表示缺失。缺失值的处理会在后续讲解。

### DataFrame

Series是一维的数据结构，DataFrame是一个表格型的数据结构，它含有不同的列，每列都是不同的数据类型。我们可以把DataFrame看作Series组成的字典，它既有行索引也有列索引。想象得更明白一点，它类似一张excel表格或者SQL，只是功能更强大。

构建DataFrame的方法有很多，最常用的是传入一个字典。

{% img /images/python/39_12.png %}
DataFrame会自动补充索引，并且将字典的key作为列标签，即column。在这里，dict的key顺序是DataFame的顺序，不再是无序的。


DataFrame中可以通过info函数直接查看数据类型和统计。

{% img /images/python/39_13.png %}

列名后面是列的非空值统计量，以及数据类型，最后一行是DataFrame占用的内存大小，对于pandas来说，千万行几百兆的数据也是不再话下的。

{% img /images/python/39_14.png %}
DataFrame的数据类型变更和numpy一样，用astype就行，记住要赋值。df.age = df.age.astype，图上只是举例，没有真的更改数据类型。

DataFrame的索引方式和Series一样，它选取的是列。

{% img /images/python/39_15.png %}

返回的是一组Series，索引和原DataFrame一致。除了方括号选取，DataFrame还有更简单的选取方法。

{% img /images/python/39_16.png %}

行也可以通过通过位置获取，用索引字段ix的方式。

{% img /images/python/39_17.png %}

第二个方法不需要调用ix，直接以切片的形式获取行，切片的用啊和数组一样。记住，切片无法用单独的数字选取，只能用冒号切选范围。

列可以通过赋值的方式修改和添加，当列的名称是全新，则会在DataFrame的最右边自动加上新的一列。
{% img /images/python/39_18.png %}

列表和数组都可以赋值到列，长度必须匹配，列表是按照顺序，数组的话，可以按index参数设置的索引对应，若为空，则填上缺失值。

DataFrame的index不可更改，这是为了数据的安全性，但我们能通过index函数获取详情。

{% img /images/python/39_19.png %}
DataFrame在数据选取上面非常强大。

{% img /images/python/39_20.png %}

它支持常用的逻辑判断，例子中，后两种数据选取是通过布尔数组过滤出我们想要的结果。

{% img /images/python/39_21.png %}

df.sex == '男'返回了一个布尔数组(记住，是两个等号，一个等号是赋值），然后通过布尔数组找出df中符合条件为true的结果。

现在我们希望找出年龄在20以下，且性别为男的人。

{% img /images/python/39_22.png %}

这里用到布尔类型数据的计算公式，true and true 为true，false and true 为false。pandas中的逻辑符号，并且是&，或者是|。接下来过滤出性别为女，或者年龄为18的人。

{% img /images/python/39_23.png %}

当逻辑条件复杂时，这种写法并不优雅，比如性别为男，且年龄在18岁，以及性别为女，且年龄在18岁以上的两类人群，这种过滤条件就比较复杂了。pandas中可以用query函数以类SQL语言执行查询。

{% img /images/python/39_24.png %}

query中可以直接使用列名，它的功能远不至于此，大家有兴趣可以深入学习。

{% img /images/python/39_25.png %}

DataFrame可以直接在列上进行运算，当DataFrame和DataFrame之间运算时，按索引进行加减乘除。

{% img /images/python/39_26.png %}

如果直接用加法，若列名匹配不上，会产生NaN 的缺失值，我们可以在后续后续fillna函数填充。另外一种方法是add函数，它可以直接通过参数选择填充值。减乘除对应sub、mul、div。

DataFrame还有两个常用函数，又特别容易搞混的是，iloc和loc。

{% img /images/python/39_27.png %}

它们都是通过索引选取行，iloc是通过所在行的数字为索引，loc是所在行的标签为索引，简单讲，iloc是第几行，loc是标签。当索引没有标签时，loc和iloc等价。两者支持冒号的范围选择。

上文提到的过的ix，则是两者的混合，即可以行号，也可以行索引。

当行和列需要同时选择的时候，用逗号分割，逗号前是想要选择的行，逗号后是想要选择的列。

{% img /images/python/39_28.png %}

完整形式是ix[ 行1:行2, 列1:列2 ]，iloc和loc也支持行列同时选择，方法一样。新人接触这几块比较容易混淆。后续联系即可掌握。



