---
title: eosvoter|实现生成xls并自动下载|币币生息
date: 2019-09-23 21:07:10
categories:
- [项目经历,中型,eosvoter]
tags:
- postman
- eosvoter
- 币币生息
password: eosvotereosvoter0
abstract: eosvoter 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---

这里是陈哥给我安排的任务。

<!-- more -->

<br/>

# 项目环境 

<br/>

- MACbook
- python3.7
- flask

<br/>

# 项目需求

<br/>

想要实现将数据库的数据生成xls文件，并且可以下载。

<br/>

# postman的用法

<br/>

这个项目的请求是通过 postman 来发送请求的，有两个蛮有意思的地方。

## 存储cookie

这个项目有点就是需要先登录，如果不登陆的话就会访问不了其它页面。

经过测试，postman有存储cookie的功能，也就是登上去之后，能和浏览器一样保存用户的登录信息或者说是cookie。

登录的请求时 post，其传参方式如下图：

![](/images/bbsx/3_0.png)

这里尤为注意的是 post 请求填写参数的位置在 body 的 form-data 中，而不是 params。

当传参成功后，我们的postman就有了用户的cookie。

## 利用postman下载

如果要利用 postman 下载的话，就要将右侧的 send 切换到 send and download。

![](/images/bbsx/3_1.png)

![](/images/bbsx/3_2.png)

<br/>

# 代码书写

<br/>

## 技巧寻找数据的自身BUG

将 sql 文件进行注入完成后。

进入 mysql 环境后，然后直接输入 sql 的所在文件位置就好了

	mysql> ~/test.sql

其中，我们需要用 postman 访问（当然，在访问这个之前，要先用postman登录）

	http://127.0.0.1:5000/eosvoter/v1/vote/statistics

这个访问的 url 调用的是下面的方法

```python
@app.route("/eosvoter/v1/vote/statistics", methods=['GET'])
@login_required
def vote_statistics():
    bp_name = request.args.get('bp_name')
    bps = Vote.get_bp_list(current_user.user_name)
    if bp_name and bp_name not in bps:
        response = Response.fail(100002, "数据异常", [])
        return jsonify(response.to_json())
    ret, data = voteService.get_vote_statistics_by_bps(current_user.user_name)
    if not ret:
        response = Response.fail(100001, "数据为空", [])
        return jsonify(response.to_json())
```

但是，返回的为 500。

经过很长时间的排查后，发现原来是忘了传参数了

	bp_name = request.args.get('bp_name')

上面的那句话的意思是根据url的bp_name获取数据，由于没有导致出现服务器500的错误，所以，修改url为

	http://127.0.0.1:5000/eosvoter/v1/vote/statistics?bp_name=atticlabeosb

传入这个后，发现返回的是数据为空，也就是没有ret。
我们追踪

	ret, data = voteService.get_vote_statistics_by_bps(current_user.user_name)
		中的get_vote_statistics_by_bps方法

进入到下面的代码（截取了部分）：

```python
代码没有截全，只是部分
@staticmethod
def get_vote_statistics_by_bps(bp_name):
	vote_obj = modelVote()
	period = vote_obj.get_max_period()
	vote_bps = vote_obj.get_vote_statistics_by_bp(bp_name, period)
	if not vote_bps:
		return False, None
	vote_static = []
	for voteItem in vote_bps:
		bps = voteItem[3]
		bps = json.loads(bps)
		tmp_bp_v = Vote.bps_convert(bps)
		all_total_eos = voteItem[2]
		back_all_total_eos = 0
		vote_record = vote_obj.get_vote_statistics(voteItem[1], voteItem[0], period)
```

其中比较重要的是

	vote_bps = vote_obj.get_vote_statistics_by_bp(bp_name, period)

然后继续跟踪

	get_vote_statistics_by_bp

进入到下面的代码：

```python
    def get_vote_statistics_by_bp(self, from_bp, period):
        sql = "select from_bp,to_bp,vote_eos,bps,bp_accounts from t_vote_statistics where from_bp = %s and period=%s"
        return self.db.get_all(sql, [from_bp, period])
```

发现这句话出了带 from_bp 的参数外，还携带 period。

然后，我们输出了一下传递的period，发现为空，所以，这个数值应该是网络获取的，不过，就目前来说，我们测试开发要写死。

调用数据库得知，这个period是25或者26。所以，我取了25，上面的那句话就变成了：

	vote_bps = vote_obj.get_vote_statistics_by_bp(bp_name, period=25)

这里还有一点需要注意的是，下面还有一句话也需要写死period，要不然就会使得数据返回不全，这个数据不全的问题，浪费我一天的时间，才找到原因。

![](/images/bbsx/3_3.png)

到了现在才解决数据回传的所有问题，那么下面就是开发生成xls了（现实中的流程并不是这样的，我是先做完生成xls的，然后处理回传数据，最后根据回传数据填入xls，因为一开始我根本不知道回传数据的问题出现在哪里）

另外，还有一点需要说明的是

	http://127.0.0.1:5000/eosvoter/v1/vote/statistics?bp_name=atticlabeosb

这个网址传递的参数，也就是 bp_name ,必须要在 t_account的数据库表中存在，要不然不能返回数据，具体那里需要写死我没找到，我也不想找了。。。

## 生成 xls

这一章节，我想进行两步，一个是生成xls，另一个是向xls中填写数据。

说实话，被文档坑了一下。。。