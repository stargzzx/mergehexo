---
title: 抓捕漏的数据|币币生息
date: 2019-09-22 16:07:51
categories:
- 币币生息
tags:
- 项目经验
- 币币生息
---
在公司接到的第一件事就是将漏抓的数据补全。
<!-- more -->
因为之前写的python脚本有问题，导致，有些数据没抓到，需要补全，所以，陈哥将这项工作交给我。
{% img /images/bbsx/1_0.png %}
我的环境是 python3.7
python的web框架为flask。
# 爬虫
初期，我们是想采用抓包的方式，将漏的数据抓下来。
我们需要抓取的网址是
[bloks.io](https://bloks.io/account/eosnairobike)
一共有三个节点的数据需要抓取，所以，我们的网址如下

	https://bloks.io/account/eosnairobike
	https://bloks.io/account/eosiomeetone
	https://bloks.io/account/starteosiobp
	
我们想抓取8月31号——9月20号的节点出块和投票奖励数据。
{% img /images/bbsx/1_1.png %}
于是，经过陈哥的指点，我们要抓取的包的网址是

	https://eos.greymass.com/v1/history/get_actions
	
我们打开开发者界面，发现
{% img /images/bbsx/1_2.png %}
请求时 post 请求，并且，还有三个参数。
所以，我们写代码如下：
{% codeblock %}
import requests
import json
import time

url = "https://eos.greymass.com"
uri = "/v1/history/get_actions"

start = -1
end = -10000

api_timeout = 30
def_headers = {
    'Content-Type': 'application/json'
}

def_timeout = api_timeout
def post(url, headers=def_headers, timeout=def_timeout, data=None, proxies=None):
    response = requests.post(url, data=data, headers=headers, timeout=timeout, proxies=proxies)
    if response.status_code / 200 != 1:
        raise Exception(response.text)
    return response


def handle_action(actions,index):
    account = 'eosio.vpay'

    action = actions[index]
    action_trace = action['action_trace']
    act = action_trace['act']
    action_name = act['name']
    contract = act['account']
    if action_name != 'transfer' or contract != 'eosio.token':
        return True
    if action_trace['receipt']['receiver'] != account:
        return True
    block_time_str = str(action['block_time'])
    sender = 'eosio.vpay'
    receiver = 'eosnairobike'
    # print(action)
    if sender == act['data']['from'] and receiver == act['data']['to']:
        print(action)


data = """
    {
        "account_name": "%s","pos": %s,"offset": %s
    }
    """ % ("eosnairobike", start, end)
print(data)
actions = post(url=url + uri, data=data, headers=def_headers).json()['actions']
index = len(actions) - 1
print(index)
while index >= 0 :
    try:
        # print(actions[index])
        result = handle_action(actions,index)
    finally:
        index -= 1
{% endcodeblock %}
对于解读数据包的结构我们在这里就不说了，我们重点说传包的数据即

	data = """
		{
			"account_name": "%s","pos": %s,"offset": %s
		}
		""" % ("eosnairobike", 1, 1000)

这个在我用3.7之前，是用下面的传参方式

	data = ""
		{
			"account_name": "%s","pos": %s,"offset": %s
		}
		"" % ("eosnairobike", 1, 1000)
		
但是，总是返回500，服务器拒绝访问。最后才发现是数据的格式问题，改成上面的格式，才能访问到数据。
# 丢包
尽管上面的代码可以访问到数据，但是丢包现像及其严重。
{% img /images/bbsx/1_2.png %}
这个有三个参数

	account_name
	pos
	offset
	
其中pos代表位置，offset代表迁移，如果

	pos = -1
	offset = -100
	
代表的是取最新的100条数据（倒着取），但是，由于数据很多，我们的数据范围是100000条，所以，我们将 offset = -1000000
最后的结果是这样的
{% img /images/bbsx/1_3.png %}
可以看出虽然取得数据范围都一样，但是，抓的数据量却不一样。
所以，想着把pos进行移动
{% img /images/bbsx/1_4.png %}
最后的实验结果，pos只能等于-1。
后来又尝试抓取了正向数据，但是也是有问题。
### 疑问
最终因为这些问题，导致数据不能被抓取，实在是遗憾。
但是，也有一个问题，丢包这么严重，为什么网站的从来没有丢包，可以全部展示，所以，其中肯定是有一些机制搞错了。
## 手动录入
因为抓包失败，所以，只能手动录入一些数据，然后写脚本将这些数据录到数据库中。
幸好只有十几天的数据，所以也不是很麻烦。

	2019-09-20 03:57:29 0 113.6447
	2019-09-19 03:57:26 0 109.8642
	2019-09-18 03:57:24 0 110.6762
	2019-09-17 05:57:21 0 116.1159
	2019-09-16 03:52:33 0 119.7189
	2019-09-15 03:52:30 0 121.7453
	2019-09-13 03:52:25 0 128.5356
	2019-09-12 03:52:22 0 127.1297
	2019-09-11 03:52:19 0 150.8844
	2019-09-10 03:52:16 0 131.1467
	2019-09-09 03:52:13 0 249.8659
	2019-09-08 03:52:11 0 247.8559
	2019-09-07 03:52:08 0 247.6257
	2019-09-06 03:52:06 0 248.2704
	2019-09-05 03:52:03 0 227.7176
	2019-09-04 03:53:00 0 257.5333
	2019-09-03 03:51:57 0 253.9288
	2019-09-02 03:51:54 0 263.8488
	2019-09-01 03:51:52 0 262.6623
	2019-09-21 09:30:53 328.8607 597.4688
	2019-09-20 09:30:52 329.0933 609.388
	2019-09-19 09:30:51 328.6718 601.1045
	2019-09-18 09:30:49 328.1935 600.0729
	2019-09-17 09:30:48 322.2121 596.7116
	2019-09-16 09:30:46 327.6421 623.953
	2019-09-15 09:30:45 327.6593 631.4993
	2019-09-14 09:30:44 328.0858 645.0606
	2019-09-13 09:30:42 328.012 651.1466
	2019-09-12 09:30:41 327.7106 598.7641
	2019-09-11 09:30:40 328.1227 555.3307
	2019-09-10 09:30:39 327.013 554.228
	2019-09-09 09:30:37 327.8269 545.8809
	2019-09-08 09:30:36 327.7262 565.6201
	2019-09-07 09:30:34 328.6543 564.8974
	2019-09-06 09:30:33 298.7611 550.7432
	2019-09-05 09:30:31 327.6324 509.6212
	2019-09-04 09:30:29 328.0524 518.8298
	2019-09-03 09:30:27 321.4466 495.493
	2019-09-02 09:30:26 327.6381 497.1798
	2019-09-01 09:30:25 328.1644 498.8103
	2019-08-31 09:30:24 307.8788 500.731
	2019-09-21 04:30:26 329.6669 567.5969
	2019-09-20 04:30:24 329.3243 587.2845
	2019-09-19 04:28:45 331.3629 573.9292
	2019-09-18 04:34:23 331.3979 553.9459
	2019-09-17 04:14:23 328.1745 544.5849
	2019-09-16 03:58:27 328.6312 537.2949
	2019-09-15 03:58:23 328.3801 545.1533
	2019-09-14 03.58.22 329.3878 560.8843
	2019-09-13 03:56:22 331.4432 555.1423
	2019-09-12 03:42:23 329.6426 556.5584
	2019-09-11 03:38:25 329.4591 555.9285
	2019-09-10 03:36:25 328.5671 546.5143
	2019-09-09 03:36:22 328.7868 553.9193
	2019-09-08 03:34:22 329.2491 547.4382
	2019-09-07 03:32:24 329.471 550.2771
	2019-09-06 03:30:22 328.9204 555.164
	2019-09-05 03:28:22 329.0479 548.9647
	2019-09-04 03:26:21 328.5712 557.3907
	2019-09-03 03:26:21 328.9192 550.5592
	2019-09-02 03:24:21 328.8934 571.4601
	2019-09-01 03:22:21 328.3212 577.812
	2019-08-31 03:22:20 328.684 579.4395

然后脚本代码如下：
{% codeblock %}
import pymysql

db = pymysql.connect(
    host='localhost',
    port=3306,
    user='root',
    passwd='12345678',
    db='eos_voter')


cursor = db.cursor()
with open('data.txt','r') as file:
    lines = file.readlines()
    for line in lines:
        claim_time   = line.split(' ')[0] +' ' + line.split(' ')[1]
        block_reward = float(line.split(' ')[2].strip())
        vote_reward  = float(line.split(' ')[3].strip())
        all_reward   = block_reward + vote_reward

        sql = """
            SELECT vote_eos FROM t_claim_info WHERE claim_time='%s'
        """ % (claim_time)

        numnber = cursor.execute(sql)
        if numnber == 1:
            vote_eos = list(cursor.fetchone())[0]
            reward_every_10w_eos = round(all_reward * 100000.0 / vote_eos,4)


            sql = """
                UPDATE t_claim_info SET block_reward=%f,vote_reward=%f,all_reward=%f,reward_every_10w_eos=%f where claim_time='%s'
                """ % (block_reward,vote_reward,all_reward,reward_every_10w_eos,claim_time)
            try:
                cursor.execute(sql)
                db.commit()
            except:
                print("fail")
{% endcodeblock %}
最后成功录入。有一点需要注意的是
{% img /images/bbsx/1_0.png %}
因为eos出块的时间都不一样，所以，录入数据库是寻求时间戳的唯一性进行录制的。
# 代码改进
这个代码是陈哥改的。
上面的代码其实有存在一定BUG的。
因为我是手动录入，所以会导致数据上出错，比较常见的一种错误就是，你的时间写错了，但是这个时间恰好是其他节点的出块时间，所以，导致最后数据填充出现问题。
所以，这里的关键点是找到唯一的，并且，即便是时间上写错，依然不会出现任何问题。
因为区块链的出块时间是唯一的，并且，出块的对象也是唯一的，所以，只要同时满足出块对象和出块时间相吻合，就能保证数据的唯一，所以，最后的数据集的内容如下：

	eosnairobike 2019-09-19 03:57:26 0 109.8642
	eosnairobike 2019-09-18 03:57:24 0 110.6762
	eosnairobike 2019-09-17 03:57:21 0 116.1159
	eosnairobike 2019-09-16 03:52:33 0 119.7189
	eosnairobike 2019-09-15 03:52:30 0 121.7453
	eosnairobike 2019-09-13 03:52:25 0 128.5356
	eosnairobike 2019-09-12 03:52:22 0 127.1297
	eosnairobike 2019-09-11 03:52:19 0 150.8844
	eosnairobike 2019-09-10 03:52:16 0 131.1467
	eosnairobike 2019-09-09 03:52:13 0 249.8659
	eosnairobike 2019-09-08 03:52:11 0 247.8559
	eosnairobike 2019-09-07 03:52:08 0 247.6257
	eosnairobike 2019-09-06 03:52:06 0 248.2704
	eosiomeetone 2019-09-19 09:30:51 328.6718 601.1045
	eosiomeetone 2019-09-18 09:30:49 328.1935 600.0729
	eosiomeetone 2019-09-17 09:30:48 322.2121 596.7116
	eosiomeetone 2019-09-16 09:30:46 327.6421 623.953
	eosiomeetone 2019-09-15 09:30:45 327.6593 631.4993
	eosiomeetone 2019-09-14 09:30:44 328.0858 645.0606
	eosiomeetone 2019-09-13 09:30:42 328.012 651.1466
	eosiomeetone 2019-09-12 09:30:41 327.7106 598.7641
	eosiomeetone 2019-09-11 09:30:40 328.1227 555.3307
	eosiomeetone 2019-09-10 09:30:39 327.013 554.228
	eosiomeetone 2019-09-09 09:30:37 327.8269 545.8809
	eosiomeetone 2019-09-08 09:30:36 327.7262 565.6201
	eosiomeetone 2019-09-07 09:30:34 328.6543 564.8974
	eosiomeetone 2019-09-06 09:30:33 298.7611 550.7432
	eosiomeetone 2019-09-05 09:30:31 327.6324 509.6212
	eosiomeetone 2019-09-04 09:30:29 328.0524 518.8298
	eosiomeetone 2019-09-03 09:30:27 321.4466 495.493
	eosiomeetone 2019-09-02 09:30:26 327.6381 497.1798
	eosiomeetone 2019-09-01 09:30:25 328.1644 498.8103
	eosiomeetone 2019-08-31 09:30:24 307.8788 500.731
	starteosiobp 2019-09-18 04:24:23 331.3979 553.9459
	starteosiobp 2019-09-17 04:14:23 328.1745 544.5849
	starteosiobp 2019-09-16 03:58:27 328.6312 537.2949
	starteosiobp 2019-09-15 03:58:23 328.3801 545.1533
	starteosiobp 2019-09-14 03.58.22 329.3878 560.8843
	starteosiobp 2019-09-13 03:56:22 331.4432 555.1423
	starteosiobp 2019-09-12 03:42:23 329.6426 556.5584
	starteosiobp 2019-09-11 03:38:23 329.4591 555.9285
	starteosiobp 2019-09-10 03:36:25 328.5671 546.5143
	starteosiobp 2019-09-09 03:36:22 328.7868 553.9193
	starteosiobp 2019-09-08 03:34:22 329.2491 547.4382
	starteosiobp 2019-09-07 03:32:24 329.471 550.2771
	starteosiobp 2019-09-06 03:30:22 328.9204 555.164
	starteosiobp 2019-09-05 03:28:22 329.0479 548.9647
	starteosiobp 2019-09-04 03:26:21 328.5712 557.3907
	starteosiobp 2019-09-03 03:26:21 328.9192 550.5592
	starteosiobp 2019-09-02 03:24:21 328.8934 571.4601
	starteosiobp 2019-09-01 03:22:21 328.3212 577.812
	starteosiobp 2019-08-31 03:22:20 328.684 579.4395

而，插入数据库的关键语句也变成了：

	sql = """
	UPDATE t_claim_info SET block_reward=%f,vote_reward=%f,all_reward=%f,reward_every_10w_eos=%f where bp_name = '%s' and  claim_time='%s'
	""" % (block_reward, vote_reward, all_reward, reward_every_10w_eos, bp_name, claim_time)

程序更加健壮。
