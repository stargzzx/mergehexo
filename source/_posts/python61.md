---
title: python-crontab
date: 2019-10-27 16:30:31
categories:
- python
tags:
- python
- crontab
- linux
---
因为项目需求需要在服务器上定时任务，进行扫描。

<!-- more -->

在linux上进行定时任务扫描，也就是需要需要 crontab ，而 python 中也有这个包，强大的 python。

下面一起来看看如何使用这个包吧。

## 安装

	pip install python-crontab
	
## 使用

封装一个类，用来新增和清除定时任务：

下面这个是 python2.7 的操作，根据语法换成 3 就好了。

{% codeblock %}
# coding=utf-8
from crontab import CronTab
class Crontab_Update(object):
  def __init__(self):
    # 创建当前用户的crontab，当然也可以创建其他用户的，但得有足够权限
    self.cron = CronTab(user=True)
    # self.cron = CronTab(user='website')
  def add_crontab_job(self, cmmand_line, time_str, commont_name, user):
    # 创建任务
    job = self.cron.new(command=cmmand_line)
    # 设置任务执行周期
    job.setall(time_str)
    # 给任务添加一个标识，给任务设置comment，这样就可以根据comment查询
    job.set_comment(commont_name)
    # 将crontab写入配置文件
    # self.cron.write()
    self.cron.write_to_user(user=user) # 指定用户，写入指定用户下的crontab任务
  def del_crontab_jobs(self, comment_name, user):
    # 根据comment查询，当时返回值是一个生成器对象，
    # 不能直接根据返回值判断任务是否存在，
    # 如果只是判断任务是否存在，可直接遍历my_user_cron.crons
    # jobs = self.cron.find_comment(commont_name)
    # 返回所有的定时任务，返回的是一个列表
    # a = self.cron.crons
    # print 'a = ', a
    # print 'len(a) = ', len(a)
    # 按comment清除定时任务
    # self.cron.remove_all(comment=comment_name)
    # 按comment清除多个定时任务，一次write即可
    self.cron.remove_all(comment=comment_name)
    self.cron.remove_all(comment=comment_name+ ' =')
    # 清除所有定时任务
    # self.cron.remove_all()
    # 写入配置文件
    # self.cron.write()
    self.cron.write_to_user(user=user) # 指定用户,删除指定用户下的crontab任务
if __name__ == "__main__":
  print 'start --------'
  cmmand_line = "/usr/bin/python /mnt/print_time.py"
  time_str = "* * * * *"
  commont_name = "Test_Crontab_Job"
  user = "xue"
  # 创建一个实例
  crontab_update = Crontab_Update()
  # 调用函数新增一个crontab任务
  # print '&&&&&& add_crontab_job '
  # crontab_update.add_crontab_job(cmmand_line, time_str, commont_name, user)
  print '&&&&&& del_crontab_jobs '
  crontab_update.del_crontab_jobs(commont_name, user)
  print 'end -------'
{% endcodeblock %}

设置定时任务后：

下面可通过命令查看，是否创建成功：

	crontab -l

<br/>

# 注意点

<br/>

上面的代码我是直接贴的网上的，但是也可以看懂，主要有两个注意点。

## 1

如果你的文件名是 crontab ，那么你引入包的时候会报错 from crontab import CronTab

![](/images/python/61_1.png)

## 2

crontab是很蠢的，你必须要把所有的路径都标注出来。

而所有的路径都需要体现在上面那个代码中的 cmmand_line 中。

比如，我的项目是 eosvoter 。我要想执行某一个python文件，需要执行下面的步骤

	cd /root/eosvoter/monitor (我把执行代码放在这个目录下)
	python3.7 dingTalkSend.py
	
只有这样我才能执行这个文件，所以，我也想对小伙伴说，要想正确的验证，你的命令是否能够执行，你可以到其他目录走一遍，然后，我的总命令就是

	cd /root/eosvoter/monitor;python3.7 /root/eosvoter/monitor/dingTalkSend.py
	
我是验证过可以执行，然后，在cmmand_line 中添加相应的 str 。

然后我们使用 

	crontab -l
	
可以查看到

![](/images/python/61_0.png)

还有一点关于在 crontab 中如何分隔多个命令，其语法如下：

多个命令可以放在一行上，其执行情况得依赖于用在命令之间的分隔符。

如果每个命令被一个分号 (;) 所分隔，那么命令会连续的执行下去

如果每个命令被 && 号分隔，那么这些命令会一直执行下去，如果中间有错误的命令存在，则不再执行后面的命令，没错则执行到完为止

如果每个命令被双竖线(||)分隔符分隔，如果命令遇到可以成功执行的命令，那么命令停止执行，即使后面还有正确的命令则后面的所有命令都将得不到执行。假如命令一开始就执行失败，那么就会执行 || 后的下一个命令，直到遇到有可以成功执行的命令为止，假如所有的都失败，则所有这些失败的命令都会被尝试执行一次

<br/>

# 时间设置

<br/>

![](/images/python/61_2.png)

添加定时任务 : crontab -e

基本格式 : 

	*　　*　　*　　*　　*　　command 
	分　 时　 日　 月　 周　 命令 
	
解 释: 

	第1列表示分钟1～59 每分钟用或者 /1表示 
	第2列表示小时1～23（0表示0点） 
	第3列表示日期1～31 
	第4列表示月份1～12 
	第5列标识号星期0～6（0表示星期天） 
	第6列要运行的命令
