---
title:  Tim | 一个更改内容的简单脚本
date: 2020-04-17 16:06:07
categories:
- English
- Tim
tags:
- Tim
---
这个是将人名标上颜色的脚本。

<!-- more -->

```python
f_p = "./test.md"
f = open(f_p, 'r')
alllines = f.readlines()
f.close()

name = False

f = open(f_p, 'w+')
for eachline in alllines:
    if "<!-- more -->" in eachline:
        f.writelines(eachline)
        name = True
        continue
    if name and eachline.strip():
        if ':' in eachline:
            path = eachline.split(':')
            eachline = f"<b style = 'color:red;'>{path[0]}:</b>{path[1]}"
            f.writelines(eachline)
            f.writelines('\n')
        else:
            f.writelines(eachline)
    else:
        f.writelines(eachline)
f.close()
```
