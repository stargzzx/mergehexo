---
title: linux | 命令行下载，联网访问
date: 2020-01-10 11:47:33
categories:
- linux
tags:
- linux
- curl
- wget
---
这个目前主要讲 curl 和 wget。

<!-- more -->

当想要直接通过 Linux 命令行下载文件，马上就能想到两个工具：wget 和 cURL。它们有很多一样的特征，可以很轻易的完成一些相同的任务，虽然它们有一些相似的特征，但它们并不是完全一样。这两个程序适用与不同的场合，在特定场合下，都拥有各自的特性。

# curl 与 wget 的区别

wget 和 cURL 都可以下载内容。它们的核心就是这么设计的。它们都可以向互联网发送请求并返回请求项。这可以是文件、图片或者是其他诸如网站的原始 HTML 之类。

这两个程序都可以进行 HTTP POST 请求。这意味着它们都可以向网站发送数据，比如说填充表单什么的。

由于这两者都是命令行工具，它们都被设计成可脚本化。wget 和 cURL 都可以写进你的 Bash 脚本 ，自动与新内容交互，下载所需内容。

## wget

wget 简单直接。这意味着你能享受它超凡的下载速度。wget 是一个独立的程序，无需额外的资源库，更不会做其范畴之外的事情。

wget 是专业的直接下载程序，支持递归下载。同时，它也允许你下载网页中或是 FTP 目录中的任何内容。

wget 拥有智能的默认设置。它规定了很多在常规浏览器里的事物处理方式，比如 cookies 和重定向，这都不需要额外的配置。可以说，wget 简直就是无需说明，开罐即食！

## curl

cURL是一个多功能工具。当然，它可以下载网络内容，但同时它也能做更多别的事情。

cURL 技术支持库是：libcurl。这就意味着你可以基于 cURL 编写整个程序，允许你基于 libcurl 库中编写图形环境的下载程序，访问它所有的功能。

cURL 宽泛的网络协议支持可能是其最大的卖点。cURL 支持访问 HTTP 和 HTTPS 协议，能够处理 FTP 传输。它支持 LDAP 协议，甚至支持 Samba 分享。实际上，你还可以用 cURL 收发邮件。

cURL 也有一些简洁的安全特性。cURL 支持安装许多 SSL/TLS 库，也支持通过网络代理访问，包括 SOCKS。这意味着，你可以越过 Tor 来使用cURL。

cURL 同样支持让数据发送变得更容易的 gzip 压缩技术。

## 类比

cURL 支持你做很多事情。你可以把 cURL 想象成一个精简的命令行网页浏览器。它支持几乎你能想到的所有协议，可以交互访问几乎所有在线内容。唯一和浏览器不同的是，cURL 不会渲染接收到的相应信息。

curl由于可自定义各种请求参数所以在模拟web请求方面更擅长；wget由于支持ftp和Recursive所以在下载文件方面更擅长。类比的话curl是浏览器，而wget是迅雷9。

curl是libcurl这个库支持的，wget是一个纯粹的命令行命令。

curl支持更多的协议。curl supports FTP, FTPS, HTTP, HTTPS, SCP, SFTP, TFTP, TELNET, DICT, LDAP, LDAPS, FILE, POP3, IMAP, SMTP and RTSP at the time of this writing. Wget supports HTTP, HTTPS and FTP.

curl 默认支持HTTP1.1（也支持1.0），而wget仅仅支持HTTP1.0规范。

curl在指定要下载的链接时能够支持URL的序列或集合，而wget则不能这样;

wget支持递归下载，而curl则没有这个功能。

# 命令区别

## 下载文件

	curl -O http://man.linuxde.net/text.iso
		#O大写，不用O只是打印内容不会下载
	
	wget http://www.linuxde.net/text.iso
		#不用参数，直接下载文件

## 下载文件并重命名

	curl -o rename.iso http://man.linuxde.net/text.iso
		#o小写

	wget -O rename.zip http://www.linuxde.net/text.iso
		#O大写

## 断点续传

	curl -O -C - http://man.linuxde.net/text.iso
		#O大写，C大写
	
	wget -c http://www.linuxde.net/text.iso
		#c小写

## 限速下载

	curl --limit-rate 50k -O http://man.linuxde.net/text.iso

	wget --limit-rate=50k http://www.linuxde.net/text.iso

## 显示响应头部信息

	curl -I http://man.linuxde.net/text.iso

	wget --server-response http://www.linuxde.net/test.iso

## wget利器--打包下载网站

	wget --mirror -p --convert-links -P /var/www/html http://man.linuxde.net/

这里面仅仅只是很少以一部分命令说明，后期会继续补充。
