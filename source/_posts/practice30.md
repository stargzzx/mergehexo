---
title: 双栈排序
date: 2018-07-31 21:01:54
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：
一个栈中元素类型为整型，现在想将该栈从顶到底按从大到小排序，只许申请一个栈，除此之外可以申请新的变量，但不能申请额外的数据结构，如何完成排序？
<!-- more -->
## 原理
其实很简单，建立一个辅助栈，当辅助栈为空的时候直接将数据栈的栈顶压入辅助栈中。
如果不是空，则比较数据栈的栈顶和辅助栈的栈顶，如果小于辅助栈的栈顶，则将数据栈的栈顶压入辅助栈中。如果大于，则将辅助栈大于数据栈栈顶元素压入数据栈中，直到辅助栈为空或者辅助栈的栈顶大于数据栈的栈顶。
## 代码
{% codeblock %}

def sort(stack,top):
	# 辅助栈
    tmp_stack = [None for k in range(len(stack))]
    # 辅助栈的栈顶
	tmp_top = -1
	# 当数据栈不是空的时候
    while(top is not -1):
		# 当辅助栈为空的时候
        if tmp_top is -1:
            tmp_top += 1
            tmp_stack[tmp_top] = stack[top]
            top -= 1
		# 当辅助栈不为空的时候
        else:
            last = stack[top]
            top -= 1
			# 如果数据栈的栈顶大于辅助栈的栈顶
            if last > tmp_stack[tmp_top]:
                while(True):
					# 当辅助栈为空
                    if tmp_top is -1:
                        break
					# 当数据栈的栈顶小于辅助栈的栈顶
                    if last <= tmp_stack[tmp_top]:
                        break
                    top += 1
                    stack[top] = tmp_stack[tmp_top]
                    tmp_top -= 1
                tmp_top += 1
                tmp_stack[tmp_top] = last
			# 如果数据栈的栈顶小于或等于辅助栈的栈顶
            else:
                tmp_top += 1
                tmp_stack[tmp_top] = last
    print(tmp_stack)



stack = [5,1,3,4,2,1]
top = 5
sort(stack,top)

{% endcodeblock %}
