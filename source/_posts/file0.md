---
title: 文件类型 | YAML文件
date: 2020-05-05 11:34:51
categories:
- 文件
- 类型
tags:
- 文件
---
YAML 是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。

<!-- more -->

YAML 的语法和其他高级语言类似，并且可以简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和YAML非常接近）。

YAML 的配置文件后缀为 `.yml`，如：`runoob.yml` 。

<br/>

# 基本语法

<br/>

- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释

<br/>

# 数据类型

<br/>

YAML 支持以下几种数据类型：

- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
- 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
- 纯量（scalars）：单个的、不可再分的值

<br/>

# YAML 对象

<br/>

对象键值对使用冒号结构表示 key: value，冒号后面要加一个空格。

也可以使用 key:{key1: value1, key2: value2, ...}。

还可以使用缩进表示层级关系；

	key: 
	    child-key: value
	    child-key2: value2

较为复杂的对象格式，可以使用问号加一个空格代表一个复杂的 key，配合一个冒号加一个空格代表一个 value：

	?  
	    - complexkey1
	    - complexkey2
	:
	    - complexvalue1
	    - complexvalue2

意思即对象的属性是一个数组 [complexkey1,complexkey2]，对应的值也是一个数组 [complexvalue1,complexvalue2]

## 例子

对象的一组键值对，使用冒号结构表示。

	animal: pets

转为 JavaScript 如下。

	{ animal: 'pets' }

Yaml 也允许另一种写法，将所有键值对写成一个行内对象。

	hash: { name: Steve, foo: bar } 

转为 JavaScript 如下。

	{ hash: { name: 'Steve', foo: 'bar' } }

<br/>

# YAML 数组

<br/>

以 - 开头的行表示构成一个数组：

	- A
	- B
	- C

YAML 支持多维数组，可以使用行内表示：

	key: [value1, value2, ...]

数据结构的子成员是一个数组，则可以在该项下面缩进一个空格。

	-
	 - A
	 - B
	 - C

一个相对复杂的例子：

	companies:
	    -
	        id: 1
	        name: company1
	        price: 200W
	    -
	        id: 2
	        name: company2
	        price: 500W

意思是 companies 属性是一个数组，每一个数组元素又是由 id、name、price 三个属性构成。

数组也可以使用流式(flow)的方式表示：

	companies: [{id: 1,name: company1,price: 200W},{id: 2,name: company2,price: 500W}]


<br/>

# 复合结构

<br/>

数组和对象可以构成复合结构，例：

	languages:
	  - Ruby
	  - Perl
	  - Python 
	websites:
	  YAML: yaml.org 
	  Ruby: ruby-lang.org 
	  Python: python.org 
	  Perl: use.perl.org

转换为 json 为：

	{ 
	  languages: [ 'Ruby', 'Perl', 'Python'],
	  websites: {
	    YAML: 'yaml.org',
	    Ruby: 'ruby-lang.org',
	    Python: 'python.org',
	    Perl: 'use.perl.org' 
	  } 
	}

<br/>

# 纯量

<br/>

纯量是最基本的，不可再分的值，包括：

- 字符串
- 布尔值
- 整数
- 浮点数
- Null
- 时间
- 日期

## 注意点

	boolean: 
	    - TRUE  #true,True都可以
	    - FALSE  #false，False都可以
	float:
	    - 3.14
	    - 6.8523015e+5  #可以使用科学计数法
	int:
	    - 123
	    - 0b1010_0111_0100_1010_1110    #二进制表示
	null:
	    nodeName: 'node'
	    parent: ~  #使用~表示null
	string:
	    - 哈哈
	    - 'Hello world'  #可以使用双引号或者单引号包裹特殊字符
	    - newline
	      newline2    #字符串可以拆成多行，每一行会被转化成一个空格
	date:
	    - 2018-02-17    #日期必须使用ISO 8601格式，即yyyy-MM-dd
	datetime: 
	    -  2018-02-17T15:02:31+08:00    #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区

## 例子

	number: 12.30
	isSet: true
	parent: ~ 
	iso8601: 2001-12-14t21:59:43.10-05:00 
	date: 1976-07-31

	YAML 允许使用两个感叹号，强制转换数据类型。
	e: !!str 123
	f: !!str true

<br/>

# 字符串

<br/>

字符串是最常见，也是最复杂的一种数据类型。

字符串默认不使用引号表示。

	str: 这是一行字符串

转为 JavaScript 如下。

	{ str: '这是一行字符串' }

如果字符串之中包含空格或特殊字符，需要放在引号之中。

	str: '内容： 字符串'

转为 JavaScript 如下。

	{ str: '内容: 字符串' }

单引号和双引号都可以使用，双引号不会对特殊字符转义。

	s1: '内容\n字符串'
	s2: "内容\n字符串"

转为 JavaScript 如下。

	{ s1: '内容\\n字符串', s2: '内容\n字符串' }

单引号之中如果还有单引号，必须连续使用两个单引号转义。

	str: 'labor''s day' 

转为 JavaScript 如下。

	{ str: 'labor\'s day' }

字符串可以写成多行，从第二行开始，必须有一个单空格缩进。换行符会被转为空格。

	str: 这是一段
	  多行
	  字符串

转为 JavaScript 如下。

	{ str: '这是一段 多行 字符串' }

多行字符串可以使用|保留换行符，也可以使用>折叠换行。

	this: |
	  Foo
	  Bar
	that: >
	  Foo
	  Bar

转为 JavaScript 代码如下。

	{ this: 'Foo\nBar\n', that: 'Foo Bar\n' }

`+`表示保留文字块末尾的换行，`-`表示删除字符串末尾的换行。


	s1: |
	  Foo

	s2: |+
	  Foo


	s3: |-
	  Foo

转为 JavaScript 代码如下。

	{ s1: 'Foo\n', s2: 'Foo\n\n\n', s3: 'Foo' }

字符串之中可以插入 HTML 标记。

	message: |

	  <p style="color: red">
	    段落
	  </p>

转为 JavaScript 如下。


	{ message: '\n<p style="color: red">\n  段落\n</p>\n' }

<br/>

# 引用

<br/>

`&` 锚点和 `*` 别名，可以用来引用:

	defaults: &defaults
	  adapter:  postgres
	  host:     localhost

	development:
	  database: myapp_development
	  <<: *defaults

	test:
	  database: myapp_test
	  <<: *defaults

相当于:

	defaults:
	  adapter:  postgres
	  host:     localhost

	development:
	  database: myapp_development
	  adapter:  postgres
	  host:     localhost

	test:
	  database: myapp_test
	  adapter:  postgres
	  host:     localhost

`&` 用来建立锚点（defaults），`<<` 表示合并到当前数据，`*` 用来引用锚点。

下面是另一个例子:

	- &showell Steve 
	- Clark 
	- Brian 
	- Oren 
	- *showell

转为 JavaScript 代码如下:

	[ 'Steve', 'Clark', 'Brian', 'Oren', 'Steve' ]

