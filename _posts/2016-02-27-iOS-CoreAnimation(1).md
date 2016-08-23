---
title: "iOS--Core Animation(1)"
tags: 
  - Core Animation 
  - CALayer 
  - ARC
  - Core Foundation
---

{% include toc title="Core Animation学习笔记" icon="file-text" %}

整理、记录Core Animation的学习过程。Core Animation的前身叫做Layer Kit，它有着众多的特性而动画只是其中之一。

# Layers & Views

在iOS中所有的视图是从UIView这个基类派生而来，UIView可以处理触摸事件，支持基于Core Graphics绘图，可以做仿射变换(例如旋转或者缩放)，或者简单的类似于滑动或者渐变的动画。CALayer与UIView类似，也是一些被层级关系树管理的矩形块。同样可以包含一些内容(如图片、文本或背景色)，管理子图层的位置。他们有一些方法和属性来做动画和变换。与UIView最大不同的是CALayer不能处理用户交互。关于这一点是由两者继承的类不同决定的UIView继承自UIResponder:NSObject，而CALayer直接继承自NSObject，也因此相对来说CALayer较UIView更加轻量级一些。有关CALayer的特性可以在这篇文章中体验一下<a href = "https://www.raywenderlich.com/90488/calayer-in-ios-with-swift-10-examples">CALayer Tutorial: Getting Started</a>。
也有些特性是CALayer有而UIView未提供的:

* 阴影、圆角、带颜色边框
* 3D变换
* 非矩形范围
* 透明遮罩
* 多级非线性动画

![Layer Tree]({{ site.url }}/images/CoreAnimation_1_1.png)

每一个UIView都有一个CAlayer实例的图层属性，也就是所谓的backing layer，视图的职责就是创建并管理这个图层，以确保当子视图在层级关系中添加或者被移除的时候他们关联的图层也同样在对应的层级关系树中有相同的操作。实际上这些背后关联的图层才是真正用来在屏幕上显示和动画的，UIView仅仅是对它的一个封装，提供了一些iOS类似于触摸的具体功能，以及Core Animation底层方法的高级接口。CALayer内部维护着三个层级关系树，model layer tree或者layer tree(图层树)，presentation tree(呈现树)和render tree(渲染树)。

![CALayer Tree]({{ site.url }}/images/CoreAnimation_1_2.png)

> An app using Core Animation has three sets of layer objects. Each set of layer objects has a different role in making the content of your app appear onscreen:

> Objects in the model layer tree (or simply “layer tree”) are the ones your app interacts with the most. The objects in this tree are the model objects that store the target values for any animations. Whenever you change the property of a layer, you use one of these objects.

> Objects in the presentation tree contain the in-flight values for any running animations. Whereas the layer tree objects contain the target values for an animation, the objects in the presentation tree reflect the current values as they appear onscreen. You should never modify the objects in this tree. Instead, you use these objects to read current animation values, perhaps to create a new animation starting at those values.

> Objects in the render tree perform the actual animations and are private to Core Animation.

# CALayer 属性

## backgroundColor

用来设置layer的背景颜色，注意使用UIColor时要用CGColor转换。

{% highlight css %}
- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view setBackgroundColor:[UIColor grayColor]];

    CALayer *layer = [CALayer layer];
    layer.frame = CGRectMake(100, 100, 100, 100);
    [layer setBackgroundColor:[[UIColor blueColor] CGColor]];

    [self.view.layer addSublayer:layer];
}
{% endhighlight %}

## contents

虽然contents这个属性的类型是id，但是要想在layer上显示一张图片的话需要赋值的类型为`CGImageRef`。CGImageRef是一个指向CGImage结构的指针，CGImageRef是一个Core  Foundation对象而不是Cocoa对象，而Core Foundation对象是不支持ARC的。

{% highlight css %}
layer.contents = (__bridge_transfer id)image.CGImage;
{% endhighlight %}

补充一点关于Core Foundation对象的内存管理，由于Core Foundation对象不支持ARC所以需要手动释放。CFRetain与CFRelease用法同MRC中的retain和release。
在ARC下将Core Foundation对象转换为OC对象会用到bridge关键字。

* __bridge: 只做类型转换，不修改相关对象的引用计数，原来的Core Foundation对象在不用时，需要调用CFRelease方法。
* __bridge_retain: 类型转换后，将相关对象的引用计数加1，原来的Core Foundation对象在不用时，需要调用CFRelease方法。
* __brisge_transfer: 类型转换后，将该对象的引用计数交给ARC管理，Core Foundation对象在不用时，不再需要调用CFRelease方法。

## contentsGravity

CALayer中与UIView的contentMode(枚举)对应的属性叫做contentsGravity，它是一个NSString类型。其可选常量值如下:

* kCAGravityCenter
* kCAGravityTop
* kCAGravityBottom
* kCAGravityLeft
* kCAGravityRight
* kCAGravityTopLeft
* kCAGravityTopRight
* kCAGravityBottomLeft
* kCAGravityBottomRight
* kCAGravityResize
* kCAGravityResizeAspect
* kCAGravityResizeAspectFill

