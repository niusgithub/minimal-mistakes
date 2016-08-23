---
title: "iOS--Core Animation(3)"
tags: 
  - Core Animation 
  - CAShapeLayer
---

{% include toc title="Core Animation学习笔记" icon="file-text" %}

## CAShapeLayer

### 概述
CAShapeLayer是一个通过矢量图形而不是bitmap来绘制的layer子类。
CAShapeLayer可以用来绘制所有能通过CGPath来表示的形状。这个形状不一定要闭合，图层路径也不一定要不可破，事实上可以在一个图层上绘制好几个不同的形状。CAShapeLayer属性是CGPathRef类型。

### 优点
CAShapeLayer相比CALayer有如下优点:

* 渲染快速。CAShapeLayer启用了硬件加速，绘制同一图形时会比用Core Graphics快很多。
* 高效利用内存。一个CAShapeLayer不需要像普通CALayer一样创建一个寄宿图形，所以无论多大，都不会占用太多内存。
* 不会被图层边界裁剪。一个CAShapeLayer可以在边界之外绘制。你的图层路径不会像在使用Core Graphics的普通CALayer一样被裁减。
* 不会出现像素化。当CAShapeLayer做3D变换时，它不像一个有寄宿图的的普通Layer一样像素化。

### 圆角

可以用UIBezierPath来创建图层路径，这样就不用考虑人工释放CGPath了。

{% highlight css %}

CGRect rect = CGRectMake(50, 400, 100, 100);
CGSize radii = CGSizeMake(20, 20);
UIRectCorner corners = UIRectCornerTopLeft | UIRectCornerTopRight;

UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:rect byRoundingCorners:corners cornerRadii:radii];

CAShapeLayer *shapeLayer = [CAShapeLayer layer];
shapeLayer.strokeColor = [UIColor orangeColor].CGColor;
shapeLayer.fillColor = [UIColor redColor].CGColor;
shapeLayer.lineWidth = 5;
shapeLayer.lineJoin = kCALineJoinRound;
shapeLayer.lineCap = kCALineCapRound;
shapeLayer.path = path.CGPath;

[self.view.layer addSublayer:shapeLayer];

{% endhighlight %}


## CATextLayer

### 概述

CATextLayer是CALayer的子类，它以layer的形式包含了UILabel几乎所有的绘制特性，并且提供了一些新的特性。

### 与UILabel的比较

对于多行且有行数限制的情况，UILabel有numberOfLines属性，CATextLayer则没有相应的属性，只有wrapped属性命令内容自动换行。CATextLayer没有对戳断字符的处理方式，虽然CATextLayer有truncationMode属性，但是设置了没有任何作用。
CATextLayer使用了Core Text，并且渲染速度很快。UILabel使用Core Graphics直接向图层中绘制字符串，这个操作比较繁琐。UILabel的底层实现如下图：

iOS6及之前的实现

![iOS_CA_3_1]({{ site.url }}/images/iOS_CA_3_1.jpg)

iOS7的实现

![iOS_CA_3_2]({{ site.url }}/images/iOS_CA_3_2.jpg)

由于渲染方式的不同，CATextLayer和UILabel的文本行距和字距是不同的，有细微的差别。

### 使用

实现一个继承子UILabel的子类，然后在该子类的实现中重写`+ layerClass`方法，用CATextLayer来替换原layer。UIView会在初始化的时候调用`+ layerClass`方法，然后根据返回值来创建宿主图层。

{% highlight css %}

+ (Class)layerClass {
    return [CATextLayer class];
}

{% endhighlight %}


## CATransformLayer

CATransformLayer不会将图层所有的子图层平面化。


## CAGradientLayer

CAGradientLayer用来生成两种或者更多颜色平滑渐变，CAGradinetLayer启用了硬件加速.

### 属性

colors数组表示平滑渐变的颜色。

startPoint和endPoint决定了渐变的的方向。这两个是以单位坐标系进行定义的，如左上角是(0,0),右下角是(1,1)。

locations数组必须和colors大小相同，用NSNumber来定义。它用来在多重渐变中表示每种颜色的位置。locations也是用单位坐标定义的，0.0表示渐变开始，1.0表示结束。


## CAReplicatorLayer
