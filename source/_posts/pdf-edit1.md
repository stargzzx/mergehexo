---
title: pdf_edit | 转换后格式不变
date: 2020-03-18 20:28:28
categories:
- pdf_edit
tags:
- pdf_edit
---
首先，这个项目我先解决的是，经过转换后格式不变的问题。

这一节所用的库是 PyMuPDF。

关于这个库的使用，你可以看我下面的博文。

[python | PyMuPDF](https://benpaodewoniu.github.io/2020/03/18/pdf-edit1/)

<!-- more -->

首先，我们要先确认是否能有效的识别各个文本、图像的坐标。

这里用的是

	text = page.getText('dict')

写了一个工具方法:

```python
# 根据坐标画出某一页的坐标分布图
def paint_rect_in_pdf(page, deal_type):
    '''
    :param page:
    :param deal_type: ['dict','blocks']
    :return:
    '''
    positions = []
    data = page.getText(deal_type)
    if deal_type == 'dict':
        for t in data['blocks']:
            positions.append(t['bbox'])
    elif deal_type == 'blocks':
        for t in data:
            positions.append((t[0], t[1], t[2], t[3]))
    paint_rect(page, positions)

# 根据坐标画图
def paint_rect(page, positions):
    pix = page.getPixmap()
    mode = "RGBA" if pix.alpha else "RGB"
    img = Image.frombytes(mode, [pix.width, pix.height], pix.samples)
    draw = ImageDraw.Draw(img)
    for p in positions:
        draw.rectangle(p, fill=None, outline='red')
    img.show()
```

调用

```python
doc = fitz.open('./test.pdf')
page = doc[2]
paint_rect_in_pdf(page, 'dict')
```

其效果如下图所示（红框就是我们的坐标，看起来是非常的好）

![](/images/pdf_edit/1_0.png)