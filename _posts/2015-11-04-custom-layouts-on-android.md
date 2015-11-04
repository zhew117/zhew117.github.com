---
layout: post
title: "Custom Layouts on Android(翻译)"
description: ""
category: 
tags: []
---
{% include JB/setup %}

![](/assets/layouts-post.png)


[##原文地址](http://lucasr.org/2014/05/12/custom-layouts-on-android/)


If you ever built an Android app, you have definitely used some of the built-in layouts available in the platform—RelativeLayout, LinearLayout, FrameLayout, etc. They are our bread and butter for building Android UIs.

如果你曾经做过Android开发，那么你肯定用过其平台当中的一些built—in布局－相对布局，线性布局，框架布局等等，他们是我们做Android UI的面包和奶油。

The built-in layouts combined provide a powerful toolbox for implementing complex UIs. But there will still be cases where the design of your app will require you to implement custom layouts.

built—in 布局联合提供一个强大的工具盒来实现复杂的UI，但是总有一些情况下你的app的设计将会需要你使用自定义布局。

There are two main reasons to go custom. First, to make your UI more efficient—by reducing the number of views and/or making faster layout traversals. Second, when you are building UIs that are not naturally possible to implement with stock widgets.

有两个主要愿意使用自定义，第一，减少使用view的次数或者更快的布局遍历让你的UI更加高效，第二，当你正在构建的UI不能自然的被原生控件实现。

In this post, I will demonstrate four different ways of implementing custom layouts and discuss their respective pros and cons: composite view, custom composite view, flat custom view, and async custom views.

在这片文章中，我将会是示范四种方式去实现自定义布局并且讨论他们的优缺点，分别是：composite view, custom composite view, flat custom view, and async custom views.

The code samples are available in my [_android-layout-samples_](https://github.com/lucasr/android-layout-samples) repo. This app implements the same UI with each technique discussed here and uses [_Picasso_](http://square.github.io/picasso/) for image loading. The UI is a simplified version of Twitter app’s stream—no interactions, just the layouts.

你们可以在我们的android-layout-samples 仓库中看到代码的样本，这个app用每一个我们之前说的技术实现了相同的UI，并且用
Picasso实现图片的加载。这个UI是推特应用stream—no交流的简化版本，只是布局。

Ok, let’s start with the most common type of custom layout: composite view.

好的，让我们开始最常见的一种自定义布局：composite view

###Composite View

This is usually your starting point. Composite views (also known as compound views) are the easiest way of combining multiple views into a reusable UI component. They are very simple to implement:

这通常是你的起始点，Composite views通常也叫compound views 他们是最简单的方式将多个view组合成为一个可服用的UI零件。他们非常简单就可以实现：

1.Subclass one of the built-in layouts.
2.Inflate a merge layout in the constructor.
3.Initialize members to point to inner views with findViewById().
4.Add your own APIs to query and update the view state.

1.继承一个built—in布局。
2.填充一个merge laytou到构造方法中。
3.用findViewById()初始化所有成员指向内部view
4.添加你自己的API用来查询或者更新view的状态。

[_TweetCompositeView_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/widget/TweetCompositeView.java) is a composite view. It subclasses RelativeLayout, inflates [_tweet_composite_layout.xml_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/res/layout/tweet_composite_view.xml) into it, and exposes an update() method to refresh its state in the [_adapter_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/adapter/TweetsAdapter.java#L85). Simple stuff.

TweetCompositeViewcode 是一个composite view，继承了RelativeLayout，用tweet_composite_layout.xmlcode填充进去，暴露一个update()方法在adaptercode去刷新其状态，很简单。

###Custom Composite View

TweetCompositeView will likely perform fairly well in most situations. But, for sake of argument, suppose you want to reduce the number of child views and make layout traversals a bit more efficient.

TweetCompositeView在大部分的情况下都表情的不错，但是，为了讨论的目的，假设你想减少子view的数量而且让布局遍历的更加高效。

Although composite views are simple to implement, using general-purpose layouts has a cost—especially with complex containers like LinearLayout and RelativeLayout. As platform building blocks, they have to handle tons of layout combinations and might have to measure child views multiple times in a single traversal—LinearLayout‘s layout_weight being a common example.

尽管composite views容易去实现，通过使用多用途的布局可以减少复杂的容器，比如线性布局和相对布局。平台在构建块的时候，会掌控及其多的布局合并或许还需要计算子时图许多次在一个traversal—LinearLayout的布局成为通用例子。 

You can greatly optimize your UI by implementing a custom logic for measuring and positioning child views that is specially tailored for your app. This is what I like to call a custom composite view.

为你的App通过测量和指定来实现自定义逻辑可以充分利用你的UI，所以我喜欢叫custom composite view

A custom composite view is simply a composite view that overrides onMeasure() and onLayout(). So, instead of subclassing an existing container like RelativeLayout, you will be subclassing the more general ViewGroup.

一个custom composite view简单的重写了composite view的 onMeasure（）和 onLayout（），所以，你可以继承更加普通的ViewGroup，而不用继承一个向相对局部的容器。

[_TweetLayoutView_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/widget/TweetLayoutView.java) implements this technique. Note how it gets rid of the LinearLayout from TweetComposiveView and avoids the use of layout_weight [_altogether_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/res/layout/tweet_layout_view.xml).

TweetLayoutView实现了这个技术，了解它是如何从TweetComposiveView中摆脱了LinearLayout并且避免使用了layout_weight altogether

The laborious work of figuring out what [_MeasureSpec_](http://developer.android.com/reference/android/view/View.MeasureSpec.html) to use on each child view is done by the ViewGroup’s handy [_measureChildWithMargins()_](http://developer.android.com/reference/android/view/ViewGroup.html#measureChildWithMargins%28android.view.View,%20int,%20int,%20int,%20int%29) method—and [_getChildMeasureSpec()_](http://developer.android.com/reference/android/view/ViewGroup.html#getChildMeasureSpec%28int,%20int,%20int%29) under the hood.

搞清楚每个子view在ViewGroup使用如何measureChildWithMargins() 方法和 getChildMeasureSpec()去测量间距 是棘手的工作

TweetLayoutView probably doesn’t handle all possible layout combinations correctly but it doesn’t have to. It is absolutely fine to optimize custom layouts for your specific needs. This allows you to write both simpler and more efficient layout code for your app.

TweetLayoutView或许不需要控制所有的可能的布局混合的正确性，但是这不是完全不必要的，优化custom layout为你的特别需求时绝对可以的，这允许你为你的app写更简单且更高效的布局代码。

###Flat Custom View

As you can see, custom composite views are fairly simple to write using ViewGroup APIs. Most of the time, they will give you the performance your app needs.

你可以看到，通过使用ViewGroup 的API可以非常简单的写出来custom composite views，大多数的时候，他们会给你你所需要的表现。

However, you might want to go further and optimize your layouts even more on critical parts of your UI that are very dynamic e.g. ListViews, ViewPager, etc. What about merging all TweetLayoutView child views into a single custom view to rule them all? That is what flat custom views are about—see image below.

然而，你或许想更进一步的优化你的布局甚至动态布局是UI的一个关键部分，就像是ListView或者ViewPager，合并所有的TweetLayoutView的子view去一个单一的custom view去控制？这就是flat custom views。

![](/assets/layouts.png)

A flat custom view is a fully custom view that measures, arranges, and draws its inner elements. So you will be subclassing View instead of ViewGroup.

flat custom view是完全自定义view并且测量，整理和画自己的元素，所以你需要继承View而不是ViewGroup

If you are looking for real-world examples, enable the “show layout bounds” developer option in your device and have a look at apps like Twitter, GMail, and Pocket. They all use flat custom views in their listing UIs.

如果你正在找真实世界的例子，在你的设备启用“show layout bounds”开发者选项然后看一看App像是推特，Gmail或者Pocket，他们都是使用flat custom views在他们的列表UI。

Flat custom views give you maximum freedom as they are literally a blank canvas. But this comes at a price: you won’t be able to use the feature-packed stock widgets such as TextView and ImageView. Yes, it is simple to [_draw text_](http://developer.android.com/reference/android/graphics/Canvas.html#drawText%28java.lang.String,%20float,%20float,%20android.graphics.Paint%29) on a Canvas but what about [_ellipsizing?_](http://developer.android.com/reference/android/widget/TextView.html#setEllipsize%28android.text.TextUtils.TruncateAt%29) Yes, you can easily [_draw a bitmap_](http://developer.android.com/reference/android/graphics/Canvas.html#drawBitmap%28int[],%20int,%20int,%20float,%20float,%20int,%20int,%20boolean,%20android.graphics.Paint%29) but what about [_scaling modes_](http://developer.android.com/reference/android/widget/ImageView.html#attr_android:scaleType)? Same applies to touch events, accessibility, keyboard navigation, etc.

Flat custom views给你最大的自由就像实实在在的黑色画布，代价就是，你将不能使用feature-packed stock widgets（例如TextView或者ImageView），是的，在一个画布上写文字非常简单，但是多行呢？是的，你可以简单的画一个bitmap但是如果是一个scaling modes呢？同样适用于触摸事件，无障碍设置，键盘导航等等。

The bottom line is: with flat custom views,you will likely have to re-implement features that you would get for free from the platform. So you should only consider using them on core parts of your UI. For all other cases, just lean on the platform with composite views, custom or not.

底线就是，通过flat custom views你几乎等于重新实现了你想养在平台上自由实现的功能，所以只有核心的部分的UI你应该考虑一下，其他的情况，仅仅是依靠平台提供的composite views，custom不custom都可以。

[_TweetElementView_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/widget/TweetElementView.java) is a flat custom view. To make it easier to implement it, I created a little custom view framework called UIElement. You will find it in the [_canvas_](https://github.com/lucasr/android-layout-samples/tree/master/src/main/java/org/lucasr/layoutsamples/canvas) package.

TweetElementView是一个flat custom view，为了让它更佳容易的实现，我创建了个小的custom view框架叫做UIElement，你会在anvas package当中找到它。

The UIElement framework provides a measure/layout API which is analogous to Android’s. It contains headless versions of TextView and ImageView with only the necessary features for this demo—see [_TextElement_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/canvas/TextElement.java) and [_ImageElement_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/canvas/ImageElement.java) respectively. It also has its own [_inflater_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/canvas/UIElementInflater.java) to instantiate UIElements from layout resource [_files_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/res/layout/tweet_element_view.xml).

UIElement架构提供类似android的一个测量／布局 API，它包含了仅仅使用了必要功能的无头版本的TextView和ImageView，可以在TextElement 和 ImageElement仓库汇中看到，它有自己的inflater从layout resource files初始化UIElements。

Probably worth noting: the UIElement framework is in a very early development stage. Consider it a very rough sketch of something that might actually become useful in the future.

或许不值得，UIElement架构是一个早起的开发状态，它是一个或许将来会变的非常有用的非常粗糙的描述。

You have probably noticed how simple [_TweetElementView_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/widget/TweetElement.java) looks. This is because the real code is all in TweetElement—with TweetElementView just acting as a [_host_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/canvas/UIElementHost.java).

你可能意识到了TweetElementView看起来是多么简单，这是因为真实的code全在TweetElement配合 TweetElementView，就像是使用寄主

The layout code in TweetElement is pretty much analogous to TweetLayoutView‘s. It handles Picasso requests [_differently_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/widget/ImageElementTarget.java) because it doesn’t use ImageViews.

TweetElement中的布局代码差不多和TweetLayoutView的是类似的，它掌控了Picasso的请求，因为它不需要使用ImageVIew。

###Async Custom View

As we all know, the Android UI framework is [_single-threaded_](http://developer.android.com/guide/components/processes-and-threads.html#Threads). And this is for a good reason: UI toolkits are not your simplest piece of code. Making them thread-safe and asynchronous would be an unworthy herculean effort.

我们都知道，Android UI 架构是一个单线程的，这是一个很好的理由：UI工具包是不是你最简单的一段代码。使他们线程安全的，异步的将是一个不值得的艰巨努力。

This single-threaded nature leads to some fundamental limitations. It means, for example, that you can’t do layout traversals off main thread at all—something that would be useful in very complex and dynamic UIs.

单线程的本质导致了一些基础的限制，比如，你不能做布局遍历关闭主线程的所有的东西，将是非常复杂和动态的用户界面非常有用。

For example, if your app has complex items in a ListView (like most social apps do), you will probably end up skipping frames while scrolling because ListView has to [_measure_](https://github.com/android/platform_frameworks_base/blob/kitkat-release/core/java/android/widget/ListView.java#L1870) and [_layout_](https://github.com/android/platform_frameworks_base/blob/kitkat-release/core/java/android/widget/ListView.java#L1882) each child view to account for their new content as they become visible. The same issue applies to GridViews, ViewPagers, and the like.

例如，如果你的应用程序在ListView具有复杂的项目（就像许多的社交应用），你可能会跳过帧滚动时因为ListView具有测量和每个子布局解释他们的新内容，他们成为可见的视图。同样的问题适用于GridViews，viewpagers，等。

Wouldn’t it be nice if we could do a layout traversal on the child views that are not visible yet without blocking the main thread? This way, the measure() and layout() calls on child views would take no time when needed in the UI thread.

如果我们能做一个布局遍历子时图，是不可见的，而不阻塞的主线，这是不是很好？这种方式，在UI县城中子视图和触发measure() layout()便是即时的。

Enter async custom view, an experiment to allow layout passes to happen off main thread. This is inspired by the [_async node framework_](https://www.youtube.com/watch?v=TCuVxU07NWs&feature=youtu.be&t=33m43s) developed by the Paper team at Facebook.

async custom view，一个允许你布局但是不通过主县城。这是由在facebook的研究团队开发的异步节点框架。

Given that we can never ever touch the UI toolkit off main thread, we need an API that is able to measure/layout the contents of a view without being directly coupled to it. This is exactly what the UIElement framework provides us.

考虑到我们永远无法接触到主要的线程的用户界面工具包，我们需要一个接口，可以测量/布局的内容，而不直接耦合到它。这正是uielement框架提供了我们。

[_AsyncTweetView_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/async/AsyncTweetView.java) is an async custom view. It uses a thread-safe [_AsyncTweetElement_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/async/AsyncTweetElement.java) [_factory_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/async/AsyncTweetElementFactory.java) to define its contents. Off-screen AsyncTweetElement instances are created, pre-measured, and cached in memory from a background thread using a [*Smoothie*](https://github.com/lucasr/smoothie) item [_loader_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/async/TweetsLayoutLoader.java).

I had to compromise the async behaviour a bit because there’s no sane way of showing layout placeholders on list items with arbitrary heights i.e. you end up resizing them once the layout gets delivered asynchronously. So whenever an AsyncTweetView is about to be displayed and it doesn’t find a matching AsyncTweetElement in memory, it will force its creation in the UI [_thread_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/async/AsyncTweetView.java#L46).

Furthermore, both the preloading logic and the memory cache expiration would need to be a lot smarter to ensure more layout cache hits in the UI thread. For instance, using a LRU [_cache_](https://github.com/lucasr/android-layout-samples/blob/master/src/main/java/org/lucasr/layoutsamples/async/UIElementCache.java) here doesn’t seem ideal.

Despite these limitations, the preliminary results from async custom views look very promising. I’ll continue the explorations in this area by refining the UIElement framework and using it in other kinds of UIs. Let’s see where it goes.

###Wrapping up

When it comes to layouts, the more custom you go, the less you’ll be able to lean on the platform’s proven components. So avoid premature optimization and only go fully custom on areas that will actually affect the perceived quality and performance of your app.

This is not a black-and-white decision though. Between stock widgets and fully custom views there’s a wide spectrum of solutions—from simple composite views to the more complex async views. In practice, you’ll usually end up combining more than one of the techniques demonstrated here.






















