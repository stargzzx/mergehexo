---
title: linux | 语法
date: 2020-03-16 17:53:42
categories:
- linux
tags:
- linux
---
掌握 linux 语法是写系统脚本和提升技术栈的必经之路。

因为内容较少，就放在一个文件里面了。

<!-- more -->

<br/>

# 参考资料

<br/>

[Shell 教程](https://www.runoob.com/linux/linux-shell.html)

<br/>

# Shell 教程

<br/>

Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

Ken Thompson 的 sh 是第一种 Unix Shell，Windows Explorer 是一个典型的图形界面 Shell。

## shell 环境

Shell 编程跟 JavaScript、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Linux 的 Shell 种类众多，常见的有：

- Bourne Shell（/usr/bin/sh或/bin/sh）
- Bourne Again Shell（/bin/bash）
- C Shell（/usr/bin/csh）
- K Shell（/usr/bin/ksh）
- Shell for Root（/sbin/sh）

本教程关注的是 Bash，也就是 Bourne Again Shell，由于易用和免费，Bash 在日常工作中被广泛使用。同时，Bash 也是大多数Linux 系统默认的 Shell。

在一般情况下，人们并不区分 Bourne Shell 和 Bourne Again Shell，所以，像 #!/bin/sh，它同样也可以改为 #!/bin/bash。

\#! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。

## 查询可用的 shell 环境

	cat /etc/shells 

输出

	/bin/sh
	/bin/bash
	/sbin/nologin

## 查看正在使用的 shell 环境

	echo $SHELL

## 第一个shell脚本

打开文本编辑器(可以使用 vi/vim 命令来创建文件)，新建一个文件 test.sh，扩展名为 sh（sh代表shell），扩展名并不影响脚本执行，见名知意就好，如果你用 php 写 shell 脚本，扩展名就用 php 好了。

输入一些代码，第一行一般是这样：

```shell
#!/bin/bash
echo "Hello World !"
```

\#! 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell。

echo 命令用于向窗口输出文本。

## 运行 Shell 脚本有两种方法：

### 作为可执行程序

将上面的代码保存为 test.sh，并 cd 到相应目录：

	chmod +x ./test.sh  #使脚本具有执行权限
	./test.sh  #执行脚本

注意，一定要写成 ./test.sh，而不是 test.sh，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

## 作为解释器参数

这种运行方式是，直接运行解释器，其参数就是 shell 脚本的文件名，如：

	/bin/sh test.sh
	/bin/php test.php

这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

<br/>

# Shell 变量

<br/>

## 定义变量

定义变量时，变量名不加美元符号（$，PHP语言中变量需要），如：

	your_name="runoob.com"

注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样。同时，变量名的命名须遵循如下规则：

- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线（_）。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。

除了显式地直接赋值，还可以用语句给变量赋值，如：

	for file in `ls /etc`
	或
	for file in $(ls /etc)

以上语句将 /etc 下目录的文件名循环出来。

## 使用变量

使用一个定义过的变量，只要在变量名前面加美元符号即可，如：

	your_name="qinjx"
	echo $your_name
	echo ${your_name}

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：

	for skill in Ada Coffe Action Java; do
	    echo "I am good at ${skill}Script"
	done

如果不给skill变量加花括号，写成echo "I am good at $skillScript"，解释器就会把$skillScript当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

推荐给所有变量加上花括号，这是个好的编程习惯。

## 只读变量

使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。

下面的例子尝试更改只读变量，结果报错：


	#!/bin/bash
	myUrl="http://www.google.com"
	readonly myUrl

## 删除变量

使用 unset 命令可以删除变量。语法：

	unset variable_name

变量被删除后不能再次使用。unset 命令不能删除只读变量。

	#!/bin/sh
	myUrl="http://www.runoob.com"
	unset myUrl

<br/>

# Shell 字符串

<br/>

## 单引号

单引号字符串的限制：

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。

## 双引号

双引号的优点：

- 双引号里可以有变量
- 双引号里可以出现转义字符

<br/>

	your_name="runoob"
	# 使用双引号拼接
	greeting="hello, "$your_name" !"
	greeting_1="hello, ${your_name} !"
	echo $greeting  $greeting_1
	# 使用单引号拼接
	greeting_2='hello, '$your_name' !'
	greeting_3='hello, ${your_name} !'
	echo $greeting_2  $greeting_3

## 获取字符串长度

	string="abcd"
	echo ${#string} #输出 4

## 提取子字符串

以下实例从字符串第 2 个字符开始截取 4 个字符：

	string="runoob is a great site"
	echo ${string:1:4} # 输出 unoo

注意：第一个字符的索引值为 0。

## 查找子字符串

查找字符 i 或 o 的位置(哪个字母先出现就计算哪个)：

	string="runoob is a great site"
	echo `expr index "$string" io`  # 输出 4

注意： 以上脚本中 \` 是反引号，而不是单引号 \'，不要看错了哦

<br/>

# Shell 数组

<br/>

bash支持一维数组（不支持多维数组），并且没有限定数组的大小。

类似于 C 语言，数组元素的下标由 0 开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于 0。

## 定义数组

在 Shell 中，用括号来表示数组，数组元素用"空格"符号分割开。定义数组的一般形式为：


	array_name=(value0 value1 value2 value3)
	或者

	array_name=(
	value0
	value1
	value2
	value3
	)

还可以单独定义数组的各个分量：

	array_name[0]=value0
	array_name[1]=value1
	array_name[n]=valuen

可以不使用连续的下标，而且下标的范围没有限制。

## 读取数组

读取数组元素值的一般格式是：

	${数组名[下标]}
	例如：

	valuen=${array_name[n]}

使用@ 或 * 可以获取数组中的所有元素，例如：

	echo ${array_name[@]}

## 获取数组的长度

获取数组长度的方法与获取字符串长度的方法相同，例如：

	# 取得数组元素的个数
	length=${#array_name[@]}
	# 或者
	length=${#array_name[*]}
	# 取得数组单个元素的长度
	lengthn=${#array_name[n]}

<br/>

# Shell 注释

<br/>

## 单行注释

以 # 开头的行就是注释，会被解释器忽略。

通过每一行加一个 # 号设置多行注释，像这样：

	#--------------------------------------------
	# 这是一个注释
	# author：菜鸟教程
	# site：www.runoob.com

如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？

每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

## 多行注释

多行注释还可以使用以下格式：

	:<<EOF
	注释内容...
	注释内容...
	注释内容...
	EOF

<br/>

# shell 传递参数

<br/>

我们可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：$n。n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推……

实例
以下实例我们向脚本传递三个参数，并分别输出，其中 $0 为执行的文件名：

	#!/bin/bash
	echo "Shell 传递参数实例！";
	echo "执行的文件名：$0";
	echo "第一个参数为：$1";
	echo "第二个参数为：$2";
	echo "第三个参数为：$3";

为脚本设置可执行权限，并执行脚本，输出结果如下所示：

	$ chmod +x test.sh 
	$ ./test.sh 1 2 3
	Shell 传递参数实例！
	执行的文件名：./test.sh
	第一个参数为：1
	第二个参数为：2
	第三个参数为：3

另外，还有几个特殊字符用来处理参数：

|参数|说明|
|---|---|
|$#	|传递到脚本的参数个数|
|$*	|以一个单字符串显示所有向脚本传递的参数。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。|
|$$	|脚本运行的当前进程ID号|
|$!	|后台运行的最后一个进程的ID号|
|$@	|与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。|
|$-	|显示Shell使用的当前选项，与set命令功能相同。|
|$?	|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|

<br/>

# Shell 基本运算符

<br/>

Shell 和其他编程语言一样，支持多种运算符，包括：

- 算数运算符
- 关系运算符
- 布尔运算符
- 字符串运算符
- 文件测试运算符

原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

expr 是一款表达式计算工具，使用它能完成表达式的求值操作。

例如，两个数相加(注意使用的是反引号 \` 而不是单引号 \')：

## 两数之和

	#!/bin/bash
	val=`expr 2 + 2`
	echo "两数之和为 : $val"

两点注意：

- 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
- 完整的表达式要被 ` ` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。

## 算术运算符

|运算符|	说明	|举例|
|---|---|---|
|+	|加法|	\`expr $a + $b\` 结果为 30。|
|-	|减法	|\`expr $a - $b\` 结果为 -10。|
|*	|乘法	|\`expr $a \\* $b\` 结果为  200。|
|/	|除法	|\`expr $b / $a\` 结果为 2。|
|%	|取余	|\`expr $b % $a\` 结果为 0。|
|=	|赋值	|a=$b 将把变量 b 的值赋给 a。|
|==	|相等。用于比较两个数字，相同则返回 true。|	[ $a == $b ] 返回 false。|
|!=	|不相等。用于比较两个数字，不相同则返回 true。|	[ $a != $b ] 返回 true。|

注意：条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]。


	a=10
	b=20

	val=`expr $a + $b`
	echo "a + b : $val"

	val=`expr $a - $b`
	echo "a - b : $val"

	val=`expr $a \* $b`
	echo "a * b : $val"

	val=`expr $b / $a`
	echo "b / a : $val"

	val=`expr $b % $a`
	echo "b % a : $val"

	if [ $a == $b ]
	then
	   echo "a 等于 b"
	fi
	if [ $a != $b ]
	then
	   echo "a 不等于 b"
	fi


执行脚本，输出结果如下所示：

	a + b : 30
	a - b : -10
	a * b : 200
	b / a : 2
	b % a : 0
	a 不等于 b

注意：

- 乘号(*)前边必须加反斜杠(\\)才能实现乘法运算；
- if...then...fi 是条件语句，后续将会讲解。
- 在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\\" 。

## 关系运算符

关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

下表列出了常用的关系运算符，假定变量 a 为 10，变量 b 为 20：

|运算符|	说明|	举例|
|---|---|---|
|-eq|	检测两个数是否相等，相等返回 true。|	[ $a -eq $b ] 返回 false。|
|-ne|	检测两个数是否不相等，不相等返回 true。|	[ $a -ne $b ] 返回 true。|
|-gt|	检测左边的数是否大于右边的，如果是，则返回 true。|	[ $a -gt $b ] 返回 false。|
|-lt|	检测左边的数是否小于右边的，如果是，则返回 true。|	[ $a -lt $b ] 返回 true。|
|-ge|	检测左边的数是否大于等于右边的，如果是，则返回 true。|	[ $a -ge $b ] 返回 false。|
|-le|	检测左边的数是否小于等于右边的，如果是，则返回 true。|	[ $a -le $b ] 返回 true。|


	#!/bin/bash
	a=10
	b=20

	if [ $a -eq $b ]
	then
	   echo "$a -eq $b : a 等于 b"
	else
	   echo "$a -eq $b: a 不等于 b"
	fi
	if [ $a -ne $b ]
	then
	   echo "$a -ne $b: a 不等于 b"
	else
	   echo "$a -ne $b : a 等于 b"
	fi
	if [ $a -gt $b ]
	then
	   echo "$a -gt $b: a 大于 b"
	else
	   echo "$a -gt $b: a 不大于 b"
	fi
	if [ $a -lt $b ]
	then
	   echo "$a -lt $b: a 小于 b"
	else
	   echo "$a -lt $b: a 不小于 b"
	fi
	if [ $a -ge $b ]
	then
	   echo "$a -ge $b: a 大于或等于 b"
	else
	   echo "$a -ge $b: a 小于 b"
	fi
	if [ $a -le $b ]
	then
	   echo "$a -le $b: a 小于或等于 b"
	else
	   echo "$a -le $b: a 大于 b"
	fi

执行脚本，输出结果如下所示：

	10 -eq 20: a 不等于 b
	10 -ne 20: a 不等于 b
	10 -gt 20: a 不大于 b
	10 -lt 20: a 小于 b
	10 -ge 20: a 小于 b
	10 -le 20: a 小于或等于 b

## 布尔运算符

下表列出了常用的布尔运算符，假定变量 a 为 10，变量 b 为 20：

|运算符|	说明|	举例|
|---|---|---|
|!|	非运算，表达式为 true 则返回 false，否则返回 true。|	[ ! false ] 返回 true。|
|-o	|或运算，有一个表达式为 true 则返回 true。|	[ $a -lt 20 -o $b -gt 100 ] 返回 true。|
|-a|	与运算，两个表达式都为 true 才返回 true。|	[ $a -lt 20 -a $b -gt 100 ] 返回 false。|

	#!/bin/bash
	a=10
	b=20

	if [ $a != $b ]
	then
	   echo "$a != $b : a 不等于 b"
	else
	   echo "$a == $b: a 等于 b"
	fi
	if [ $a -lt 100 -a $b -gt 15 ]
	then
	   echo "$a 小于 100 且 $b 大于 15 : 返回 true"
	else
	   echo "$a 小于 100 且 $b 大于 15 : 返回 false"
	fi
	if [ $a -lt 100 -o $b -gt 100 ]
	then
	   echo "$a 小于 100 或 $b 大于 100 : 返回 true"
	else
	   echo "$a 小于 100 或 $b 大于 100 : 返回 false"
	fi
	if [ $a -lt 5 -o $b -gt 100 ]
	then
	   echo "$a 小于 5 或 $b 大于 100 : 返回 true"
	else
	   echo "$a 小于 5 或 $b 大于 100 : 返回 false"
	fi

执行脚本，输出结果如下所示：

	10 != 20 : a 不等于 b
	10 小于 100 且 20 大于 15 : 返回 true
	10 小于 100 或 20 大于 100 : 返回 true
	10 小于 5 或 20 大于 100 : 返回 false

## 逻辑运算符

以下介绍 Shell 的逻辑运算符，假定变量 a 为 10，变量 b 为 20:

|运算符|	说明|	举例|
|---|---|---|
|&&	|逻辑的| AND|	[[ $a -lt 100 && $b -gt 100 ]] 返回 false|
|\|\|	|逻辑的| OR|	[[ $a -lt 100 || $b -gt 100 ]] 返回 true|


	#!/bin/bash

	a=10
	b=20

	if [[ $a -lt 100 && $b -gt 100 ]]
	then
	   echo "返回 true"
	else
	   echo "返回 false"
	fi

	if [[ $a -lt 100 || $b -gt 100 ]]
	then
	   echo "返回 true"
	else
	   echo "返回 false"
	fi

执行脚本，输出结果如下所示：

	返回 false
	返回 true


## 字符串运算符

下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：

|运算符|	说明	|举例|
|---|---|---|
|=|	检测两个字符串是否相等，相等返回 true.|	[ $a = $b ] 返回 false。|
|!=	|检测两个字符串是否相等，不相等返回 true。|	[ $a != $b ] 返回 true。|
|-z|	检测字符串长度是否为0，为0返回 true。|	[ -z $a ] 返回 false。|
|-n|	检测字符串长度是否不为 0，不为 0 返回 true。|	[ -n "$a" ] 返回 true。|
|$	|检测字符串是否为空，不为空返回 true。|	[ $a ] 返回 true。|


	#!/bin/bash

	a="abc"
	b="efg"

	if [ $a = $b ]
	then
	   echo "$a = $b : a 等于 b"
	else
	   echo "$a = $b: a 不等于 b"
	fi
	if [ $a != $b ]
	then
	   echo "$a != $b : a 不等于 b"
	else
	   echo "$a != $b: a 等于 b"
	fi
	if [ -z $a ]
	then
	   echo "-z $a : 字符串长度为 0"
	else
	   echo "-z $a : 字符串长度不为 0"
	fi
	if [ -n "$a" ]
	then
	   echo "-n $a : 字符串长度不为 0"
	else
	   echo "-n $a : 字符串长度为 0"
	fi
	if [ $a ]
	then
	   echo "$a : 字符串不为空"
	else
	   echo "$a : 字符串为空"
	

执行脚本，输出结果如下所示：

	abc = efg: a 不等于 b
	abc != efg : a 不等于 b
	-z abc : 字符串长度不为 0
	-n abc : 字符串长度不为 0
	abc : 字符串不为空

## 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：

|操作符|	说明|	举例|
|---|---|---|
|-b file|	检测文件是否是块设备文件，如果是，则返回 true。	|[ -b $file ] 返回 false。|
|-c file|	检测文件是否是字符设备文件，如果是，则返回 true。	|[ -c $file ] 返回 false。|
|-d file|	检测文件是否是目录，如果是，则返回 true。	|[ -d $file ] 返回 false。|
|-f file|	检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	|[ -f $file ] 返回 true。|
|-g file|	检测文件是否设置了 SGID 位，如果是，则返回 true。	|[ -g $file ] 返回 false。|
|-k file|	检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	|[ -k $file ] 返回 false。|
|-p file|	检测文件是否是有名管道，如果是，则返回 true。|	[ -p $file ] 返回 false。|
|-u file|	检测文件是否设置了 SUID 位，如果是，则返回 true。|	[ -u $file ] 返回 false。|
|-r file|	检测文件是否可读，如果是，则返回 true。	|[ -r $file ] 返回 true。|
|-w file|	检测文件是否可写，如果是，则返回 true。	|[ -w $file ] 返回 true。|
|-x file|	检测文件是否可执行，如果是，则返回 true。|	[ -x $file ] 返回 true。|
|-s file|	检测文件是否为空（文件大小是否大于0），不为空返回 true。	|[ -s $file ] 返回 true。|
|-e file|	检测文件（包括目录）是否存在，如果是，则返回 true。	|[ -e $file ] 返回 true。|

其他检查符：

- \-S: 判断某文件是否 socket。
- \-L: 检测文件是否存在并且是一个符号链接。

变量 file 表示文件 /var/www/runoob/test.sh，它的大小为 100 字节，具有 rwx 权限。下面的代码，将检测该文件的各种属性：

	#!/bin/bash

	file="/var/www/runoob/test.sh"
	if [ -r $file ]
	then
	   echo "文件可读"
	else
	   echo "文件不可读"
	fi
	if [ -w $file ]
	then
	   echo "文件可写"
	else
	   echo "文件不可写"
	fi
	if [ -x $file ]
	then
	   echo "文件可执行"
	else
	   echo "文件不可执行"
	fi
	if [ -f $file ]
	then
	   echo "文件为普通文件"
	else
	   echo "文件为特殊文件"
	fi
	if [ -d $file ]
	then
	   echo "文件是个目录"
	else
	   echo "文件不是个目录"
	fi
	if [ -s $file ]
	then
	   echo "文件不为空"
	else
	   echo "文件为空"
	fi
	if [ -e $file ]
	then
	   echo "文件存在"
	else
	   echo "文件不存在"
	fi

执行脚本，输出结果如下所示：

	文件可读
	文件可写
	文件可执行
	文件为普通文件
	文件不是个目录
	文件不为空
	文件存在

<br/>

# Shell echo命令

<br/>

Shell 的 echo 指令与 PHP 的 echo 指令类似，都是用于字符串的输出。命令格式：

	echo string

您可以使用echo实现更复杂的输出格式控制。

## 显示普通字符串
	
	echo "It is a test"

这里的双引号完全可以省略，以下命令与上面实例效果一致：

	echo It is a test

## 显示转义字符

	echo "\"It is a test\""

结果将是:

	"It is a test"

同样，双引号也可以省略

## 显示变量

read 命令从标准输入中读取一行,并把输入行的每个字段的值指定给 shell 变量

	#!/bin/sh
	read name 
	echo "$name It is a test"

以上代码保存为 test.sh，name 接收标准输入的变量，结果将是:

	[root@www ~]# sh test.sh
	OK                     #标准输入
	OK It is a test        #输出

## 显示换行

	echo -e "OK! \n" # -e 开启转义
	echo "It is a test"

输出结果：

	OK!
	It is a test

## 显示不换行

	#!/bin/sh
	echo -e "OK! \c" # -e 开启转义 \c 不换行
	echo "It is a test"

输出结果：

	OK! It is a test

## 显示结果定向至文件

	echo "It is a test" > myfile

## 原样输出字符串，不进行转义或取变量(用单引号)

	echo '$name\"'

输出结果：

	$name\"

## 显示命令执行结果

	echo `date`

注意： 这里使用的是反引号 \`, 而不是单引号 \'。

结果将显示当前日期

	Thu Jul 24 10:08:46 CST 2014

<br/>

# Shell test 命令

<br/>

[后面内容的链接](https://www.runoob.com/linux/linux-shell-test.html)
