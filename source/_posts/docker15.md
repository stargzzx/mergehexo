---
title: docker | 四种网络模式
date: 2020-04-27 21:54:20
categories:
- [docker,进阶]
tags:
- docker
---
docker 和 宿主机一共有四种网络连接方式，需要我们在不同的情况下选择性使用。

<!-- more -->

- host模式，使用--net=host指定。
- container模式，使用--net=container:NAME_or_ID指定。
- none模式，使用--net=none指定。
- bridge模式，使用--net=bridge指定，默认设置。

<br/>

# 参考资料

<br/>

[Docker网络模型](https://www.jianshu.com/p/a14ebdc37386)
[Docker-----四种网络模式](https://my.oschina.net/u/4397474/blog/3538156)

<br/>

# 四种经典网络模型

<br/>

## host模式

	docker run -it --net=host ubuntu:v1 /bin/bash

Docker使用的网络实际上和宿主机一样，在容器内看到的网卡ip是宿主机上的ip。

众所周知，Docker使用了Linux的Namespaces技术来进行资源隔离，如PID Namespace隔离进程，Mount Namespace隔离文件系统，Network Namespace隔离网络等。一个Network Namespace提供了一份独立的网络环境，包括网卡、路由、Iptable规则等都与其他的Network Namespace隔离。一个Docker容器一般会分配一个独立的Network Namespace。但如果启动容器的时候使用host模式，那么这个容器将不会获得一个独立的Network Namespace，而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。

换言之，容器的IP地址即为宿主机的IP地址。所以容器可以和宿主机一样，使用宿主机的任意网卡，实现和外界的通信。

<div style="width: 50%;padding-left: 25%">

![](/images/docker/15_0.png)

</div>

当然，这种模式有优势，也就有劣势，主要包括以下几个方面：

- 最明显的就是容器不再拥有隔离、独立的网络栈。容器会与宿主机竞争网络栈的使用，并且容器的崩溃就可能导致宿主机崩溃，在生产环境中，这种问题可能是不被允许的。

- 容器内部将不再拥有所有的端口资源，因为一些端口已经被宿主机服务、bridge模式的容器端口绑定等其他服务占用掉了。

## bridge模式

	docker run -it ubuntu:v1 /bin/bash [第一个容器]

bridge模式是Docker默认的网络设置，此模式会为每一个容器分配Network Namespace、设置IP等，并将一个主机上的Docker容器连接到一个虚拟网桥上。

类似于Vmware的nat网络模式。同一个宿主机上的所有容器会在同一个网段下，相互之间是可以通信的。

在这种模式下，docker为容器创建独立的网络栈，保证容器内的进程使用独立的网络环境，实现容器之间、容器与宿主机之间的网络栈隔离。同时，通过宿主机上的docker0网桥，容器可以与宿主机乃至外界进行网络通信。

<div style="width: 50%;padding-left: 25%">

![](/images/docker/15_1.png)

</div>

- 从该网络模型可以看出，容器从原理上是可以与宿主机乃至外界的其他机器通信的。同一宿主机上，容器之间都是连接到docker0这个网桥上的，它可以作为虚拟交换机使容器可以相互通信。然而，由于宿主机的IP地址与容器veth pair的 IP地址均不在同一个网段，故仅仅依靠veth pair和namespace的技术，还不足以使宿主机以外的网络主动发现容器的存在。为了使外界可以方位容器中的进程，docker采用了端口绑定的方式，也就是通过iptables的NAT，将宿主机上的端口端口流量转发到容器内的端口上。

举一个简单的例子，使用下面的命令创建容器，并将宿主机的3306端口绑定到容器的3306端口：

	docker run -tid –name db -p 3306:3306 mysql
	在宿主机上，可以通过iptables -t nat -L -n，查到一条DNAT规则：
	DNAT tcp — 0.0.0.0/0 0.0.0.0/0 tcp dpt:3306 to:172.17.0.5:3306
	上面的172.17.0.5即为bridge模式下，创建的容器IP。
	很明显，bridge模式的容器与外界通信时，必定会占用宿主机上的端口，从而与宿主机竞争端口资源，对宿主机端口的管理会是一个比较大的问题。同时，由于容器与外界通信是基于三层上iptables NAT，性能和效率上的损耗是可以预见的。

## container模式

	docker run -it --net=container ubuntu:v1 /bin/bash [第一个容器]
	docker run -it --net=container:321de323[第一个容器的 ID] ubuntu:v1 /bin/bash [下一个容器]

多个容器使用共同的网络看到的ip是一样的。

在理解了host模式后，这个模式也就好理解了。这个模式指定新创建的容器和已经存在的一个容器共享一个Network Namespace，而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP、端口范围等。同样，两个容器除了网络方面，其他的如文件系统、进程列表等还是隔离的。两个容器的进程可以通过lo网卡设备通信。

<div style="width: 50%;padding-left: 25%">

![](/images/docker/15_2.png)

</div>

在这种模式下的容器可以通过localhost来同一网络命名空间下的其他容器，传输效率较高。而且这种模式还节约了一定数量的网络资源，但它并没有改变容器与外界通信的方式。在一些特殊的场景中非常有用，例如，kubernetes的pod，kubernetes为pod创建一个基础设施容器，同一pod下的其他容器都以其他容器模式共享这个基础设施容器的网络命名空间，相互之间以localhost访问，构成一个统一的整体。

## none模式

	docker run -it --net=none ubuntu:v1 /bin/bash

这种模式下不会配置任何网络。

在这种模式下，Docker容器拥有自己的Network Namespace，但是，并不为Docker容器进行任何网络配置。也就是说，这个Docker容器没有网卡、IP、路由等信息。需要我们自己为Docker容器添加网卡、配置IP等。

也就是说，none模式为容器做了最少的网络设置，但是俗话说得好“少即是多”，在没有网络配置的情况下，通过第三方工具或者手工的方式，开发这任意定制容器的网络，提供了最高的灵活性。

ps：

[Docker网络模型](https://www.jianshu.com/p/a14ebdc37386)

这篇文章里面还有很多很有意思的东西，感兴趣的可以尝试一下。	
