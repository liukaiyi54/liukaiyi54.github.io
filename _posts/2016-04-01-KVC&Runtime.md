---
layout: post
title: 用KVC来修改系统类的私有属性
date: 2016-04-01
author: 凯一
categories: iOS
---
### 用KVC来修改系统类的私有属性

#### 1. 获得私有属性

获得私有属性需要用到runtime, 代码如下, 具体每个函数用途就不解释了, 顾名思义即可.


{% highlight ruby %}

unsigned int count = 0;
Ivar *ivars = class_copyIvarList([UIPageControl class], &count);
for (int i = 0; i < count; i++) {
	const char *name = ivar_getName(ivar[i]);
	const char *type = ivar_getTypeEncoding(ivars[i]);
	NSLog(@"%s----%s", name, type);
}

{% endhighlight %}

这里是获取`UIPageControl`的所有属性列表. 运行结果如下:

{% highlight ruby %}

2016-04-01 15:17:05.283 rank[78962:7532230] _lastUserInterfaceIdiom-----q
2016-04-01 15:17:05.283 rank[78962:7532230] _indicators-----@"NSMutableArray"
2016-04-01 15:17:05.284 rank[78962:7532230] _currentPage-----q
2016-04-01 15:17:05.284 rank[78962:7532230] _displayedPage-----q
2016-04-01 15:17:05.284 rank[78962:7532230] _pageControlFlags-----{?="hideForSinglePage"b1"defersCurrentPageDisplay"b1}
2016-04-01 15:17:05.284 rank[78962:7532230] _currentPageImage-----@"UIImage"
2016-04-01 15:17:05.284 rank[78962:7532230] _pageImage-----@"UIImage"
2016-04-01 15:17:05.284 rank[78962:7532230] _currentPageImages-----@"NSMutableArray"
2016-04-01 15:17:05.285 rank[78962:7532230] _pageImages-----@"NSMutableArray"
2016-04-01 15:17:05.285 rank[78962:7532230] _backgroundVisualEffectView-----@"UIVisualEffectView"
2016-04-01 15:17:05.285 rank[78962:7532230] _currentPageIndicatorTintColor-----@"UIColor"
2016-04-01 15:17:05.285 rank[78962:7532230] _pageIndicatorTintColor-----@"UIColor"
2016-04-01 15:17:05.285 rank[78962:7532230] _legibilitySettings-----@"_UILegibilitySettings"
2016-04-01 15:17:05.285 rank[78962:7532230] _numberOfPages-----q

{% endhighlight %}

这里面有`_currentPageImage`和`_pageImage`这两个属性, 类型是`UIImage`可以让我们通过修改它们来自定义pageControl的式样.

#### 2.通过KVC来修改属性

废话不说了, 直接上代码

{% highlight ruby %}

UIPageControl *pageControl = [[UIPageControl alloc] init];
[pageControl setValue:[UIImage imageNamed:@"your_image"] forKey:@"_pageImage"];
[pageControl setValue:[UIImage imageNamed:@"your_another_image"] forKey:@"_currentPageImage"];

{% endhighlight %}

#### 3.其他

多说两句. 除了获取私有属性外, 还可以获取私有方法.

{% highlight ruby %}
unsigned int count = 0;
Method *memberFuncs = class_copyMethodList([UIPageControl class], &count);

for (int i = 0; i < count; i++) {
	SEL method_name = method_getName(memberFuncs[i]);
	const char *name = sel_getName(method_name);
	NSLog(@"%s", name);
}
{% endhighlight %}

> Method: typedef struct objc_method *Method; <br>
 Method是runtime中定义的一个宏, 关于runtime的这些基础知识, 完了再做一次整理吧.

利用这个函数, 可以添加方法. 效果和用Category一样.

{% highlight ruby %}
BOOL class_addMethod(Class cls, SEL name, IMP imp, const char *types)
{% endhighlight %}

其中

 `cls`:你要给哪个类添加方法

 `name`:你要添加的方法叫什么

 `imp`:你要添加的方法的实现叫什么, 这个方法必须至少有**self**和**_cmd**两个参数

 `types`: 一个字符数组, 用来描述方法参数的类型. 因为这个方法至少有**self**和**_cmd**两个参数, 所以第二个和第三个字符必须是 **@:** . 第一个字符是返回值得类型. 具体参考[Type Encodings](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html)

这个函数的返回值是个布尔类型，如果添加方法成功的话返回**YES**，失败返回**NO**。
