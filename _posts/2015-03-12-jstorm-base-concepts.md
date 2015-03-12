---
layout: post
title: jstorm-base-concepts
category: jstorm 
tags: [jstorm, spout, bolt, tuple, thrift]
keywords: 
description: 
---

## Topology
    拓扑图
    跨语言（使用thrift）
    ![](/public/img/topology.jpg)

## Spout
    ![](/public/img/spout_bolt.jpg)
    输入流

## Bolt
    ![](/public/img/spout_bolt.jpg)
    输入管道

## Tuple
    输入数据单元

## Worker
    处理进程

## Task
    处理线程； 每一个spout、bolt都会有至少一个task运行
    Task的数量可以用来控制spout、bolt的处理阀值

## Slot
    资源

    cpu、memory、disk、port



