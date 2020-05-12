---
title: 博客制作——django 分页
date: 2019-08-13 10:07:56
categories:
- python
- 框架
- django
tags:
- Django
---
这一篇讲述分页。

<!-- more -->

为了获得具体是哪一页，我们的 url 采用 /blog/index?page=1 的这种方式

我们将boorstrap的分页组件放在相应的页面中

![](/images/django/5_0.png)

<br/>

# django的分页组件

<br/>

```python
from django.core.paginator import Paginator
l = [1,2,3,4]
p = Paginator(l,2) # 将 l 分为 2 组
print(p.num_pages)
	# 2
print(p.count)
	# 4
page1 = p.page(1)
print(page1.object_list)
	# [1,2]
page2 = p.page(2)
print(page2.object_list)
	# [3,4]
print(page2.has_next())
	# False
print(page1.has_previous())
	# False

```

为了获取 url 里的参数，我们可以修改blog/views.py 的 get_index_page()

```python
def get_index_page(request):
    page = request.GET.get('page')
    if page:
        page = int(page)
    else:
        page = 1

    all_article = Article.objects.all()

    paginator = Paginator(all_article, 3)
    page_num = paginator.num_pages
    page_article_list = paginator.page(page)

    if page_article_list.has_next():
        next_page = page + 1
    else:
        next_page = page

    if page_article_list.has_previous():
        previous_page = page - 1
    else:
        previous_page = page
    return render(request, 'blog/index.html',
                  {
                      'article_list': page_article_list,
                      'page_num': range(1, page_num + 1),
                      'curr_page': page,
                      'next_page': next_page,
                      'previous_page': previous_page
                  })
```

而 /blog/index.html的界面代码如下：

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>这是一个测试</title>
    <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
    <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa"
            crossorigin="anonymous"></script>
</head>
<body>
<div class="container page-header">
    <h1>三小时入门django
        <small>licong</small>
    </h1>
</div>
<div class="container panel-body">
    <div class="col-md-9" role="main">
        <div class="body-main">
            {% for article in article_list %}
                <div>
                    <h2>{{ article.title }}</h2>
                    <p>
                        {{ article.brief_content }}
                    </p>
                </div>
            {% endfor %}
        </div>
    </div>
    <div class="col-md-3" role="complementary">
        <div>
            <h2>最新文章</h2>
            {% for article in article_list %}
                <h4><a href="#">{{ article.title }}</a></h4>
            {% endfor %}
        </div>
    </div>
    <div>
        <nav aria-label="Page navigation">
            <ul class="pagination">
                <li>
                    <a href="/blog/index?page = {{ previous_page }}" aria-label="Previous">
                        <span aria-hidden="true">&laquo;</span>
                    </a>
                </li>
                {% for num in page_num %}
                    <li><a href="/blog/index?page={{ num }}">{{ num }}</a></li>
                {% endfor %}
                <li>
                    <a href="/blog/index?page = {{ next_page }}" aria-label="Next">
                        <span aria-hidden="true">&raquo;</span>
                    </a>
                </li>
            </ul>
        </nav>
    </div>
</div>
</body>
</html>

```

![](/images/django/5_1.png)











