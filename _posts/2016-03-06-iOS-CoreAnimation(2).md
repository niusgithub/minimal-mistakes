---
layout: post
title: iOS--Core Animation(2)
excerpt: "iOS Core Animation 学习笔记"
modified: 2016-03-15
tags: [Core Animation, CALayer]
comments: true
image:
    feature: sample-image-4.jpg
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


# CALayer 属性

## contentsScale

contentsSclae定义了contents的像素尺寸和视图比例的大小，其默认值为1.0。contentsScale的作用并不在于放大或缩小图层，这个属性属于支持高分辨率屏幕机制的一部分。它用来判断在绘制图层的时候应该为contents创建的空间的大小，和需要显示的图片的拉伸度(在未设置contentsGravity属性的情况下)。如果contentsScale设置为1.0，将会以每点1个像素绘制图片，如果为2.0，则会以每点2个像素绘制图片，即为Retina屏幕绘制。
当用代码的方式来处理contents内的图片时，一定要设置contentsScale属性，以免图片在Retina屏幕上无法正确显示。

{% highlight css %}
layer.contentsScale = [UIScreen mainScreen].scale;
{% endhighlight %}

## maskToBounds

在默认情况下，UIView仍会绘制超出视图边界的内容或是子视图，在CALayer中也是如此。

UIView的clipToBounds属性决定是否显示超出边界的内容，其默认值是NO，在CALayer中对应的属性叫做maskToBounds。

## contentsRect

contentsRect属性允许我们在图层内显示contents的一个子域。与bounds和frame不同，contentsRect不是按点计算的，它使用了单位坐标，单位坐标制定在0到1之间，是一个相对值，表示其与contents的相对尺寸。iOS中使用了以下的坐标系统:

* 点--在iOS和Mac OS中最常见的坐标体系。点就像是虚拟的像素，也被称为逻辑像素。在非Retina设备上，一个点是一个像素，而在Retina设备上，一个点表示2*2个像素。iOS用点作为屏幕的坐标测算体系是为了在Retina和非Retina设备上有一致的视觉效果。
* 像素--物理像素坐标并不会用来屏幕布局，但是仍与图片有相对关系。UIImage是一个屏幕分辨率解决方案，所以指定点来度量大小。但是一些底层的图片表示如CGImage就会使用像素。
* 单位--对于图片大小或者是图层边界相关的的显示，单位坐标是一个方便的度量方式，当大小改变时也不需要再次调整。单位坐标在OpenGl这种纹理坐标系统中用的很多，Core Animation中也用到了单位坐标。

contentsRect的默认值是{0,0,1,1}，表示整个contents都是默认可见的，如果指定一个值，如{0,0,0.5,0.5}就会将原图的左上角到中点的矩形拉伸后显示在图层中。如果给contents设置了一个负数的原点或者大于{1,1}尺寸，图层外的像素会被拉伸以填充。

contentsRect有一项叫做`image sprite`(图片拼合)的用法。图片拼合是将一张大图一次性载入，然后根据不同的contentsRect来使用大图上的小图，相比于多次载入不同的图片，这样做能够带来很多方面的好处：内存使用，载入时间，渲染性能等等。在以前做过一个小游戏中，人物走路时的动作是由很多帧动画做成的，这样就可以读取大图然后每次取用时用contentsRect取不用的区域然后循环播放，人物就动起来了。

![Link]({{ site.url }}/images/CoreAnimation_2_1.png)

{% highlight css %}
- (void)addSpriteImage:(UIImage *)image
       withContentRect:(CGRect)rect
               toLayer:(CALayer *)layer {
    layer.contents = (__bridge id)image.CGImage;
    layer.contentsGravity = kCAGravityResizeAspect;
    layer.contentsRect = rect;
}
{% endhighlight %}

{% highlight css %}
[self addSpriteImage:image
     withContentRect:CGRectMake(0.5,0.5,0.5,0.5)
             toLayer:self.view.layer];
{% endhighlight %}

## contentsCenter

contentsCenter是一个CGRect，它定义了一个固定边框和一个在图层上可拉伸的区域。改变contentsCenter的值并不会影响到contents图的显示，除非这个图层的大小改变了，才能看到效果。默认情况下contentsCenter是{0,0,1,1},这意味着如果大小改变了，图片会均匀的拉伸开。但如果增加原点的值并减小尺寸，就会创建拉伸区域。

![contentsCenter]({{ site.url }}/images/CoreAnimation_2_2.png)

{% highlight css %}
layer.contentsCenter = rect;
{% endhighlight %}

其效果和UIIamge的－resizableImageWithCapInset:的效果十分类似，UIImage的这个方法可以设置inset为图片最中间的1*1的那个区域，这样拉伸图片可用于UITabBar或UITableViewCell的背景图。其实还有个叫.9图片也是同样的原理，不过扯远了。






