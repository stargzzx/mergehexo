---
title: python | 下载文件的 11 种方式
date: 2020-07-12 17:09:46
categories:
- python
- 进阶
tags:
- python
---
你将学习如何使用不同的Python模块从web下载文件。此外，你将下载常规文件、web页面、Amazon S3和其他资源。

最后，你将学习如何克服可能遇到的各种挑战，例如下载重定向的文件、下载大型文件、完成一个多线程下载以及其他策略。

<!-- more -->

## 使用 Requests

你可以使用requests模块从一个URL下载文件。

考虑以下代码:

```python
import requests
url = '**/**.png'
file = requests.get(url)
open('**.png','wb').write(file.content)
```
你只需使用requests模块的get方法获取URL，并将结果存储到一个名为“myfile”的变量中。然后，将这个变量的内容写入文件。

## 使用wget

你还可以使用Python的wget模块从一个URL下载文件。你可以使用pip按以下命令安装wget模块:

```python
pip install wget
```

考虑以下代码，我们将使用它下载Python的logo图像。

```python
import wget
url = '***/***.png'
wget.download(url,'**.png')
```

在这段代码中，URL和路径(图像将存储在其中)被传递给`wget`模块的`download`方法。

## 下载重定向的文件

在本节中，你将学习如何使用`requests`从一个`URL`下载文件，该URL会被重定向到另一个带有一个`.pdf`文件的URL。该`URL`看起来如下:

	https://***/downloads/pdf/lastest/

要下载这个pdf文件，请使用以下代码:

```python
import requests
url = "https://***/downloads/pdf/lastest/"
file = requests.get(url,allow_redirects=True)
open(**/**.pdf,'wb').write(file.content)
```

在这段代码中，我们第一步指定的是URL。然后，我们使用`request`模块的`get`方法来获取该`URL`。在get方法中，我们将`allow_redirects`设置为`True`，这将允许`URL`中的重定向，并且重定向后的内容将被分配给变量`file`。

最后，我们打开一个文件来写入获取的内容。

## 分块下载大文件

下载大文件时，需要考虑内存问题，使用requests.get方法，默认会立即下载文件内容并保存到内存中，如果文件很大，会给内存造成压力，因此我们需要设置stream参数为True，这样，只有当我们遍历iter_content时才会进行数据下载

考虑下面的代码:

```python
import requests

url = 'https://www.python.org/ftp/python/3.8.1/python-3.8.1-macosx10.9.pkg'
res = requests.get(url, stream=True)

print(res.status_code, res.headers)

with open("py.pkg", "wb") as pypkg:
    for chunk in res.iter_content(chunk_size=1024):
        if chunk:
            pypkg.write(chunk)
```

不漂亮吗?不要担心，稍后我们将显示一个下载过程的进度条。

## 使用进度条下载

使用clint模块来显示下载进度，你需要先安装

```python
import requests
from clint.textui import progress

url = 'https://www.python.org/ftp/python/3.8.1/python-3.8.1-macosx10.9.pkg'
res = requests.get(url, stream=True)
total_length = int(res.headers.get('content-length'))

with open("py.pkg", "wb") as pypkg:
    for chunk in progress.bar(res.iter_content(chunk_size=1024), expected_size=(total_length/1024) + 1, width=100):
        if chunk:
            pypkg.write(chunk)
```

一次读取1024个字节，总文件大小是29051411，共需要读取28371次，随着读取的进行，进度条也会发生变化

## 下载多个文件(并行/批量下载)

要同时下载多个文件，请导入以下模块:

```python
import os
import requests
from time import time
from multiprocessing.pool import ThreadPool
```

我们导入了os和time模块来检查下载文件需要多少时间。ThreadPool模块允许你使用池运行多个线程或进程。

让我们创建一个简单的函数，将响应分块发送到一个文件:

```python
def url_response(url):
	path,url = url
	r = requests.get(url,stream = True)
	with open(path,'wb') as f:
		for ch in r:
			f.write(ch)
```

这个URL是一个二维数组，它指定了你要下载的页面的路径和URL。

```python
urls = [("Event1,'url1"),
("Event2,'url2"),
("Event3,'url3"),
("Event4,'url4"),
("Event5,'url5"),
("Event6,'url6"),
("Event7,'url7"),
...]
```

就像在前一节中所做的那样，我们将这个`URL`传递给`requests.get`。最后，我们打开文件(URL中指定的路径)并写入页面内容。

现在，我们可以分别为每个URL调用这个函数，我们也可以同时为所有URL调用这个函数。让我们在for循环中分别为每个URL调用这个函数，注意计时器:

```python
start = time()
for x in urls:
	url_response(x)
print(time() - start)
```

使用下面的代码替代 `for 循环`

```python
ThreadPool(9).imap_unordered(url_response,urls)
```

## 使用urllib下载网页

在本节中，我们将使用urllib下载一个网页。

urllib库是Python的标准库，因此你不需要安装它。

以下代码行可以轻松地下载一个网页:

```python
urllib.request.urlretrieve('url','path')
```

在这里指定你想将文件保存为什么以及你想将它存储在哪里的URL

	urllib.request.urlretrieve('https://www.baidu/com','~/baidu.html')

在这段代码中，我们使用了`urlretrieve`方法并传递了文件的URL，以及保存文件的路径。文件扩展名将是.html。

## 通过代理下载

如果你需要使用代理下载你的文件，你可以使用urllib模块的ProxyHandler。请看以下代码：

```python
import urllib.request

proxy = urllib.request.ProxyHandler({'http':'127.0.0.2'})
openProxy = urllib.request.build_opener(proxy)
urllib.request.urlretrieve('https://...')
```

在这段代码中，我们创建了代理对象，并通过调用`urllib`的`build_opener`方法来打开该代理，并传入该代理对象。然后，我们创建请求来获取页面。

此外，你还可以按照官方文档的介绍来使用requests模块:

```python
import requests
proxy = {'http':'http://127.0.0.2:3001'}
requests.get('url',proxies = proxy)
```

你只需要导入requests模块并创建你的代理对象。然后，你就可以获取文件了。

## 使用urllib3

`urllib3`是`urllib`模块的改进版本。你可以使用pip下载并安装它:

```python
pip install urllib3
```

我们将通过使用urllib3来获取一个网页并将它存储在一个文本文件中。

导入以下模块:

```python
import urllib3,shutil
```

在处理文件时，我们使用了shutil模块。

现在，我们像这样来初始化URL字符串变量：

```python
url = 'url'
```

然后，我们使用了urllib3的PoolManager ，它会跟踪必要的连接池。

```python
c = urllib3.PoolManager()
```

创建一个文件

```python
file = 'test.txt'
```

最后，我们发送一个`GET`请求来获取该URL并打开一个文件，接着将响应写入该文件:

```python
with c.request('GET',url,preload_content = False) as res, open(file,'wb') as out:
	shutil.copyfileobj(res,out)
```

## 使用Boto3从S3下载文件

这个没懂，请参照

- [Python下载文件的11种方式](https://mp.weixin.qq.com/s/dhW5u4_ww8DRiPNwUgvMfw)

## 使用asyncio

`asyncio`模块主要用于处理系统事件。它围绕一个事件循环进行工作，该事件循环会等待事件发生，然后对该事件作出反应。这个反应可以是调用另一个函数。这个过程称为事件处理。asyncio模块使用协同程序进行事件处理。

要使用`asyncio`事件处理和协同功能，我们将导入`asyncio`模块:

```python
import asyncio
```

现在，像这样定义asyncio协同方法：

```python
async def coroutine():
	await fun()
```

关键字`async`表示这是一个原生`asyncio`协同程序。在协同程序的内部，我们有一个`await`关键字，它会返回一个特定的值。我们也可以使用`return`关键字。

现在，让我们使用协同创建一段代码来从网站下载一个文件:

```python
import os
import urllib.requests

async def coroutine(url):
	r = urllib.request.urlopen(url)
	filename = '***.txt'
	with open(filename,'wb') as f:
		for ch in f:
			f.write(ch)
	msg = 'success'
	return msg

async def main_fun(urls_to_download):
	co = [coroutine(url) for url in urls_to_download]

	downloaded,downloading = await asyncio.wait(co)

	for i in downloaded:
		print(i.result())

urls_to_download = ['url1','url2','url3']

eventLoop = asyncio.get_event_loop()
eventLoop.run_until_complete(main_fun(urls_to_download))
```

在这段代码中，我们创建了一个异步协同函数，它会下载我们的文件并返回一条消息。

然后，我们使用另一个异步协同程序调用`main_fun`，它会等待URL并将所有URL组成一个队列。asyncio的wait函数会等待协同程序完成。

现在，为了启动协同程序，我们必须使用`asyncio`的`get_event_loop()`方法将协同程序放入事件循环中，最后，我们使用`asyncio`的`run_until_complete()`方法执行该事件循环。
