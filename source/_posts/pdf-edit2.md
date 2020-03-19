---
title: pdf_edit | 使用 google 翻译接口
date: 2020-03-18 21:37:20
categories:
- pdf_edit
tags:
- pdf_edit
---
这一章的任务就是使用 google 翻译的接口然后翻译。

之所以先用谷歌，我想但凡使用过的人都懂。

<!-- more -->

<br/>

# 参考资料

<br/>

[Python 实现自动化翻译和替换的脚本](https://inspurer.github.io/2019/04/08/Python-%E5%AE%9E%E7%8E%B0%E8%87%AA%E5%8A%A8%E5%8C%96%E7%BF%BB%E8%AF%91%E5%92%8C%E6%9B%BF%E6%8D%A2%E7%9A%84%E8%84%9A%E6%9C%AC/)
[Google TK参数](https://github.com/cocoa520/Google_TK)

<br/>

# 过程

<br/>

## 使用 Googletrans 轮子

[py-googletrans](https://github.com/ssut/py-googletrans)

我在网上搜索的时候，发现已经有大神写好了使用谷歌翻译接口的库，但是，在我使用的时候，总是报错。

因为，还有其他方案可以选择，于是，就没有再进行深究，这个方案放弃。

## 使用 google 的原生接口

谷歌翻译爬虫

通过浏览器 F12 开发者工具，很容易定位到翻译请求的 URL: 

	http://translate.google.cn/translate_a/single?client=t&sl=en&tl=zh-CN&hl=zh-CN&dt=at&dt=bd&dt=ex&dt=ld&dt=md&dt=qca&dt=rw&dt=rm&dt=ss &dt=t&ie=UTF-8&oe=UTF-8&clearbtn=1&otf=1&pc=1&srcrom=0&ssel=0&tsel=0&kc=2

这个请求接受两个参数，一个就是我们要翻译的字符串 q，另一个是用于用户认证的 tk(token),其中 q 很容易构造，tk 的构造就需要花费一番心力了，需要我们调试 js 代码，这里参考 Github 上大神的轮子： https://github.com/cocoa520/Google_TK

关于如何获取 tk 值，在这里，我附上我改造后的代码。

- translation.py

```python
import execjs


class Py4Js():

    def __init__(self):
        self.ctx = execjs.compile(""" 
        function VL(a) {
        var b = a.trim();
        return TL(b);
        }
        
        function TL(a) {
            var k = "";
            var b = 406644;
            var b1 = 3293161072;
            
            var jd = ".";
            var $b = "+-a^+6";
            var Zb = "+-3^+b+-f";
        
            for (var e = [], f = 0, g = 0; g < a.length; g++) {
                var m = a.charCodeAt(g);
                128 > m ? e[f++] = m : (2048 > m ? e[f++] = m >> 6 | 192 : (55296 == (m & 64512) && g + 1 < a.length && 56320 == (a.charCodeAt(g + 1) & 64512) ? (m = 65536 + ((m & 1023) << 10) + (a.charCodeAt(++g) & 1023),
                e[f++] = m >> 18 | 240,
                e[f++] = m >> 12 & 63 | 128) : e[f++] = m >> 12 | 224,
                e[f++] = m >> 6 & 63 | 128),
                e[f++] = m & 63 | 128)
            }
            a = b;
            for (f = 0; f < e.length; f++) a += e[f],
            a = RL(a, $b);
            a = RL(a, Zb);
            a ^= b1 || 0;
            0 > a && (a = (a & 2147483647) + 2147483648);
            a %= 1E6;
            return a.toString() + jd + (a ^ b)
        };
        
        function RL(a, b) {
            var t = "a";
            var Yb = "+";
            for (var c = 0; c < b.length - 2; c += 3) {
                var d = b.charAt(c + 2),
                d = d >= t ? d.charCodeAt(0) - 87 : Number(d),
                d = b.charAt(c + 1) == Yb ? a >>> d: a << d;
                a = b.charAt(c) == Yb ? a + d & 4294967295 : a ^ d
            }
            return a
        }
    """)

    def getTk(self, text):
        return self.ctx.call("VL", text)
```

在这里我们用到了 execjs 这个库，关于它的用法可以参考我下面的博文。

[python | PyExecJS](https://benpaodewoniu.github.io/2020/03/19/python85/)

进行调用的代码如下：

- main.py

```python
from translation import Py4Js
import requests


def translate(tk, content):
    if len(content) > 4891:
        print("翻译的长度超过限制！！！")
        return

    param = {'tk': tk, 'q': content}

    result = requests.get("""http://translate.google.cn/translate_a/single?client=t&sl=en
        &tl=zh-CN&hl=zh-CN&dt=at&dt=bd&dt=ex&dt=ld&dt=md&dt=qca&dt=rw&dt=rm&dt=ss
        &dt=t&ie=UTF-8&oe=UTF-8&clearbtn=1&otf=1&pc=1&srcrom=0&ssel=0&tsel=0&kc=2""", params=param)

    data = result.json()
    print(data[0][0][0])


p = Py4Js()
tk = p.getTk('i love you')
translate(tk, 'i love you')
```