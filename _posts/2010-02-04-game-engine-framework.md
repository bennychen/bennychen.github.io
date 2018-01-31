---
id: 705
title: Game Engine Framework
date: 2010-02-04T13:44:37+08:00
author: Benny Chen
layout: post
guid: /?p=705
permalink: /2010/02/game-engine-framework/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - CjQIToRAAxp0
categories:
  - Game Programming
  - Some Experiences
tags:
  - framework
  - game engine
  - 框架
  - 游戏引擎
---
在这次找工作的过程中，有一道笔试题让我印象深刻，题目只有简单的一句话：**please use pseudo code** **to write a game engine framework**（请用伪代码写出一个游戏引擎的框架）

当时看到这道题，完全是一种头皮发麻的感觉，虽然我对游戏引擎还算比较熟悉，但它可是个“巨象”般的庞大结构，要在短短的90分钟的笔试时间内把它“摸”完（而且90分钟也不只这一道题），那可真是天方夜谭。所以我当时在试卷上留下的结果就是，草草的画了几个模块图了事。

之后我才意识到，我根本就完全没有理解这道题，或者说，是完全没有理解一个词的意义——framework。受游戏引擎庞大印象的牵连，我把framework想得太大了。

Framework对于IT人士来说貌似是个挺时髦的词，这个词也经常被我们挂在嘴边，我也如此。但问题是，我几乎从来没有去好好的留心过或者深究过，到底什么才是framework，什么样一个东西才能被称为framework，它的准确定义又是什么呢。

看一般英汉字典里对于framework的解释：

> structure giving shape and support  框架; 结构

这只是给了我们framework的中文翻译而已，几乎还是没有给我们什么有用的信息。

记住一句话：有困难，找Wiki。当我看到Wiki上对software framework精确的定义时，突然间，一切都明白了。

原文URL：<a href="http://en.wikipedia.org/wiki/Software_framework" target="_blank">http://en.wikipedia.org/wiki/Software_framework</a>

> A **software framework**, in computer programming, is an abstraction in which common code providing generic functionality can be selectively overridden or specialized by user code providing specific functionality. Frameworks are a special case of software libraries in that they are reusable abstractions of code wrapped in a well-defined API, yet they contain some key distinguishing features that separate them from normal libraries.
> 
> Software frameworks have these distinguishing features that separate them from libraries or normal user applications:
> 
> 1. inversion of control &#8211; In a framework, unlike in libraries or normal user applications, the overall program&#8217;s flow of control is not dictated by the caller, but by the framework.
  
> 2. default behavior &#8211; A framework has a default behavior. This default behavior must actually be some useful behavior and not a series of no-ops.
  
> 3. extensibility &#8211; A framework can be extended by the user usually by selective overriding or specialized by user code providing specific functionality
  
> 4. non-modifiable framework code &#8211; The framework code, in general, is not allowed to be modified. Users can extend the framework, but not modify its code.

这段话的大概意思是这样，首先表明了software framework是一种抽象体，这个抽象体通过一些公共代码提供了通用的系统功能，使用framework的用户可以在此基础上进行编码，以覆盖或者特化该系统中的相关功能。接着，这段话还说明framework也是一种库，但跟传统的软件库又不同。framework的特点被总结为以下4点：

  1. framework完全决定了程序的控制流，该控制流绝对不会被调用framework的用户(callee)改变，这就是framework跟传统的软件库不同的地方；
  2. framework提供默认行为，且这些默认行为绝对不是无意义的行为；
  3. 用户可以扩展framework的行为；
  4. framework本身的代码不允许被用户改变。

看到这里，我几乎是只有抱头痛哭的份了。因为，从开始学最基础的DirectX技术起，那每一个Demo中，不管多小的Demo，几乎就包含了这样的一种框架，抑或是说，一种游戏引擎框架。只要理解了framework的意思，这道题就根本不是什么问题。我是完全被“游戏引擎”这4个字吓住了，这道题的目的根本不是要你真正的写一个正规的游戏引擎（那是扯蛋），只要弄出一个基本架子即可，达到“麻雀虽小五脏俱全”的效果。

几乎所有的游戏或者说所有的图形渲染的程序，都是遵循下面这样一个过程，一个再熟悉不过的控制流。

<a href="/wp-content/uploads/2010/02/flow-of-control.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="aligncenter size-full wp-image-712" title="flow of control" src="/wp-content/uploads/2010/02/flow-of-control.jpg" alt="FoC of Game" srcset="/wp-content/uploads/2010/02/flow-of-control.jpg 462w, /wp-content/uploads/2010/02/flow-of-control-300x241.jpg 300w, /wp-content/uploads/2010/02/flow-of-control-372x300.jpg 372w" sizes="(max-width: 462px) 100vw, 462px" /></a>

游戏的整个控制流就可以到这么简单，除了图上所描述的东西之外，再加上一系列的消息处理，就可以组成一个基本的游戏框架了。按照wiki上所描述的基本软件framework的4大特点，再来看看游戏引擎framework是如何体现的。

  1. 很显然，游戏的控制流如上图所示，使用framework的用户无法更改；
  2. 游戏引擎framework当然提供默认的行为，比如Initialize部分封装默认的窗口初始化和一系列的设备初始化等过程；
  3. Update和Render，是framework使用者真正进行艺术创造的地方，是游戏开发者的画板。
  4. 游戏引擎framework的代码同样不允许用户修改。

用代码来表现framework是很简单的，在面向对象的语言中一般用抽象类，既提供一些默认的实现，又提供抽象接口供用户扩展。而用户要使用这个framework，只需自定义一个类继承该抽象类，并提供各个抽象方法的实现。用户所需做的只是做一系列的填空题，当然填的如何精彩这就完全取决于用户了。

写这篇文章的真正目的不是为了说明一个游戏引擎的framework是怎么样的，只是借此来强化framework的概念，这样当我们下次再说出这个词的时候，能够知其所以然，而不是自欺欺人。

如果对游戏引擎framework感兴趣，这里有一个简单的游戏引擎framework的代码可供参考： <a href="http://gpwiki.org/index.php/SDL:Tutorials:Simple_Engine_Framework" target="_blank">http://gpwiki.org/index.php/SDL:Tutorials:Simple_Engine_Framework</a>