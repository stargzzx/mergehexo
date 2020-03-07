---
title: python - pandas
date: 2018-05-23 13:56:54
categories:
- python
tags:
- python
- tutorial
- pandas
---
Here is the tutorial of python's pandas.

<!-- more -->

## 基本属性

{% codeblock %}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# pandas 类似于字典

#创建
s = pd.Series([1,3,6,np.nan,44,1])
print(s)
#0     1.0
#1     3.0
#2     6.0
#3     NaN
#4    44.0
#5     1.0
#dtype: float64
{% endcodeblock %}

## 基本操作

{% codeblock %}
#索引自定义
dates = pd.date_range('20180501',periods=6) #从 20180501 开始生成六天
print(dates)
#DatetimeIndex(['2018-05-01', '2018-05-02', '2018-05-03', '2018-05-04',
#               '2018-05-05', '2018-05-06'],
#              dtype='datetime64[ns]', freq='D')
df = pd.DataFrame(np.random.randn(6,4),index=dates)
print(df)
#             0          1           2        3
#2018-05-01 -0.794152  0.049826  0.173014 -0.237818
#2018-05-02  0.781276 -0.405538 -1.102965 -0.691218
#2018-05-03 -1.108763  2.705587 -1.308696  1.326732
#2018-05-04  0.453428 -0.004210 -0.113948  0.197456
#2018-05-05  0.557447 -0.098532  1.619433  0.292091
#2018-05-06  0.400914  0.288742  2.705538 -0.712801
df = pd.DataFrame(np.random.randn(6,4),index=dates,columns=['a','b','c','d'])
print(df)
#                   a         b         c         d
#2018-05-01 -0.624222  1.123151  0.611911 -0.295465
#2018-05-02 -0.388773 -0.902031  0.360797 -0.828147
#2018-05-03 -0.887488  0.234514  1.534400 -0.047077
#2018-05-04  0.096631  0.762920  1.200034  0.393583
#2018-05-05 -0.286951 -0.733449  0.656634 -1.417360
#2018-05-06 -1.442686  0.721030 -0.056592  0.728287

df = pd.DataFrame({
    'A':1,
    'B':pd.Timestamp('20180522'),
    'C':'foo'
},index=[0,1,2])
print(df)
#   A          B    C
#0  1 2018-05-22  foo
#1  1 2018-05-22  foo
#2  1 2018-05-22  foo
print(df.dtypes) #查看数据类型
#   A          B    C
#0  1 2018-05-22  foo
#1  1 2018-05-22  foo
#2  1 2018-05-22  foo
#A             int64
#B    datetime64[ns]
#C            object
#dtype: object
print(df.index) #Int64Index([0, 1, 2], dtype='int64')
print(df.columns) #Index(['A', 'B', 'C'], dtype='object')
print(df.values)
#[[1 Timestamp('2018-05-22 00:00:00') 'foo']
#[1 Timestamp('2018-05-22 00:00:00') 'foo']
#[1 Timestamp('2018-05-22 00:00:00') 'foo']]
print(df.describe())  #只会运算一下数字类型的数据
#         A
#count  3.0
#mean   1.0
#std    0.0
#min    1.0
#25%    1.0
#50%    1.0
#75%    1.0
#max    1.0
print(df.T)   #类似于矩阵的转置
#                     0                    1                    2
#A                    1                    1                    1
#B  2018-05-22 00:00:00  2018-05-22 00:00:00  2018-05-22 00:00:00
#C                  foo                  foo                  foo
print(df.sort_index(axis=1))  #排序 axis 代表按列还是行排序
#   A          B    C
#0  1 2018-05-22  foo
#1  1 2018-05-22  foo
#2  1 2018-05-22  foo
print(df.sort_index(axis=1,ascending=False))  #倒排序 axis 代表按列还是行排序
#     C          B  A
#0  foo 2018-05-22  1
#1  foo 2018-05-22  1
#2  foo 2018-05-22  1
print(df.sort_values(by = 'A')) #按照值排序
#   A          B    C
#0  1 2018-05-22  foo
#1  1 2018-05-22  foo
#2  1 2018-05-22  foo
{% endcodeblock %}
## 选取数据
{% codeblock %}
dates = pd.date_range('20180522',periods=6)
df    = pd.DataFrame(np.arange(24).reshape((6,4)),index=dates,columns=['A','B','C','D'])

print(df['A'])
#2018-05-22     0
#2018-05-23     4
#2018-05-24     8
#2018-05-25    12
#2018-05-26    16
#2018-05-27    20
#Freq: D, Name: A, dtype: int32
print(df.A) #和上面一样
print(df[0:3])
#            A  B   C   D
#2018-05-22  0  1   2   3
#2018-05-23  4  5   6   7
#2018-05-24  8  9  10  11
print(df.loc[             # loc 按标签筛选
    '20180523'
      ])
#Name: 2018-05-23 00:00:00, dtype: int32
print(df.loc[:,['A','B']])
#             A   B
#2018-05-22   0   1
#2018-05-23   4   5
#2018-05-24   8   9
#2018-05-25  12  13
#2018-05-26  16  17
#2018-05-27  20  21
print(df.loc['20180522',['A']])
#A    0
#Name: 2018-05-22 00:00:00, dtype: int32
print(df.iloc[3])         # iloc 按行列数筛选
#A    12
#B    13
#C    14
#D    15
#Name: 2018-05-25 00:00:00, dtype: int32
print(df.iloc[3:5,1:3])
#             B   C
#2018-05-25  13  14
#2018-05-26  17  18
print(df.iloc[[1,2,4],1:3])
#             B   C
#2018-05-23   5   6
#2018-05-24   9  10
#2018-05-26  17  18
print(df.ix[:3,['A','C']])      # ix 是 loc 和 iloc 的结合体
#            A   C
#2018-05-22  0   2
#2018-05-23  4   6
#2018-05-24  8  10
print(df[df.A > 8])
#             A   B   C   D
#2018-05-25  12  13  14  15
#2018-05-26  16  17  18  19
#2018-05-27  20  21  22  23
{% endcodeblock %}
## 设置值
{% codeblock %}
dates = pd.date_range('20180522',periods=6)
df    = pd.DataFrame(np.arange(24).reshape((6,4)),index=dates,columns=['A','B','C','D'])

df.iloc[2,2] = 1111  #序列改
print(df)
#             A   B     C   D
#2018-05-22   0   1     2   3
#2018-05-23   4   5     6   7
#2018-05-24   8   9  1111  11
#2018-05-25  12  13    14  15
#2018-05-26  16  17    18  19
#2018-05-27  20  21    22  23
df.loc['20180522','C'] = 222  #值改
df.A[df.A > 4] = 0
print(df)
#            A   B     C   D
#2018-05-22  0   1   222   3
#2018-05-23  4   5     6   7
#2018-05-24  0   9  1111  11
#2018-05-25  0  13    14  15
#2018-05-26  0  17    18  19
#2018-05-27  0  21    22  23
df['F'] = np.nan
print(df)
#            A   B     C   D   F
#2018-05-22  0   1   222   3 NaN
#2018-05-23  4   5     6   7 NaN
#2018-05-24  0   9  1111  11 NaN
#2018-05-25  0  13    14  15 NaN
#2018-05-26  0  17    18  19 NaN
#2018-05-27  0  21    22  23 NaN
{% endcodeblock %}
## 处理丢失数据
{% codeblock %}
dates = pd.date_range('20180522',periods=6)
df    = pd.DataFrame(np.arange(24).reshape((6,4)),index=dates,columns=['A','B','C','D'])
df.iloc[0,1] = np.nan
df.iloc[1,2] = np.nan
print(df)
#             A     B     C   D
#2018-05-22   0   NaN   2.0   3
#2018-05-23   4   5.0   NaN   7
#2018-05-24   8   9.0  10.0  11
#2018-05-25  12  13.0  14.0  15
#2018-05-26  16  17.0  18.0  19
#2018-05-27  20  21.0  22.0  23
print(df.dropna(axis = 0,how='any')) #丢掉 nan 值，axis 选择丢掉行还是列 how 有 any 和 all 。any ：只要有一个nan就丢掉 all 必须行或列都是 nan 才丢掉
#             A     B     C   D
#2018-05-24   8   9.0  10.0  11
#2018-05-25  12  13.0  14.0  15
#2018-05-26  16  17.0  18.0  19
#2018-05-27  20  21.0  22.0  23
print(df.fillna(value = 0)) #对 nan 进行填充
#             A     B     C   D
#2018-05-22   0   0.0   2.0   3
#2018-05-23   4   5.0   0.0   7
#2018-05-24   8   9.0  10.0  11
#2018-05-25  12  13.0  14.0  15
#2018-05-26  16  17.0  18.0  19
#2018-05-27  20  21.0  22.0  23
print(df.isnull())
#                A      B      C      D
#2018-05-22  False   True  False  False
#2018-05-23  False  False   True  False
#2018-05-24  False  False  False  False
#2018-05-25  False  False  False  False
#2018-05-26  False  False  False  False
#2018-05-27  False  False  False  False
print(np.any(df.isnull()) == True) #判断里面是否有 nan
#True
{% endcodeblock %}
## 导入导出
{% codeblock %}
#支持导入导出的文件格式
# csv excel hdf sql json msgpack html gbq stata clipboard pickle
# csv 也是表格，可以直接用 excel 保存
data = pd.read_csv('name') #同理，读 hdf 就用 read_hdf()
data 默认加索引，默认用 0 - N
data.to_pickle('name.pickle') #保存
{% endcodeblock %}
## 合并concatenating
{% codeblock %}
df1 = pd.DataFrame(np.ones((3,4)) * 0,columns=['A','B','C','D'])
df2 = pd.DataFrame(np.ones((3,4)) * 1,columns=['A','B','C','D'])
df3 = pd.DataFrame(np.ones((3,4)) * 2,columns=['A','B','C','D'])
res = pd.concat([df1,df2,df3],axis = 0)
print(res)
#     A    B    C    D
#0  0.0  0.0  0.0  0.0
#1  0.0  0.0  0.0  0.0
#2  0.0  0.0  0.0  0.0
#0  1.0  1.0  1.0  1.0
#1  1.0  1.0  1.0  1.0
#2  1.0  1.0  1.0  1.0
#0  2.0  2.0  2.0  2.0
#1  2.0  2.0  2.0  2.0
#2  2.0  2.0  2.0  2.0
res = pd.concat([df1,df2,df3],axis = 0,ignore_index = True)
print(res)
#     A    B    C    D
#0  0.0  0.0  0.0  0.0
#1  0.0  0.0  0.0  0.0
#2  0.0  0.0  0.0  0.0
#3  1.0  1.0  1.0  1.0
#4  1.0  1.0  1.0  1.0
#5  1.0  1.0  1.0  1.0
#6  2.0  2.0  2.0  2.0
#7  2.0  2.0  2.0  2.0
#8  2.0  2.0  2.0  2.0
#数据标识不符相合并
df1 = pd.DataFrame(np.ones((3,4)) * 0,columns=['A','B','C','D'],index = [1,2,3])
df2 = pd.DataFrame(np.ones((3,4)) * 1,columns=['B','C','D','E'],index = [2,3,4])
res = pd.concat([df1,df2]) #默认 join 为outer
print(res)
#     A    B    C    D    E
#1  0.0  0.0  0.0  0.0  NaN
#2  0.0  0.0  0.0  0.0  NaN
#3  0.0  0.0  0.0  0.0  NaN
#2  NaN  1.0  1.0  1.0  1.0
#3  NaN  1.0  1.0  1.0  1.0
#4  NaN  1.0  1.0  1.0  1.0
res = pd.concat([df1,df2],join='inner')
print(res)
#     B    C    D
#1  0.0  0.0  0.0
#2  0.0  0.0  0.0
#3  0.0  0.0  0.0
#2  1.0  1.0  1.0
#3  1.0  1.0  1.0
#4  1.0  1.0  1.0
res = pd.concat([df1,df2],join='inner',ignore_index = True)
print(res)
#     B    C    D
#0  0.0  0.0  0.0
#1  0.0  0.0  0.0
#2  0.0  0.0  0.0
#3  1.0  1.0  1.0
#4  1.0  1.0  1.0
#5  1.0  1.0  1.0
res = pd.concat([df1,df2],axis=1,join_axes=[df1.index])
print(res)
#     A    B    C    D    B    C    D    E
#1  0.0  0.0  0.0  0.0  NaN  NaN  NaN  NaN
#2  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
#3  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
res = pd.concat([df1,df2],axis=1)
print(res)
#     A    B    C    D    B    C    D    E
#1  0.0  0.0  0.0  0.0  NaN  NaN  NaN  NaN
#2  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
#3  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
#4  NaN  NaN  NaN  NaN  1.0  1.0  1.0  1.0
df1 = pd.DataFrame(np.ones((3,4)) * 0,columns=['A','B','C','D'])
df2 = pd.DataFrame(np.ones((3,4)) * 1,columns=['A','B','C','D'])
df3 = pd.DataFrame(np.ones((3,4)) * 2,columns=['B','C','D','E'],index=[2,3,4])
res = df1.append(df2,ignore_index=True)
print(res)
#     A    B    C    D
#0  0.0  0.0  0.0  0.0
#1  0.0  0.0  0.0  0.0
#2  0.0  0.0  0.0  0.0
#3  1.0  1.0  1.0  1.0
#4  1.0  1.0  1.0  1.0
#5  1.0  1.0  1.0  1.0
res = df1.append([df2,df3])
print(res)
#     A    B    C    D    E
#0  0.0  0.0  0.0  0.0  NaN
#1  0.0  0.0  0.0  0.0  NaN
#2  0.0  0.0  0.0  0.0  NaN
#0  1.0  1.0  1.0  1.0  NaN
#1  1.0  1.0  1.0  1.0  NaN
#2  1.0  1.0  1.0  1.0  NaN
#2  NaN  2.0  2.0  2.0  2.0
#3  NaN  2.0  2.0  2.0  2.0
#4  NaN  2.0  2.0  2.0  2.0
s1 = pd.Series([1,2,3,4],index=['A','B','C','D'])
print(df1)
#     A    B    C    D
#0  0.0  0.0  0.0  0.0
#1  0.0  0.0  0.0  0.0
#2  0.0  0.0  0.0  0.0
res = df1.append(s1,ignore_index=True)
print(res)
#     A    B    C    D
#0  0.0  0.0  0.0  0.0
#1  0.0  0.0  0.0  0.0
#2  0.0  0.0  0.0  0.0
#3  1.0  2.0  3.0  4.0
{% endcodeblock %}
## 合并升级 merge
{% codeblock %}
left = pd.DataFrame(
    {
        'key' : ['K0','K1','K2','K3'],
        'A'   : ['A0','A1','A2','A3'],
        'B'   : ['B0','B1','B2','B3']
    }
)
right = pd.DataFrame(
    {
        'key' : ['K0','K1','K2','K3'],
        'C'   : ['C0','C1','C2','C3'],
        'D'   : ['D0','D1','D2','D3']
    }
)
print(left)
#    A   B key
#0  A0  B0  K0
#1  A1  B1  K1
#2  A2  B2  K2
#3  A3  B3  K3
res = pd.merge(left,right,on='key')
print(res)
#    A   B key   C   D
#0  A0  B0  K0  C0  D0
#1  A1  B1  K1  C1  D1
#2  A2  B2  K2  C2  D2
#3  A3  B3  K3  C3  D3



left = pd.DataFrame(
    {
        'key1' : ['K0','K2','K2','K3'],
        'key2' : ['K0','K0','K2','K3'],
        'A'   : ['A0','A1','A2','A3'],
        'B'   : ['B0','B1','B2','B3']
    }
)
right = pd.DataFrame(
    {
        'key1' : ['K0','K1','K2','K3'],
        'key2' : ['K0','K1','K1','K3'],
        'C'   : ['C0','C1','C2','C3'],
        'D'   : ['D0','D1','D2','D3']
    }
)
print(left)
#    A   B key1 key2
#0  A0  B0   K0   K0
#1  A1  B1   K2   K0
#2  A2  B2   K2   K2
#3  A3  B3   K3   K3
res = pd.merge(left,right,on = ['key1','key2']) #默认 how = inner,一共有四种方式 left right outer inner
print(res)
#    A   B key1 key2   C   D
#0  A0  B0   K0   K0  C0  D0
#1  A3  B3   K3   K3  C3  D3
res = pd.merge(left,right,on = ['key1','key2'],how = 'outer') #默认 how = inner
print(res)
#     A    B key1 key2    C    D
#0   A0   B0   K0   K0   C0   D0
#1   A1   B1   K2   K0  NaN  NaN
#2   A2   B2   K2   K2  NaN  NaN
#3   A3   B3   K3   K3   C3   D3
#4  NaN  NaN   K1   K1   C1   D1
#5  NaN  NaN   K2   K1   C2   D2
res = pd.merge(left,right,on = ['key1','key2'],how = 'left') #默认 how = inner
print(res)
#    A   B key1 key2    C    D
#0  A0  B0   K0   K0   C0   D0
#1  A1  B1   K2   K0  NaN  NaN
#2  A2  B2   K2   K2  NaN  NaN
#3  A3  B3   K3   K3   C3   D3



df1 = pd.DataFrame(
    {
        'col1'     : [0,1],
        'col_left' : ['a','b']
    }
)
df2 = pd.DataFrame(
    {
        'col1'     : [1,1,2],
        'col_right' : [2,2,2]
    }
)
res = pd.merge(df1,df2,on='col1',how='outer',indicator=True)
print(res)
#   col1 col_left  col_right      _merge
#0     0        a        NaN   left_only
#1     1        b        2.0        both
#2     1        b        2.0        both
#3     2      NaN        2.0  right_only
res = pd.merge(df1,df2,on='col1',how='outer',indicator='licong')
print(res)
#   col1 col_left  col_right      licong
#0     0        a        NaN   left_only
#1     1        b        2.0        both
#2     1        b        2.0        both
#3     2      NaN        2.0  right_only



df1 = pd.DataFrame(
    {
        'A' : ['A0','A1','A2'],
        'B' : ['B0','B1','B2']
    },
    index=['K0','K1','K2']
)
df2 = pd.DataFrame(
    {
        'C' : ['C0','C1','C2'],
        'D' : ['D0','D1','D2']
    },
index=['K0','K2','K3']
)
print(df1)
#     A   B
#K0  A0  B0
#K1  A1  B1
#K2  A2  B2
res = pd.merge(df1,df2,left_index=True,right_index=True,how='outer')
print(res)
#      A    B    C    D
#K0   A0   B0   C0   D0
#K1   A1   B1  NaN  NaN
#K2   A2   B2   C1   D1
#K3  NaN  NaN   C2   D2
res = pd.merge(df1,df2,left_index=True,right_index=True,how='inner')
print(res)
#     A   B   C   D
#K0  A0  B0  C0  D0
#K2  A2  B2  C1  D1


boys = pd.DataFrame(
    {
        'K' : ['K0','K1','K2'],
        'age' : [1,2,3]
    }
)
girls = pd.DataFrame(
    {
        'K' : ['K0','K1','K2'],
        'age' : [4,5,6]
    }
)
res = pd.merge(boys,girls,on='K',suffixes=['_boy','_girl'],how='inner')
print(res)
#    K  age_boy  age_girl
#0  K0        1         4
#1  K1        2         5
#2  K2        3         6
res = pd.merge(boys,girls,on='K',suffixes=['_boy','_girl'],how='outer')
print(res)
#    K  age_boy  age_girl
#0  K0        1         4
#1  K1        2         5
#2  K2        3         6
{% endcodeblock %}

{% img /images/python/1.jpg %}