---
title: The tips of octave
date: 2018-04-20 09:36:50
categories:
- octave
tags:
- paint
- octave
- tutorial
---
Here is tips of octave.
 <!-- more -->
### 1
{% codeblock %}
A = [1 2;1 4]
	A = [
		1 2
		1 4
	]
A = (A == 1)
	A = [
		1 0
		1 0
	]
	
A = [1 1 3;2 3 1;1 1 4]
	A =

		1   1   3
		2   3   1
		1   1   4
for i = 1:3,
	A(:,i) = (A(:,i)==i);
end;

	A =

		1   0   1
		0   0   0
		1   0   0
{% endcodeblock %}
### 2

