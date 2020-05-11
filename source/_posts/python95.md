---
title: python | selenium 在不同机器上的运行情况
date: 2020-05-11 17:11:28
categories:
- python
- 模块
- selenium
tags:
- python
- selenium
---
目前，我已经在下面的机器成功运行起来了。

- MacBook
- ubuntu 无界面版

浏览器使用的是 `chrome`。

<!-- more -->

<br/>

# MacBook

<br/>

先说一下配置。

在 MacBook 中，我的默认浏览器是 `chrome`。

然后我 chrome 的版本是

	81.0.4044.138

按照，下面的网址，我们选择 chrome 的驱动程序。

[ChromeDriver - WebDriver for Chrome](https://sites.google.com/a/chromium.org/chromedriver/downloads)

下面我就我实际情况说一下。

## 情况一

上面说了，我的 chrome 版本是

	81.0.4044.138

但是，我下载 driver 的时候下载错了，下载了这个版本

	81.0.4044.69

最后，将 driver 放在 `/usr/bin` 中。

关于，如何放置进去，请参考我下面的文章。

[eosvoter | 爬取矿池数据](https://benpaodewoniu.github.io/2020/05/08/eosvoter14/)

我们执行

	chromedriver

输出

	Starting ChromeDriver 81.0.4044.138 (8c6c7ba89cc9453625af54f11fd83179e23450fa-refs/branch-heads/4044@{#999}) on port 9515
	Only local connections are allowed.
	Please protect ports used by ChromeDriver and related test frameworks to prevent access by malicious code.

其中

	Please protect ports used by ChromeDriver and related test frameworks to prevent access by malicious code.

这句话，网上流传的说法是驱动和浏览器版本不一致导致的，但是，当时，我还不知道这件事，我就直接运行了代码，代码是可以正常运行的，当然，我也就是用驱动抓一下页面数据，然后爬一下数据而已，不涉及更高级的伪人操作。

## 情况二

当我得知，版本不一致出现的这句话的时候，我就把驱动换成了

	81.0.4044.138

结果，执行的时候还是

	Starting ChromeDriver 81.0.4044.138 (8c6c7ba89cc9453625af54f11fd83179e23450fa-refs/branch-heads/4044@{#999}) on port 9515
	Only local connections are allowed.
	Please protect ports used by ChromeDriver and related test frameworks to prevent access by malicious code.

但是，依然不影响我的代码操作浏览器。

相关代码

```python
browser = webdriver.Chrome()
# 获取全网数据
net_info = dict()
browser.get(root_url)
response = browser.page_source
pool_tables = BeautifulSoup(response, "html.parser", from_encoding="utf-8").find_all('div', class_="panel-body")
pool_li = pool_tables[2].find_all('ul')[0].find_all('li')
...
browser.close()
```

可以看出，在 MacBook 上运行是没有设置浏览器参数的。

<br/>

# ubuntu

<br/>

先说一下我接手的 ubuntu 的配置。

其不带图形化界面。

我先安装了 chrome 浏览器。

将其放在 `/usr/bin` 中，使用

	$ google-chrome --version
	Google Chrome 81.0.4044.138 

于是我就下载了相关的驱动，将其放在 `/usr/bin` 中

执行

	$ chromedriver

输出

	Starting ChromeDriver 81.0.4044.138 (8c6c7ba89cc9453625af54f11fd83179e23450fa-refs/branch-heads/4044@{#999}) on port 9515
	Only local connections are allowed.
	Please protect ports used by ChromeDriver and related test frameworks to prevent access by malicious code.
	[1589190010.728][SEVERE]: bind() failed: 无法指定被请求的地址 (99)

呃呃呃，多出了一个

	[1589190010.728][SEVERE]: bind() failed: 无法指定被请求的地址 (99)

我找遍全网没有找到合适的解决方案，但是，我个人认为，出现这个，并不影响出数据，「我没有进行过高级的伪人操作」。（之所以不影响出数据，是因为，我在后面确实可以出数据）

网上的一个解决方案是这样执行

	chromedriver --whitelisted-ips

但是，没说为什么这么执行。

再说一个疑问点，我使用安装包安装了 chrome，但是，在 `/usr/bin` 出现了两个 chrome.

- google-chrome
- google-chrome-stable

我使用，下面的命令查看安装的软件

	dpkg -l | grep chrome

出现了

	google-chrome-stable                        81.0.4044.138-1                              amd64        The web browser from Google

也就是，`google-chrome-stable` 是浏览器。

我开始尝试执行下面的代码。

```python
from selenium import webdriver
browser = webdriver.Chrome()
browser.get('https://btc.com/')
print(browser.page_source)
browser.close()
```

出现了

	selenium.common.exceptions.WebDriverException: Message: unknown error: Chrome failed to start: exited abnormally.
	(unknown error: DevToolsActivePort file doesn't exist)
	(The process started from chrome location /usr/bin/google-chrome is no longer running, so ChromeDriver is assuming that Chrome has crashed.)

我个人认为是

	(unknown error: DevToolsActivePort file doesn't exist)

这个错误。

经过查阅资料，我将其改成了

```python
from selenium import webdriver

options = webdriver.ChromeOptions()
options.add_argument("--no-sandbox")
options.add_argument('--headless')
options.add_argument('blink-settings=imagesEnabled=false')
browser = webdriver.Chrome(chrome_options=options)
browser.get('https://btc.com/')
print(browser.page_source)
browser.close()
```

教程上说

	options.add_argument("--no-sandbox")

这个必须放在第一位。但是，我修改了位置，还是可以。

ps：这个东西，我搞了好久最后，莫名其妙的都好了。在这个教程中，你看着我逻辑清晰，其实，我真正的实施路径并不是这样的，真的花了我很长的时间。

希望我的教程可以帮到你。

