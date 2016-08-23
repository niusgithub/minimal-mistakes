---
layout: post
title: MVVM&RAC--MVVM模式
excerpt: "MVVM模式相关概念"
modified: 2016-07-27
tags: [MVVM]
comments: true
image:
    feature: sample-image-3.jpg
    credit: WeGraphics
    creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

<section id="table-of-contents" class="toc">
<header>
<h3>Overview</h3>
</header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

# 说明

最近在新开发的项目中使用了MVVM结合ReactiveCocoa的架构模式，在此记录下这个过程中的总结和体会。

# MVVM模式

## MVVM概述

MVVM是一种架构模式，它由MVC演化而来。在以往的使用MVC的过程中由于大量的页面逻辑和业务逻辑代码都混杂在ViewController中，导致ViewController变得巨大臃肿并且难以测试。而MVVM模式主要解决了MVC中ViewController过于臃肿带来的不易维护和测试的问题。

## 组成与职责

MVVM是Model－View－ViewModel的缩写。Model，View，ViewModel也就是MVVM的主要的三个组成部分。

![MVVM_1]({{ site.url }}/images/MVVM&RAC_1_1.png)

* Model --- 数据模型，数据模型这一层负责保持数据和实现业务逻辑。
* View --- 视图，视图向用户展示并与用户交互并实现UI逻辑。在iOS中由View和ViewController组成。
* ViewModel --- 视图模型，视图模型负责呈现逻辑。

如上图所示三者相互之间的持有关系为View持有ViewModel，而ViewModel持有Model。这种持有的关系都是单向的并且不可以违反，View也不能越过ViewModel去持有Model。

不同于MVC中的ViewController，MVVM中的ViewController仅负责实现与用户交互的UI逻辑，通过持有ViewModel把经过ViewModel呈现逻辑处理的Model的内容呈现给用户。ViewModel的呈现逻辑主要的工作就是将Model中的数据处理为View层中的视图View类可以直接使用的形式，而不是像MVC中在ViewController中进行呈现逻辑的处理。
在如下图的传统iOS架构MVC模式中，ViewController作为View和Model之间的协调者，View和Model分别主要通过delegate，KVO，Notification以及Block回调的方式进行沟通。

![MVC]({{ site.url }}/images/MVVM&RAC_1_2.png)

而在使用MVVM时有一项关键的技术叫作双向绑定技术。这个技术主要的功能就是在Model变化时，ViewModel会自动更新，而 ViewModel变化时，View也会自动变化。通常可以使用KVO和Notification来实现这项技术，不过目前有一项比它们更适合这项工作的框架叫作ReactiveCocoa。最近开发中的应用采用的MVVM架构大致如下图，其中的数据绑定技术就是通过ReactiveCocoa实现的。

![MVVM_2]({{ site.url }}/images/MVVM&RAC_1_3.jpg)

## 优点与缺点

MVVM模式的优点将各个逻辑分别实现，降低了耦合度并提高了重用性。一个ViewModel可以被多个View所重用。这也便于将各部分拆分各部分独立开发。由于ViewModel的出现使得针对界面的单元测试也更易于实现。
MVVM的数据绑定在带来其便利的同时也带来相应的缺点。MVVM的作者对其有一个很经典的评价：

> A criticism of the pattern comes from MVVM creator John Gossman himself,who points out overhead in implementing MVVM is "overkill" for simple UI operations. He states for larger applications, generalizing the ViewModel becomes more difficult. Moreover, he illustrates data binding in very large applications can result in considerable memory consumption.

大意就是对于过大的项目，数据绑定需要花费更多的内存。还有一点就是数据绑定使得BUG很难被调试。你看到界面异常了，有可能是你View的代码有BUG，也可能是Model的代码有问题。数据绑定使得一个位置的BUG被快速传递到别的位置，使得难以定位BUG。











