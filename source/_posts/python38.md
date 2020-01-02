---
title: python pandas 函数详解
date: 2018-12-17 10:05:43
categories:
- python
tags:
- python
- python 第三方库
- pandas
---
如题。
<!-- more -->
# R
## read_csv()
pandas可以将读取到的表格型数据（文件不一定要是表格）转成DataFrame类型的数据结构，然后我们可以通过操作DataFrame进行数据分析，数据预处理以及行和列的操作等。
功能：从文件、URL、文件新对象中加载带有分隔符的数据，默认分隔符是逗号。
read_csv(*filepath_or_buffer*, *sep='*, *'*, *delimiter=None*, *header='infer'*, *names=None*, *index_col=None*,*usecols=None*, *squeeze=False*, *prefix=None*, *mangle_dupe_cols=True*, *dtype=None*, *engine=None*,*converters=None*, *true_values=None*, *false_values=None*, *skipinitialspace=False*, *skiprows=None*, *nrows=None*,*na_values=None*, *keep_default_na=True*, *na_filter=True*, *verbose=False*, *skip_blank_lines=True*,*parse_dates=False*, *infer_datetime_format=False*, *keep_date_col=False*, *date_parser=None*, *dayfirst=False*,*iterator=False*, *chunksize=None*, *compression='infer'*, *thousands=None*, *decimal='.'*, *lineterminator=None*,*quotechar='"'*, *quoting=0*, *escapechar=None*, *comment=None*, *encoding=None*, *dialect=None*, *tupleize_cols=False*,*error_bad_lines=True*, *warn_bad_lines=True*, *skipfooter=0*, *skip_footer=0*, *doublequote=True*,*delim_whitespace=False*, *as_recarray=False*, *compact_ints=False*, *use_unsigned=False*, *low_memory=True*,*buffer_lines=None*, *memory_map=False*, *float_precision=None*)
返回参数
返回参数 : DataFrame or TextParser
DataFrame：二维标记数据结构
列可以是不同的数据类型，是最常用的pandas对象，如同Series对象一样接受多种输入：lists/dicts/Series/DataFrame。
ceshi.csv 参数

	,c1,c2,c3
	a,0,5,10
	b,1,6,11
	c,2,7,12
	d,3,8,13
	e,4,9,14
	
{% codeblock %}

import pandas as pd
obj=pd.read_csv('ceshi.csv')
print obj
print type(obj)
print obj.dtypes
 Unnamed: 0  c1  c2  c3
0          a   0   5  10
1          b   1   6  11
2          c   2   7  12
3          d   3   8  13
4          e   4   9  14
<class 'pandas.core.frame.DataFrame'>
Unnamed: 0    object
c1             int64
c2             int64
c3             int64
dtype: object

{% endcodeblock %}

	代码将有列索引但没有行索引的数据，read_csv会自动添加上行索引（即使原数据有行索引）。
	read_csv读取的数据类型为Dataframe
	obj.dtypes可以查看每列的数据类型
	
传递参数

	filepath_or_buffer ：
		字符串，或者任何对象的read()方法。这个字符串可以是URL，有效的URL方案包括http、ftp、s3和文件。可以直接写入"文件名.csv"
	header :
		将行号用作列名，且是数据的开头。注意当skip_blank_lines=True时，这个参数忽略注释行和空行。所以header=0表示第一行是数据而不是文件的第一行。
	parse_dates ：
		布尔类型值 or int类型值的列表 or 列表的列表 or 字典（默认值为 FALSE）
			TRUE：则尝试解析索引
			由int类型值组成的列表(例子 [1,2,3]):作为单独数据列，分别解析原始文件中的1,2,3列
			由列表组成的列表（例子[[1,3]]）:将1,3列合并，作为一个单列进行解析
			字典（例子{‘foo’：[1, 3]}）:解析1,3列作为数据，并命名为foo
	index_col:
		int类型值，序列，FALSE（默认 None）
		将真实的某列当做index（列的数目，甚至列名）
	
### 当 header = None
即指明原始文件数据没有列索引，这样read_csv为自动加上列索引，除非你给定列索引的名字。
{% codeblock %}

obj_2=pd.read_csv('f:/ceshi.csv',header=None,names=range(2,5))
print obj_2

    2   3   4
0  c1  c2  c3
1   0   5  10
2   1   6  11
3   2   7  12
4   3   8  13
5   4   9  14

{% endcodeblock %}
### header=0
表示文件第0行（即第一行，索引从0开始）为列索引，这样加names会替换原来的列索引。
{% codeblock %}

obj_2=pd.read_csv('f:/ceshi.csv',header=0,names=range(2,5))
print obj_2

   2  3   4
0  0  5  10
1  1  6  11
2  2  7  12
3  3  8  13
4  4  9  14

{% endcodeblock %}
### index_col用法
{% codeblock %}

obj_2=pd.read_csv('ceshi.csv',index_col=0)
print obj_2

   c1  c2  c3
a   0   5  10
b   1   6  11
c   2   7  12
d   3   8  13
e   4   9  14

obj_2=pd.read_csv('ceshi.csv',index_col=[0,2])
print obj_2

     c1  c3
  c2        
a 5    0  10
b 6    1  11
c 7    2  12
d 8    3  13
e 9    4  14

{% endcodeblock %}
index_col为指定数据中那一列作为Dataframe的行索引，也可以可指定多列，形成层次索引，默认为None,即不指定行索引，这样系统会自动加上行索引。








