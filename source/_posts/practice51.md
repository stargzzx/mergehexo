---
title: leetcode 7
date: 2019-02-20 13:04:45
categories:
- practice
tags:
- leetcode
- practice
- algorithm
---
这是刷的第 7 个 leetcode 题。
关于字典的优秀应用。非常好的思想。
<!-- more -->
## 介绍
Title：
Valid Parentheses
Acceptance：
35.8%
Difficulty:
Easy
[地址](https://leetcode.com/problems/valid-parentheses/)
解决语言：
python3

## Description
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.
An input string is valid if:
Open brackets must be closed by the same type of brackets.
Open brackets must be closed in the correct order.
Note that an empty string is also considered valid.
Example 1:

	Input: "()"
	Output: true
	
Example 2:

	Input: "()[]{}"
	Output: true

Example 3:

	Input: "(]"
	Output: false
	
Example 4:

	Input: "([)]"
	Output: false
	
Example 5:

	Input: "{[]}"
	Output: true
	
## code
{% codeblock %}

class Solution:
    def isValid(self, s: 'str') -> 'bool':
        i = 0
        tmp = []
        if(len(s) == 0):
            return True
        if(len(s) == 1):
            return False
        for j in range(len(s)):
            if(s[j] == '(' or s[j] == '[' or s[j] == '{'):
                tmp.append(s[j])
                i += 1
            else:
                if(s[j] == ')'):
                    if(len(tmp) == 0 or tmp[i - 1] != '(' ):
                        return False
                    else:
                        i -= 1
                        tmp.pop(i)
                elif(s[j] == ']'):
                    if (len(tmp) == 0 or tmp[i - 1] != '['):
                        return False
                    else:
                        i -= 1
                        tmp.pop(i)
                else:
                    if (len(tmp) == 0 or tmp[i - 1] != '{'):
                        return False
                    else:
                        i -= 1
                        tmp.pop(i)
        if(len(tmp) == 0):
            return True
        else:
            return False
			
{% endcodeblock %}
这里值得思考的是 or 和 and
拿 or 来说，如果 A or B 的话，如果 A 是 True 的话，那么 A or B 就是 True，而不会去计算 B ,所以，在某种程度下 A 和 B 的顺序很重要。
贴一下别人写的代码
{% codeblock %}


JeremieMelo's avatar
JeremieMelo
2
July 6, 2017 11:14 AM

169 VIEWS

class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        stack = []
        length = len(s)
		# 这一步挺厉害的，只是一句 if 就把，奇数 和长度为 0 的情况都考虑了
        if(length % 2):
            return False
        
        for c in s:
            if(not stack):
                stack.append(c)
				# 这个 continue 也很精髓，提升了不少性能
                continue
            top = stack[-1]
            if(top == '('):
                if(c == ')'):
                    stack.pop()       
                else:
                    stack.append(c)
            elif(top == '['):
                if(c == ']'):
                    stack.pop()       
                else:
                    stack.append(c)
            elif(top == '{'):
                if(c == '}'):
                    stack.pop()       
                else:
                    stack.append(c)
            else:
                return False
        if(not stack):
            return True
        else:
            return False
		
{% endcodeblock %}
下面这个代码更加优秀。
它熟练地运用了字典的性质。
{% codeblock %}


JeremieMelo's avatar
JeremieMelo
2
July 6, 2017 11:14 AM

169 VIEWS

def isValid(self, s):
    stack, match = [], {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in match:
            if not (stack and stack.pop() == match[ch]):
                return False
        else:
            stack.append(ch)
    return not stack
		
{% endcodeblock %}
首先说明一下字典的一些概念

	m = {')': '(', ']': '[', '}': '{'}
	for i in m:
		print(i)
			# ) } ]
			
## Result
Runtime: 36 ms, faster than 84.38% of Python3 online submissions for Valid Parentheses.
Memory Usage: 12.5 MB, less than 58.33% of Python3 online submissions for Valid Parentheses.









