---
title: win10更新后 chrome内核浏览器总是打开网页一直加载 甚至打不开 解决方法
date: 2018-10-28 20:07:17
categories:
- 电脑相关
- chrome
tags:
- chrome
- computer
- win10
- skill
---
如题，坑比 win10。

<!-- more -->

升级win10之后如果出现chrome内核的浏览器网页总是打不开 打开很慢 而ie和edge是可以正常访问的 用这个方法可以 我弄了几天终于 搞好了 我直接转载过来了 

近期，工程师收到大量反馈360浏览器，极速模式无法打开网页，兼容模式无此问题。

IE浏览器和Edge都正常。用断网急救箱、重置DNS、LSP修复后问题依然存在。

下面的解决办法仅限于Win10有如上现象的情况。

解决办法一

1. 删除HKEY_CURRENT_USER\Software\Microsoft\SystemCertificates\Root\ProtectedRoots 

2. 删除HKEY_CURRENT_USER\Software\Microsoft\SystemCertificates\Root\Certificates

3. 删除HKEY_CURRENT_USER\Software\Microsoft\SystemCertificates\Root

可以参照以下步骤1、2、3、4操作：

步骤1、打开注册表，点开始菜单，点运行，输入regedit.exe后回车

找到注册表键值HKEY_CURRENT_USER\Software\Microsoft\SystemCertificates\Root\ProtectedRoots 

右键点权限，（如果有弹窗，点重新排序，不是每台电脑都会遇到），把所有用户名（下图4个）都设置成完全控制权限，点确定，右键ProtectedRoots，点删除，如下图，如果还不行，看步骤2

![](/images/computer/2_0.png)

我的电脑在这一步的时候就已经可以删除了，但是为了照顾广大集体，我再给出其它方法解决不能按照上述步骤删除的步骤。

步骤2、如果无法删除，需要点控制面板，点“账户”，点“改用本地账户登陆”，然后注销，再按照上面的步骤删除，如果还不行，看步骤3

（之后在登陆账户即可，重启电脑后也会正常）

![](/images/computer/2_1.png)

步骤3、如果出现“无法保存对ProtectedRoots 权限所作的更改”，主要原因是所有者不对。右键ProtectedRoots点“权限”，点“高级”，找到“所有者”，如下图所有者是Administrator，点右边的“更改”，出现界面后，点高级，点立即查找，找到本地账户，比如“张三”，一路点确定，然后再删除试试，如果还不行，看步骤4

![](/images/computer/2_2.png)

步骤4、如果所有者对了，完全控制也可以设置好了，还是无法删除，可能是键值有父对象。右键ProtectedRoots点“权限”，点“高级”点“禁用继承”，弹出框选第一个，点确定，变成“无”，然后再删除

![](/images/computer/2_3.jpg)

成功删除ProtectedRoots后，再删除Certificates和Root，方法一样，成功删掉后关闭注册表，删掉后会生成正常权限的键值，不影响使用。




