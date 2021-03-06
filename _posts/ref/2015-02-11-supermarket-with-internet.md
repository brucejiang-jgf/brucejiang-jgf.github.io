---
layout: post
title:  从超市收银故事看互联网技术 
category: reference 
tags: [architecture, internet]
keywords: architecture, internet 
description: 
---

引用自[毕玄](http://bluedavy.me/?p=23)

> 通过一个超市的运营讲解，来映射互联网技术

## 超市讲解

一家小超市，一个收银员，同时还兼着干点其他的事情，例如打扫卫生、摆货；

来买东西的人多起来了，排队很长，顾客受不了，于是加了一个收银台，雇了一个收银员；

忙的时候收银员根本没时间去打扫卫生，超市内有点脏，于是雇了一个专门打扫卫生的；

顾客不断的增加，超市也经过好几次装修，由以前的一层变成了两层，这个时候所做的事情就是不断的加收银台、收银员和打扫卫生的人；

在超市运转的过程中，老板发现一个现象，有些收银台很长的队，有些收银台人不多，了解了下原因是收银台太多了，顾客根本看不到现在各个收银台的状况，对于这个现象，一种简单的方法就是继续加收银台，一方面超市没地方可加收银台了，另一方面作为老板当然不需要雇太多的人，于是开始研究怎么样能让顾客了解到收银台的状况，简单的加了一个摄像头和一个大屏幕，在大屏幕上显示目前收银台的状况，这样后发现基本上排的队的长度是差不多了；

排队长度差不多后，又出现了一个现象，就是有些收银台速度明显比其他的慢，了解了下原因是排在有些收银台的顾客买的东西特别的多，于是又想了一招，就是设立专门的10件以下的通道，这样买东西比较少的顾客就不用排太长的队了，这一招施展后，顾客的满意度明显提升，销售额也好了不少，后来就继续用这招应对了团购状况、vip状况；

在解决了上面的一些烦心事后，关注到了一个存在已久的现象，就是白天收银台很闲，晚上则很忙，从节省成本上来考虑，于是决定实行部分员工只用晚上上班的机制，白天则关闭掉一些收银台，顾客仍然可以通过大屏幕看到哪些收银台是关闭的，避免走到没人的收银台去了，实行这招后，成本大大降低了。

这个生活中的例子以及其解决的方法，其实和互联网网站发展过程中的一些技术是非常类似的，只是在技术层面用其他名词来表达了而已，例如有集群、分工、负载均衡、根据QoS分配资源等。

## 集群

所有收银员提供的都是收银功能，无论顾客到哪一个去都可完成付款，可以认为所有的收银员就构成了一个集群，都希望能做到顾客增加的时候只用加收银员就行，在现实生活中有场地的限制，而在互联网应用中，能否集群化还受限于应用在水平伸缩上的支撑程度，而集群的规模通常会受限于调度、数据库、机房等；

## 分工

收银员、打扫卫生的分开，对于互联网而言，这是一项重要而复杂的技术，没有现实生活中这么简单，涉及到的主要有按功能拆分系统以及数据库等，如何拆分以及拆分后如何交互是两个需要面临的挑战，因此会有高性能通信框架、SOA平台、消息中间件、分布式数据层等基础产品的诞生；

## 负载均衡

让每个收银台排队差不多长，设立小件通道、团购通道、VIP通道等，这些可以认为都是集群带来的负载均衡的问题，从技术层面上来说，实现自然也比生活中复杂很多；

## 根据QoS分配资源

部分员工仅晚上上班的机制，这个对于生活中来说做到不难，对互联网应用而言，还真是件复杂而且极度具备挑战的事。

参照生活中的例子来说，在面对用户增长的情况下，想出这些招应该不难，不过要掌握以上四点涉及的技术已经相当复杂了，而且互联网中涉及的其他的很多技术还没在这个例子中展现出来，例如缓存、CDN等优化手段；运转状况监测、功能降级、资源劣化、流控等可用性手段，自建机房、硬件组装等成本控制手段，因此构建一个互联网网站确实还是不容易的，技术含量十足，当然，经营一家超市也不简单，如果有同学能继续把涉及的技术补充到这个超市的故事就好了，例如还有超市进货的问题、摆货的问题等，欢迎接龙，:)。


## twitter的另一个超市策略

[引用毕玄的另外一篇文章](http://bluedavy.me/?p=25)

> 实现了公平算法， 只要存在多个队列，肯定会存在不公平，因此合并到一个大队列，保证绝对公平

3月30日Twitter在其engineering blog上写了一篇Unicorn Power的blog：http://engineering.twitter.com/2010/03/unicorn-power.html，写的挺经典的，按我的理解来讲下这篇blog吧，如有错误，请帮忙纠正，:)。
@sandofsky和@netik在blog中首先举了个经典的超市收银的问题，通常超市会有多个收银台，在高峰期通常每个收银台前都会排队，而这个时候如果某个顾客买的东西特别多，或者某个顾客买东西时遇到麻烦的话，就会导致队伍后面的人要排很久的队，而其实此时其他的队有可能处理的更快很多，这种情况会造成的问题是有些人先排的队，但却等了更长很多的时间，于是有家叫Fry的超市，采取了另外一种方法来进行收银，就是让所有的顾客在同一个地方排队，然后三十个收银员进行收银，每完成一个时就按下信号灯，表示可以去该收银台进行付款了，这样之后的好处是减少了由于个别顾客造成一堆人等待很长时间的现象。
从超市的例子脱离出来，来看看twitter的状况，之前twitter运行在apache和mongrel上，通过mod_proxy_balancer来做负载均衡，每台业务处理器能处理一定的请求，处理不过来的就排队，于是就有可能出现有些用户不幸的在处理比较慢的机器中排队，在排了长时间后可能会出现的是超时，而另外一个问题就是队列排不下了，就只能丢弃请求了，但其实也许这个时候如果请求都合理的分配的话，可能能够让超时或丢弃请求的现象降低，于是twitter的工程师开始寻求改变。
在去年11月，twitter开始尝试Unicorn，unicorn增加了一个类似Fry超市的拉的方式，twitter首先在mobile.twitter.com上进行了尝试，到了今年1月，已经在twitter.com上也采用了unicorn，在采用Unicorn了后，twitter惊喜的发现，它们明显的降低了30%的请求延时，并且还很明显的降低了CPU使用率。
其他的是一些监测方面的介绍，就不在此分析了，感兴趣的同学可以看看原作。

这篇blog给了我挺大的感触的，在标准的通过硬件负载设备访问real server时，尤其典型的通过硬件负载设备访问后端tomcat的情况中，会出现的现象就和twitter的现象基本一样了，假设一个tomcat只能处理100个请求，为了避免用户太高的失败率，必然会给一个较大的等待队列，这个时候就会出现如果请求处理慢，就导致了排在队中的用户要等待很久，从而造成超时或请求丢弃，如果能采取类似Fry的方法，那么就能够做到按照用户发起请求的顺序来进行处理了，而不会因为个别用户请求处理慢就造成排队的用户超时和请求被拒绝的现象，这确实是一个合理发挥server请求处理能力的负载均衡不同角度的策略，但这里其实有个要求，就是负载设备或软件负载方案要能支持排队功能，这对传统的方案还是有一定挑战的（例如最少连接数），但无论如何，其能带来的效果还是明显的。

ps: 不过如果一家超市只有一个队的话，那得多长呀…有些时候可能会给顾客造成心理压力，搞不好就不买了，呵呵，当然，应用到软件领域倒是没这个问题，反正用户也不知道现在排的队有多长，:)
