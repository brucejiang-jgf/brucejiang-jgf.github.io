---
layout: post
title: abing-pvm-kernel design
category: abing-pvm 
tags: [workflow, workflow-pvm]
keywords: workflow,bpm,pvm
description: 
---

> 工作流架构采用分层体系设计， 鉴于工作流的业务复杂性、调度固定性（调度模式基本固定几种模式）； 因此提出工作流内核层（该层包括两大模块：abing-pvm-kernel/abing-pvm-framework）, abing-pvm-kernel主要抽象了工作流的节点调度模型，完成抽象的节点调度。 


## abing-pvm-kernel description 

* 抽象的节点配置(activity-configuration)及内存调度(activity-memory-schedule)，提供API供外部方便使用
* 为了保证稳定性及可维护性，abing-pvm-kernel定位是轻量级的，提供单机版单线程的实现（API线程安全）； 如果需要实现分布式or多线程调度，则由外部模块(external-module)提供
* 提供抽象的节点模型(activit-pattern)，不提供任何业务含义的节点类型（例如开始节点、结束节点、全局节点、人工节点、服务节点等）； 长久积累的流程实施经验证明，每一种的业务节点类型根据业务场景的不同，都会有不同的功能需求，因此不在abing-pvm-kernel提供这些节点类型的默认实现
* 如果需要持久化，则有external-module扩展实现

## supports 

* 提供API接口（abing-pvm-kernel-api) ，很方便的让external-module调用
    * 推动activity-memoery-schedule
    * 查找具体节点调度图(activity-diargram)
    * 遍历activity-diagram
    * 查找activity-configuration

* 提供SPI接口（ abing-pvm-kernel-spi) ，实现external-module的业务功能注入
* 能够抛出一些事件（abing-pvm-kernel-event）供externla-module监听使用，使外部模块可以利用这些事件完成一些业务功能
* pvm-kernel能够提供一些抽象配置规范（Configuration Specification, abbreviated to pvm-kernel-cs），供外部模块自定义配置，同时结合外部模块的相应配套实现，从而实现外部模块的自定义调度
* pvm-kernel提供具体的异常code、异常类型以及异常处理回调，能让external-module辨识出，并做相应的异常补充措施； 在工程实践中，遇到以下几个问题
    * 如果是多个服务节点，由于服务节点之间可能是串行同步执行，如果最后一个服务节点失败，则前面几个会重试执行； 接入了异常，则可由external-module捕捉，做到细粒度尝试，仅尝试最后一个服务节点的调度
    * 针对大数据量的流程服务，该异常子模块的接入还是非常重要的，能节省很多资源

    * 异常回调方法的问题：因为spring的事务机制设计，如果发生异常，需要当前类处理，或者采用嵌套事务； 然而嵌套事务并不一定是所有都支持（例如tddl不支持）
    * 
    * 解决方案：在pvm-kernel处理异常的时候，catch住异常，同时返回BooleanResult的富返回对象，external-module判断该值，由外部进行异常处理



## 场景1：external-module实现自己的业务调度

* 首先，按照pvm-kernel-cs，设计自定义节点配置，并提供相应的节点业务实现（实现相应的pvm-kernel-spi)； 然后，再通过pvm-kernel-api 启动activity-memory-schedule。 在节点的调度过程中，会抛出一些pvm-kernel-event，这些事件可以被external-module接收; 另外，在节点的调度过程中，可能会抛出异常（不是异常堆栈，而是BooleanResult富返回体类型）， 可以由external-moduel根据该值判断是否异常，并做后续的处理





