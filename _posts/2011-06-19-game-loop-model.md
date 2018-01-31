---
id: 1559
title: Game Loop的几种实现方式
date: 2011-06-19T23:37:10+08:00
author: Benny Chen
layout: post
guid: /?p=1559
permalink: /2011/06/game-loop-model/
sfw_pwd:
  - g2tMg6kqBgaC
categories:
  - Game Programming
tags:
  - game
  - loop
---
写这篇博客的目的是为了对game loop（游戏主循环）做一个全面的总结和介绍，包括它的定义，与之相关的专业术语（terminology），以及最重要的，对它的几种实现方式，从代码层次做一些介绍以及优缺点分析。

### 1.什么是Game Loop

对任何一个游戏开发者来说，game loop都应该不是一个陌生的概念。任何一款游戏都会有一个自己的game loop，它是整个游戏的核心，是游戏的心脏。具体什么是game loop，这是来自于Game Engine Architecture[2]上对于game loop的定义：game loop是对于一个游戏（或者一个游戏引擎）的所有子系统（subsystem）的周期性的更新。一个游戏会包含各种不同的子系统（比如渲染，物理，动画，AI等等），这些不同的子系统负责处理不同的游戏任务，game loop的实现方式就决定了这些子系统的任务执行的组织方式。而这些就决定了整个游戏的基础架构，同时也将决定game在不同的机器上将如何运行，在下面介绍game loop的不同实现方式时会对此有详细介绍。

game loop中所处理的逻辑和操作包罗万象，但一般可抽象为以下这几种模块：

  * 处理输入：这些输入包括，来自于交互设备（键盘，鼠标，游戏控制器等等）的输入，来自于网络的输入；
  * 系统更新（update）：（根据输入或者自发的）对各个子系统进行更新，以决定当前的游戏状态；
  * 渲染（render）：对整个游戏场景进行渲染。

所以一个最最简单的game loop可以抽象为如下的这些伪代码：

<pre class="brush: cpp; title: ; notranslate" title="">while ( game is running )
{
	processInput();
	update();
	render();
}
</pre>

### 2.相关术语

常常和渲染紧密相连的一个参数是FPS（frame per second），它指的是每秒显示设备在屏幕上进行绘制的频率。对于游戏来说，一般50－60的FPS是最优，最低也不要低于16帧[1]。游戏中还有一个容易被忽略的重要参数可以被称为游戏速度（game speed）[3]，它指的是游戏状态每秒被更新的次数，容易与FPS混淆。一个通俗的理解和区别它们的方式可以是，FPS是render函数被调用的频率，而game speed则是update函数被调用的频率。

游戏是对实时性（real-time）要求很高的系统，所以在game loop中，time是尤其重要的一个因素。这里需要说的是，游戏中会包含好几种不同的时间，这些时间有着各自的时间线（timeline），并且在游戏中发挥着各自不同的重要作用[2]。

  * 系统的真实时间（real time）。真实时间一般在操作系统被定义为从过去某个固定时间点（比如常见的一个时间点被称之为Epoch, 是1970年1月1日的0:00:00， UTC时间）到当前点总共逝去的时间。
  * 游戏时间（game time）。一般情况下，游戏时间等于系统时间。但在某些特殊情况下，游戏时间可以发挥不同的作用：比如在游戏暂停的时候，需要停止更新游戏时间；在游戏需要以慢速运行时，可以用比真实时间低的频率来更新游戏时间，这在我们需要进行一些游戏调试的时候，尤其有用。下面会介绍到，一个好的game loop结构，可以有助于游戏调试，这对开发人员来说是非常重要的。
  * 局部时间线（local timeline）。不管是一个动画，还是一个音频或者视频片段，都会有一个局部时间线。通过将此局部时间线以不同的方式映射到全局的时间线，可以灵活的控制片段以特定的方式进行播放。[2]
  * 帧时间（frame time）。两次帧循环之间所间隔的时间，具体可看本文章的3.2.1节。

### 3.Game Loop的实现方式

OK，介绍了这么多跟game loop相关的概念，接下来进入正题，game loop的各种实现方式大盘点。

有一点需要强调的是，game loop在单线程的游戏系统中是一种情况，而在多线程的系统中又是另一个不同的故事。多线程对于系统会引入异常的复杂性，关于这个，可以看我之前的一篇关于游戏引擎多线程的文章：</2011/01/关于游戏引擎多线程的一些整理和思考/>。相比来讲，单线程要简单很多，这篇文章里以下的一些game loop的实现方式也主要是针对在单线程环境中的。

在单线程环境中，game loop按照实现方式可以分为以下这几种类型：

  * 基于帧的（frame-based）game loop
  * 基于时间的（time-based）game loop 
      * 可变频率（variable-step）game loop
      * 固定频率（fixed-step）game loop

#### 3.1 基于帧的（frame-based）game loop

这是一种最简单的game loop，就如上面文章刚开始的伪代码所描述的一样，在这种结构下，游戏只是重复的不间隔的进行processInput，update，render操作。可以看到，这种game loop的实现异常简单，但缺点也是异常明显的。因为它缺少了一个很重要的控制因素——时间，所以在不同配置的机器环境下，游戏将以不同的速度运行。比如说，游戏中的一个物体，在每次的update操作中将它的位移增加5（单位可以是米，英尺，像素，或者其它某种单位，每个游戏都使用一个特定的距离单位）:

<pre class="brush: cpp; title: ; notranslate" title="">void update()
{
	object.position += 5.0f;
}
</pre>

在一个较快速的机器上，update被执行的次数会更多，所以物体移动的速度就会更快，这就导致了游戏在不同机器运行速度的不一致，这是不能接受的。

优点：简单；
  
缺点：在不同机器上，游戏的运行速度不一致。

#### 3.2 基于时间的（time-based）game loop

为了让不同机器的游戏运行速度一致，就需要引入时间，这就带来了基于时间的game loop实现方式。这种方式下，又可以分为两种，可变频率（variable step）的和固定频率的（fixed step）。在[5]中，这两种方式则分别被称为可变间隔的（variable interval）和固定间隔的（fixed interval）。

**3.2.1 可变频率的game loop**
  
这种方式的实现只需要为update函数引入一个时间参数elapsedTime即可，elapsedTime指的是从上一次loop的执行到当前loop所过去的时间，通常称之为帧时间（frame time），或者time delta。它的单位一般使用毫秒，这是在游戏中进行时间相关操作所使用的标准单位[2]，当然其它比如在做profiling时为了计算某个函数的执行时间，则用的是机器周期（machine cycle）这样更精确的时间机制。

这是这种game loop实现的伪代码，也非常简单：

<pre class="brush: cpp; title: ; notranslate" title="">lastFrameTime = getCurrentTime();
while ( game is running )
{
	processInput();

	currentFrameTime = getCurrentTime();
	elapsedTime = currentFrameTime - lastFrameTime;
	update( elapsedTime );
	lastFrameTime = currentFrameTime;

	render();
}
</pre>

这时，再看前面的那个例子，对游戏中的一个物体，在每次的update操作中不是将它的位移绝对增加5，而是要乘以时间这个因子，这样5实际代表的是物体的速度。速度乘以时间，就得到了物体在这段时间内的位移:

<pre class="brush: cpp; title: ; notranslate" title="">void update( float elapsedTime )
{
	object.position += 5.0f * elapsedTime;
}
</pre>

这样就解决了在不同配置的机器上运行速度不一致的问题。因为在更快的机器上，update执行的频率高，但frame time的间隔时间较短，所以物体位置更新的频率高，但每次更新的位移幅度小。反之，在较慢的机器上，update函数的执行频率较低，但frame time间隔时间较长，则每次物体将以较大幅度的更新位移。正是因为引入了时间因素，所以在不同配置的机器上，游戏的运行速度将会看起来一致。

不过这种game loop在实现时需要解决一个长时间暂停的问题，所以我们在暂停时，要同时停止更新游戏时间，以免在暂停后恢复时，将得到一个超级大的elapsedTime值。

但随之而来，这种game loop实现方式的缺点也暴露出来了。比如考虑这样一种场景，物体绕着一个弧形的轨迹进行移动。在正常的速率下，物体的运行轨迹几乎是弧形的。如下图，图片来自于[5]。

<a href="/wp-content/uploads/2011/06/Screen-shot-2011-06-18-at-11.57.04-PM.png" class="highslide-image" onclick="return hs.expand(this);"><img src="/wp-content/uploads/2011/06/Screen-shot-2011-06-18-at-11.57.04-PM.png" alt="" title="move in curve normal" width="239" height="181" class="alignnone size-full wp-image-1594" /></a>

但是在较慢的机器上，虽然物体的移动位置点能保持同步，因为更新的频率较低，物体的移动轨迹就变得非常离散，以至于不是按照一个弧形在移动，如下图。

<a href="/wp-content/uploads/2011/06/Screen-shot-2011-06-18-at-11.57.15-PM.png" class="highslide-image" onclick="return hs.expand(this);"><img src="/wp-content/uploads/2011/06/Screen-shot-2011-06-18-at-11.57.15-PM.png" alt="" title="move with curve abnormal" width="227" height="184" class="alignnone size-full wp-image-1595" /></a>

其它的效果也有类似的问题，比如说动画，虽然说动画的播放速率是一致的，但是在较慢的机器上，会出现比较严重的掉帧现象，这就是我们俗称的“卡了”。再比如说物理，在一个正常的机器上，一个障碍物能够完美的被避开，但在一个较慢的机器上，这就不好说了。

对于这种game loop，就算是较快的机器上，也是有问题的，虽然好像update被执行的越快越多，游戏运行的就越流畅，用户的体验应该越好才对。但其实不然，两点原因，首先就算在较快的机器上，也可能会遭遇到运算的高峰期，这时由于对比明显，游戏性能的下降会非常明显，游戏用户就很容易察觉到这种性能降级（performance degradation），这并不是好的体验；再一点，对于手机等移动设备上的游戏，update速率执行过快不是好事，这对电池是一种消耗。事实是，游戏只需要一定范围内的update频率就可以达到流畅而令用户接受的效果。

优点：简单且不同机器上的游戏运行速度是一致的；
  
缺点：在较慢的机器上，物体的更新频率慢会导致各种效果失真（distortion）；在较快的机器上，更新太快的话会更容易让用户察觉到性能降级，且对于移动设备，更新太快会降低电池的使用时间。

**3.2.2 固定频率的game loop**
  
由此，为了解决上面的问题，就有了这种固定频率的game loop，让游戏的更新速度保持在一个特定的恒定值。比如下面的这段伪代码，让游戏恒定运行在FPS（或者game speed，此时FPS等于game speed）为25的速度下。代码参考自[3]。

<pre class="brush: cpp; title: ; notranslate" title="">#define FRAME_RATE 25
#define FRAME_TIME ( 1000 / FRAME_RATE )

nextFrameTime = getCurrentTime();
while ( game is running )
{
	processInput();
	update( FRAME_TIME );
	render();

	nextFrameTime +=  FRAME_TIME;
	currentFrameTime = getCurrentTime();
	if ( nextFrameTime &gt;= currentFrameTime )
	{
		sleep( nextFrameTime - currentFrameTime );
	}
	else
	{
		// 我们已经跟不上帧速率了
	}
}
</pre>

可以看到，如果一次loop执行完的持续时间小于固定帧时间，则直接让主线程sleep即可。但是如果在较慢的机器上（或者是设定的固定帧速率过高），执行完一帧的时间会超过固定帧时间，导致无法达到所目标的帧速率，则只能忍受这种情况了。最差情况下，如果在某一时期内游戏遭遇到了非常巨大的运算压力，则游戏将会变得异常缓慢到无法忍受的地步。

保持固定的更新频率有一个非常重要的优点，因为它带来了游戏执行的确定性（game execution determinism）[1]，所以以这种机制所实行的game loop可以被称为确定性的game loop（deterministic game loop）。反之，可变频率的game loop则是非确定性的（non-deterministic），因为它依赖于系统每一帧运行的时间，这在游戏每次运行时是变化不定的，这就导致游戏的行为也是不定的。

确定性机制能够为系统带来一个非常重要的特点——录制和回放功能（record and replay）[2]。所谓录制回放功能，就是能够将玩家在进行游戏的时候的各种操作记录下来，以便在下一次运行时，就能够通过回放将游戏以同样的方式执行。这会成为一个很好的debugging工具，因为通过回放功能，会让一些难以发现的bug得以轻而易举的复现，这是非常珍贵的。甚至我们还可以支持单步调试（single-stepping）功能[2]，单步调试指的是当游戏处于暂停状态时，可以通过某个按键，让游戏一次执行一个frame time，这在调试游戏时都是非常有用的。

这种game loop还是有一个问题，它紧耦合了update和render的执行频率，update的频率（即game speed）保持在25基本能满足游戏运行的流畅需求，但让render的更新频率（即FPS）也保持在25，对于配置好的机器实在是有些浪费，我们可以让渲染的更快，以获得更好的画面效果。

优点：在不同机器上游戏效果一致，同时为游戏带来确定性。
  
缺点：一个更好的机器并不能带来更好的游戏画面，扩展性（scalability）差。

**3.2.3 获得最大FPS的固定频率的game loop**
  
解决上一个game loop缺点的办法就是解耦update和render，让它们以各自不同的频率运行。这就带来了这个固定频率的game loop的变种，称之为“获得最大FPS的固定频率的game loop”（Constant Game Speed with Maximum FPS）[3]。

同时这种方法还处理另一个问题，当update处理时间过久时，这种game loop会暂时不进行render而再次update。换言之，当update执行时间长于所期望的帧时间时，游戏会丢弃绘制帧并调用额外多次update函数，以让游戏从一个慢速（slowdown）状态中追赶上并恢复过来[4]。

这是该game loop的代码：

<pre class="brush: cpp; title: ; notranslate" title="">#define MAXIMUM_FRAME_RATE 45;
#define MINIMUM_FRAME_RATE = 15;
#define UPDATE_INTERVAL ( 1000 / MAXIMUM_FRAME_RATE )
#define MAX_CYCLES_PER_FRAME ( MAXIMUM_FRAME_RATE / MINIMUM_FRAME_RATE )

nextFrameTime ＝ getCurrentTime();
while ( game is running ) 
{
	loops = 0;
	while( getCurrentTime() &gt; nextFrameTime 
		  && loops &lt; MAX_CYCLES_PER_FRAME ) 
	{
		update( UPDATE_INTERVAL );
		nextFrameTime += UPDATE_INTERVAL;
		loops++;
	}

	render();
}
</pre>

从上面的代码中看出，当出现update处理时间过久时，game loop并不是一直重复执行update而不渲染，update频率被控制在15-45之间，所以在一次while循环中，最多只会执行3次update（MAX\_CYCLES\_PER\_FRAME＝MAXIMUM\_FRAME\_RATE / MINIMUM\_FRAME_RATE＝45/15）。

当然FPS也不总是可以为任意值，有时为了解决显示设备上的一种叫做tearing[2]（屏幕的上半部分显示的是上一帧的画面，而下半部分是当前帧的画面）的问题，需要将FPS设置为显示设备刷新频率的倍数。在手机等这种移动设备上，同样为了省电，也需要将FPS固定在一个恒定值。但不管怎样，update和render仍然是以各自不同的频率运行。最近在读的一本关于iOS游戏开发的书上[6]的game loop，作者使用的就是这种game loop，它的game speed被设置在15-45，而FPS则采用的是iOS上默认的60（iOS上使用CADisplayLink的frameInterval属性来设置绘制帧率，frameInterval默认是1，表示显示1秒钟会被刷新60次）。

优点：拥有固定频率的game loop的优点，同时解耦update和render，并且当帧速率降低时，可以通过丢弃绘制帧来保持游戏的速度；
  
缺点：在高配置的机器上依然有些浪费资源。

#### 3.3 其它方式

game loop还可以有更多的变化，比如在[1]中提到，对于游戏中的不同子系统，update的频率也是不一致的。比如，为了获得很好的动画效果，需要一个较高的频率来更新动画，而对于AI系统，用同等高频率的速度来更新就是浪费计算资源了。所以这篇文章提出了一种更好的update机制，将update分隔为两部分，一部分以最快的速度运行，而另一部分以某种预设的固定频率运行。

在[3]中提出，为了获得一种更平滑的画面效果，可以对frame-time进行插值（interpolate），并且为update函数提供预测函数（prediction function）。

### 4.总结

一个看似简单的game loop，也可以有这么多的变数。这篇文章主要基于时间因素列出了4种实现方式：第1种是基于帧的game loop，一般而言要避免采用这种方式，而应该选用后面3种基于时间的game loop。基于时间的可变频率的game loop是一种常见的实现方式，不过为了获得稳定的画面效果和游戏运行的确定性，可以使用固定频率的game loop。最后还可以将update和render解耦以各自频率运行，以获得最优的组织结构运行游戏。

OK，这里就是我有史以来最长的一篇博客的结尾。

### 5.参考

［1］LUIS VALENTE, Real Time Game Loop Models for Single-Player Computer Games
  
［2］Jason Gregory, Game Engine Architecture
  
［3］<http://www.koonsolo.com/news/dewitters-gameloop/>
  
［4］<http://msdn.microsoft.com/en-us/library/bb203873.aspx>
  
［5］<http://sacredsoftware.net/tutorials/Animation/TimeBasedAnimation.xhtml>
  
［6］Michael Daley, Learning iOS Game Programming － A Hands-On Guide to Building Your First iPhone Game