---
title: 修改knowhow中的坑
date: 2019-07-08 20:26:32
categories:
- 网站设计
- 前端设计
- wordpress
tags:
- webdesign
- wordpress
- 网站
- konwhow
---
我们的knowhow的其中一个文件可能会需要修改。

<!-- more -->

# 文件1

这个文件在 htdocs\wordpress\wp-content\themes\konwhow\content-meta.php

我将文件改成下面的那个了。

```python
<?php 
$st_post_meta = of_get_option('st_article_meta' );
$number = get_comments_number(get_the_ID()); ?>


<ul class="entry-meta clearfix">


<li class="date"> 
    <i class="icon-time"></i>
    <time datetime="<?php the_time('Y-m-d')?>" itemprop="datePublished"><?php the_time( get_option('date_format') ); ?></time>
</li>



<li class="author">
    <i class="icon-user"></i>
	<?php the_author(); ?>
</li>




<li class="category">
    <i class="icon-folder-close"></i>
    <?php the_category('/ '); ?>
    </li>



<?php if ( comments_open() ){ ?>
<li class="comments">
    <i class="icon-comment"></i>
	<?php comments_popup_link( __( '0 Comments', 'framework' ), __( '1 Comment', 'framework' ), __( '% Comments', 'framework' ) ); ?><?php } ?>
</li>


</ul>
```



