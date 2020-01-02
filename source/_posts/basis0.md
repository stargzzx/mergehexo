---
title: 正则表达式
date: 2018-06-13 11:22:54
categories:
- basis
tags:
- 正则表达式
- basis
- python
- Regular expression
- tutorial
---
这里介绍广谱正则表达式：
<!--more-->
这里引入我另一篇关于在 python 中如何操纵表达式的博文。
[python re 模块](https://benpaodewoniu.github.io/2018/06/08/python8/)
## 基础
{% codeblock %}

 . 匹配任意字符（除了\n）

[...] 匹配字符集中的任意一个字符

	中括号里面既可以是具体的字符 [wer]

	又可以是区间 [0-9]

	[^1] 意思为不等于 1

	[.] 仅代表 .

	[a-zA-Z]

	汉字 [\u4E00-\u9FA5]

	在匹配[]时，需要转义 re.match(r'\[[\w]\]','[a]')

\d 匹配数字

\D 匹配非数字

\s 匹配空白

\S 匹配非空白

\w 匹配单词字符 相当于 A-Za-z0-9_

\W 匹配非单词字符

* 匹配前一个字符0次或者无限次

+ 匹配前一个字符1次或者无限次

？匹配前一个字符0次或者1次

{m} / {m,n} 匹配前一个字符 m 次或者m 到 n 次（少于 m 次将匹配不成功）

贪婪模式下是反向匹配的

*? / +? / ?? /{m,n}? 匹配模式为非贪婪模式（尽可能少匹配字符，*? 最少为 0 次 ， +? 最少为1次 ，?? 最少为 0 次）从左边开始匹配

^ 匹配字符串的开头

$ 匹配字符串的结尾

\A /\Z 指定的字符串必须出现在开头/结尾

	re.match(r'\Aimmoc[\w]*','......')

| 匹配左右任意一个表达式 代表或的意思

	re.match(r'abc|d','...')

	str = "bobby123"
	
	match_str = "(bobby123|booby)"

	输出 booby123
	
{% endcodeblock %}
## 例子
{% codeblock %}
str = "booooobb123"
	
	想将booooob提取出来

	python语法：

	match_str = ".*(b.*b).*"

		.* 是贪婪匹配模式

		.* 尽可能匹配多的字符，所以 booooo 都会匹配到

		所以最终输出为 bb

	match_str = ".*?(b.*b).*"

		加上？后变成非贪婪模式，尽可能匹配少的字符

		所以.*? 的意思就是匹配了 0 个，因为 b 前面没有字符

		所以最终输出为 booooobb

	match_str = ".*?(b.*?b).*"

		括号里面加上 ？ 也变成了非贪婪模式

		尽可能匹配少的字符，但由于最后结尾是 b ，所以 b 与 b 之间的字符需要匹配出来，但是后面两个 b 由于采用最少匹配，所以只得到一个

		打印出的结果是booooob

	如果将改成 str = "b00bbakabaai192"

	采用 match_str = ".*?(b.*?b).*"

		打印结果是b00b

	str = "booooobaaaooobbb123"

	match_str = ".*(b.+b).*"

		因为 + 是最少匹配一个，而前面的 .* 又是贪婪模式

		所以最后的输出是 bbb

  	str = "b00b123b321b123"
	
	match_str = ".*(b.{3,5}b).*"

		输出为 b321b

	str = "b00b123b321b123"
	
	match_str = ".*(b.{2}b).*"

		输出为 b00b 因为后面的不能满足
		
{% endcodeblock %}
## 有意识的匹配输出
{% codeblock %}

在 python 中如果模式匹配中有括号，那么匹配的输出结果是括号内容

如果含有多层嵌套括号，那么最外层是数组 1 ，次外层是数组2

	str = "booby123"

	match_str = "((booby|bbooy)123)"

	match_obj = re.match(match_str,str)

	match_obi.group(1)

		输出booby123
	
	match_obi.group(2)

		输出booby
	
（ab） 括号中表达式作为一个分组

	re.match(r'[\w]{4,6}@(163|126).com','immoc#126.com')

\<number> 引用编号为 num 的分组匹配到的字符串

	ma = re.match(r'<([\w]+>)\1','<book>book>')

	ma = <book>book>

	r'<([\w}+>)[\w]+</\1','<book>python</book>'

(?p<name>) 分组起一个别名

(?p=name) 引用别名为 name 的分组匹配字符串

	r'<(?p<mark>[\w}+>)[\w]+</(?p=mark)','<book>python</book>'

{% endcodeblock %}