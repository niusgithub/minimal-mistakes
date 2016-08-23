---
layout: post
title: iOS--Category & Extension
excerpt: "categroy和extension的使用及两者的异同"
modified: 2016-03-09
tags: [iOS, Category, Extension]
comments: true
image:
  feature: sample-image-5.jpg
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
	
平时category用的比较多，前几天看到了一个奇怪的category，一开始认为是category，但总觉得不对啊，它怎么增加了一个变量呢，但又不是extension。后来才知道是使用runtime库的 objc_setAssociatedObject / objc_getAssociatedObject来动态生成category成员变量，本篇先复习一下category和extension的基本知识。

## Category

Category用于在不修改类的代码的情况下向一个已经存在的类中添加`方法`，无论这个类的源代码是否可见，并且被添加的方法会被其子类继承。除了扩展现有类的定义之外，category还可以用于修改现有类的定义，通过定义与原类中同名的方法来覆盖原类中的实现，这个特性可以用于修改某些无法修改源码的而源码又有错误的第三方库的方法。category可以访问类的私有成员但是`不可以定义新的成员`(不是绝对不行)。被添加的方法使用时和类的原始方法并无差异。

Category在声明时必须import其所扩展的类的头文件（interface file），通常的category文件的命名习惯是用添加category的类的类名加上categroy的名字`SystemClass+CategoryName.h`。

{% highlight css %}
#import "SystemClass.h"

@interface SystemClass (CategoryName)
// method declarations
@end
{% endhighlight %}

如果是为私有类添加category，可以在类的@implementation块前直接声明。

{% highlight css %}
#import "MyClass.h"

@interface MyClass (PrivateMethods)
// method declarations
@end

@implementation MyClass
// method definitions
@end
{% endhighlight %}

通常的category的方法的实现在`SystemClass+CategoryName.m`中定义

{% highlight css %}
#import "SystemClass+CategoryName.h"

@implementation SystemClass ( CategoryName )
// method definitions
@end
{% endhighlight %}

## Extension

extension和category很像，但是和category不同的是`可以添加成员变量`但只可以为拥有编译时源码的类添加extension，例如不能为framework中的类，如Cocoa／Cocoa Touch中的NSString类添加extension。官方文档的表述是

> it can only be added to a class for which you have the source code at compile time (the class is compiled at the same time as the class extension)

extension的声明和category很像，由于圆括号中没有命名，所以extension通常也被称为匿名category。

{% highlight css %}
@interface ClassName ()

@end
{% endhighlight %}

extension可以添加成员变量。通常定义在extension中的属性和变量被视为private而在类的@interface中的则是public，可以通过这个特性来隐藏类的私有信息。

{% highlight css %}
@interface XYZPerson ()
@property NSObject *extraProperty;
@end
{% endhighlight %}

此外在extension中声明的方法必须在类的主implementation中实现，而category并不强求这一点。


## 一点思考

category和extension在添加成员和方法上各自的特性应该是和OC的runtime机制以及OC的meta类模型相关，通过runtime可以在运行时动态地向类的响应方法的列表中添加方法，(但类的成员的地址偏移量只能在编译时确定?)。对runtime还不熟悉，关于这点会在对runtime有更深入了解后再写。