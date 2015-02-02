---
layout: post
title: abing-pmc 工作流内核模块设计 
category: abing-pvm 
tags: [workflow, bpm, pvm]
keywords: workflow,bpm,pvm
description: 
---

## 模块介绍（naming: pmv-kernel）
    ### 
		#### pvm-kernel是抽象的节点配置及内存调度(activity-memory-schedule)框架，提供API供外部方便使用
		#### 为了保证稳定性及可维护性，pvm-kernel定位是轻量级的，仅提供单机版单线程的实现（API线程安全）； 如果需要实现分布式or多线程调度，则由外部模块提供
		#### pvm-kernel仅提供抽象的节点类型(activity)，不提供任何业务含义的节点类型（例如开始节点、结束节点、全局节点、人工节点、服务节点等）； 长久积累的流程实施经验证明，每一种的业务节点类型根据业务场景的不同，都会有不同的功能需求，因此不在pvm-kernel提供这些节点类型的默认实现
		#### 如果需要持久化，则有外部模块扩展实现

	* 用例
	* 
		* pmv-kernel提供API接口（pvm-kernel-api) ，很方便的让外部模块（external-module）调用
		* 
			* 完成节点的后续调度
			* 查找具体节点调度图（能够开启是否缓存；缓存提供本地实现，同时提供spi接口，供外部提供缓存实现）
			* 遍历具体节点调度图

		* pvm-kernel提供SPI接口（ pvm-kernel-spi) ，实现外部模块的业务功能注入
		* pvm-kernel能够抛出一些事件（pvm-kernel-event）供外部的模块监听使用，使外部模块可以利用这些事件完成一些业务功能
		* pvm-kernel能够提供一些抽象配置规范（Configuration Specification, abbreviated to pvm-kernel-cs），供外部模块自定义配置，同时结合外部模块的相应配套实现，从而实现外部模块的自定义调度
		* pvm-kernel提供具体的异常code、异常类型以及异常处理回调，能让external-module辨识出，并做相应的异常补充措施； 在工程实践中，遇到以下几个问题
		* 
			* 针对严谨的仅执行一次的机制来要求，则需要接入改异常处理子模块，做到细粒度的控制； 如果不需要仅执行一次的机制，则可以不接入异常处理，直接是粗粒度的外围捕捉即可
			* 
				* etc. 如果是多个服务节点，由于服务节点之间可能是串行同步执行，如果最后一个服务节点失败，则前面几个会重试执行； 接入了异常，则可由external-module捕捉，做到细粒度尝试，仅尝试最后一个服务节点的调度
				* 针对大数据量的流程服务，该异常子模块的接入还是非常重要的，能节省很多资源

			* 异常回调方法的问题：因为spring的事务机制设计，如果发生异常，需要当前类处理，或者采用嵌套事务； 然而嵌套事务并不一定是所有都支持（例如tddl不支持）
			* 
				* 解决方案：在pvm-kernel处理异常的时候，catch住异常，同时返回BooleanResult的富返回对象，external-module判断该值，由外部进行异常处理



	* 场景1：external-module实现自己的业务调度
	* 
		* 首先，按照pvm-kernel-cs，设计自定义节点配置，并提供相应的节点业务实现（实现相应的pvm-kernel-spi)； 然后，再通过pvm-kernel-api 启动activity-memory-schedule。 在节点的调度过程中，会抛出一些pvm-kernel-event，这些事件可以被external-module接收; 另外，在节点的调度过程中，可能会抛出异常（不是异常堆栈，而是BooleanResult富返回体类型）， 可以由external-moduel根据该值判断是否异常，并做后续的处理





