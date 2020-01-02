---
title: 博客添加搜索框
date: 2018-11-08 08:46:49
categories:
- hexo
tags:
- hexo
- error
- npm
---
由于博客的文章数量与日俱增，查找一个东西变的耗时耗力，虽然，我们可以通过类来查阅，但是很不幸，文章太多的话，查起来也是很烦。
于是，我想着为博客添加一个搜索功能。
<!-- more -->
我的博客是架构在 github 上的静态博客，所以那些通过服务器查找数据库的搜索原理通通不能用。
所以，静态博客一般是在本地生成一个搜索文件，比如：***.xml ，然后，我们在前端搜索的时候，ajax 会自动读取这个文件，然后返回。
所以，基于这个原理，我们需要第三方插件。
## hexo-generator-search
[原文博客](https://www.chunqiuyiyu.com/2018/07/hexo-local-search.html)
经过查阅我找到了这篇博客，并且按照步骤安装。

	npm i hexo-generator-search --save
	
然后在 _config.yaml 中配置插件。这里的 _config.yaml 是项目的，而不是主题的。

	search:
		path: search.xml
		field: post
		format: html
		
安装插件后，每次使用 hexo s 与 hexo g 命令后此插件会抓取最新的文章内容到 search.xml中，这个文件就是我们查询服务的数据库。
添加搜索框。
在 themes/polk/layout 中合适的位置添加搜索框（想加哪儿就加哪儿，看着喜欢就好）。我自己的博客是将搜索框与标签云放置在独立显示的导航页面中。

	<div class="search">
		<input type="text" autocomplete="off" name="search" placeholder="搜索" id="local-search-input">
		<div id="local-search-result"></div>
	</div>
	
添加脚本
此时我们有搜索框来接受用户输入，也有后端容纳数据，是时候让 JavaScript 大显身手了。
{% codeblock %}

var inputArea = document.querySelector("#local-search-input");
if (inputArea) {
	inputArea.onclick = function() {
		inputArea.placeholder = '首次搜索，需载入索引文件，请稍候';
		getSearchFile();
		this.onclick = null
}
	inputArea.onkeydown = function() { if (event.keyCode == 13) return false }
}

var getSearchFile = function() {
var path = "/search.xml";
searchFunc(path, 'local-search-input', 'local-search-result');
}

{% endcodeblock %}
但是，因为我对 hexo 的分布很不了解，于是 js 添加到哪失败了。
## 后期问题
由于生成的 ***.xml 是一个非常大的文件，而且上传到 github 中，本来就是一个运气事件，但是，加上这个大文件，我上传成功的几率大幅度下降。
并且，每次都必须生成一个新的 xml 十分耗时。于是，我极其后悔，想将这个第三方插件卸载，但是万万没想到。。。
因为我 install 插件是直接打开的 Git Bash。所以，卸载的时候，我也是直接这样。。。
我试了如下的命令

	npm uninstall hexo-generator-search
	npm uninstall hexo-generator-search --save
	npm uninstall -g hexo-generator-search
	npm uninstall -g hexo-generator-search --save
	
但是，最后都以失败告终。无论怎么卸载，这个第三方插件都还在。
几近崩溃，于是我找到了一个大腿，说起缘由。
大腿告诉我，你应该到项目中卸载，而不是全局卸载。
这里要特别的说明一下卸载的地方，我们知道 npm 自成一个文件夹，位置大概在

	C:\Users\Administrator\AppData\Roaming\npm\node_modules
	
这个是 npm 的目录所在。
但是，我们的插件却安装在项目文件下，我的是以下路径

	C:\Users\Administrator\benpaodewoniu.github.io\node_modules
	
所以，在全局下卸载，是怎么也卸载不了的。
正确的姿势如下，先找到项目中的模块配置文件 package.json
打开后，删除相应的第三方插件
然后，进入第三方库的文件夹，就是下面这个

	C:\Users\Administrator\benpaodewoniu.github.io\node_modules
	
直接删除对应的第三方模块。
OK，到了这个时候，终于恢复正常了。
后期，我对于如何在大规模下快速的查询文章，采用了折中的办法，那就是给每一个文章打上详尽的标签，这样，以后靠标签搜索。
另外，如果文章数量持续增加，那么打标签根本满足帮不了我，所以，以后还有可能继续添加搜索功能。
未完待续。。。
	