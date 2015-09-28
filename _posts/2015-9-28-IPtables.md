---
layout: post
title: Iptables和Netfilter介绍
---

### 导语
在Linux生态系统中，iptables和netfilter广泛用来做包过滤，实现防火墙。如果用户对这
iptables和netfilter系统架构不熟悉，通常很难配置一个稳定可靠的防火墙。这个框架中
内部模块之间联系复杂，而且配置项语法繁琐，很容易给用户造成困难。

在OpenStack Network中，借助与Linux的namespace的隔离的网络协议栈实现了虚拟路由器，
而其中IP报文转发的规则正是基于iptables。不仅如此，虚拟路由器的端口转发也是基于
iptables。

为了让iptables和netfilter更容易理解，在下文中将阐述iptables和netfilter之间的关
联，以及如何利用他们来实现我们网络环境中的数据包的过滤和转发。

### 相关基础介绍
iptables是Linux中最常见的防火墙实现。iptables工作在用户态，与Linux内核网络协议栈
的组件netfilter协作，设置相应的检查点实现报文检查和过滤。

无论incoming或outgoing每一个网络协议栈的报文都要经过这些检查点的处理，应用程序能
够在这些检查点配置相应的规则。Linux内核模块协助iptables在这些检查点对报文检测并执行
配置好的动作，使得数据流能够按照防火墙设置的条件进行处理。

### Netfilter报文检查
Netfilter在网络协议栈中定义了五个应用程序能够进行相关规则配置的检查点。但数据报文按照
顺序通过网络协议栈时，它们将触发应用程序在这些地方配置的规则。数据报文对规则的触
发取决于报文自身的属性，包括报文的流向，他们的源和目的，以及前一个检查点的动作。

这五个检查点分别是：

 * NF_IP_PER_ROUTING: 所有进入网络协议中栈的报文都将首先触发这个检查点，在这里决
 定数据报文往那里发送
 * NF_IP_LOCAL_IN: 如果数据报文是发往系统内部，将在路由之后触发这个检查点
 * NF_IP_FORWARD: 如果incoming的数据报文是发往其他主机，将在路由之后触发这个
 检查点
 * NF_IP_LOCAL_OUT: 任何从本地发往外部的报文在进入网络协议栈之后都将首先出发这个
 检查点
 * NF_IP_POST_ROUTING: 所有从本地发往外部或者经本机转发的数据包在路由之后发出去之间会
 触发这个检查点

由于只有netfilter只有五个检查点，可能有多个内核模块同时在一个检查点按照自己的
需求配置了规则，所以他们在配置规则的时候需要确定一个优先级，用来在报文到达是
能够有一个顺序决定运用哪条规则的动作转发或丢弃报文，并且每一个报文都需要有一个
确定的规则来处理。
一条规则叫做chain，几条规则一起组成table。

### iptanles中的tables
iptables中定义了几种tables，他们分别是多个从不同的关注点对报文处理的规则chain的集合。

#### filter table

#### nat table

#### mangle table

#### raw table

#### security table

### table中的chains

