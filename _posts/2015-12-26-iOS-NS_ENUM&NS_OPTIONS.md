---
title: "iOS--枚举类型的使用 NS_ENUM和NS_OPTINOS"
tags: 
- iOS 
- NS_ENUM
- NS_OPTIONS
---

{% include toc title="使用NS_ENUM和NS_OPTINOS来定义枚举类型" icon="file-text" %}
	
枚举类型在开发中经常用来表示状态／状态码和选项。

## 使用OC对象类型

Objective-C作为C的超集，自然也可以通过enum关键字来定义枚举，不过虽然是超集但是OC并不将C的基本类型视为第一级的对象。所以在开发中中应该使用OC的对象，比如用NSInteger代替int。在定义枚举类型时用NS_ENUM而不是enum，不过NS_ENUM和NS_OPTIONS并不是OC的对象而是OC中定义的宏。在官方文档中关于二者是如下定义的：

> The NS_ENUM and NS_OPTIONS macros provide a concise, simple way of defining enumerations and options in C-based languages. These macros improve code completion in Xcode and explicitly specify the type and size of your enumerations and options. Additionally, this syntax declares enums in a way that is evaluated correctly by older compilers, and by newer ones that can interpret the underlying type information.

这么做的好处就是，这些宏具备向后兼容能力(backward compatibility),如果目标平台的编译器支持新标准就用新式语法，否则用旧式。用这些宏来定义枚举类型时可以指定用于保存枚举值的底层数据类型，如

{% highlight objc %}
typedef NS_ENUM(NSInteger, MyState) {
    MyStateValue1,
    MyStateValue2,
    MyStateValue3
};
{% endhighlight %}

## 选项可组合的的枚举类型

这种枚举类型常见于某些动画效果实现的代码和视图效果中，特点是各个选项之间可以通过`按位或操作符`来组合（｜）,例如
{% highlight objc %}
enum UIViewAutoresizing {
    UIViewAutoresizingNone                 = 0,
    UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,
    UIViewAutoresizingFlexibleWidth        = 1 << 1,
    UIViewAutoresizingFlexibleRightMargin  = 1 << 2,
    UIViewAutoresizingFlexibleTopMargin    = 1 << 3,
    UIViewAutoresizingFlexibleHeight       = 1 << 4,
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5,
}
{% endhighlight %}

相当于

{% highlight objc %}
UIViewAutoresizingFlexibleLeftMargin    00000001,
UIViewAutoresizingFlexibleWidth         00000010,
UIViewAutoresizingFlexibleRightMargin   00000100,
UIViewAutoresizingFlexibleTopMargin     00001000,
UIViewAutoresizingFlexibleHeight        00010000,
UIViewAutoresizingFlexibleBottomMargin  00100000,

UIViewAutoresizingFlexibleWidth ｜ UIViewAutoresizingFlexibleHeight 00010010
{% endhighlight %}

根据代码是否需要按C++模式编译选择使用NS_OPTIONS还是NS_ENUM，因为在C++模式下通过按位或来组合的NS_ENUM定义的枚举类型会出现隐式转换的的错误，所以在C++模式下应该使用NS_OPTIONS来省去类型转换操作，在凡是需要按位或操作来组合的枚举类型都应使用NS_OPTIONS，若是不需要则用NS_ENUM。
