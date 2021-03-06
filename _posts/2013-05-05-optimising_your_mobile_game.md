---
id: 1738
title: '[译]优化你的手机游戏（没有延迟的，才是健康的）- 一篇给游戏美术设计师读的文章'
date: 2013-05-05T22:27:18+08:00
author: Benny Chen
layout: post
guid: /?p=1738
permalink: /2013/05/optimising_your_mobile_game/
sfw_pwd:
  - JqtLxPAtjmku
categories:
  - 3D
  - Game Programming
  - Some Experiences
  - Uncategorized
  - Unity
---
[译者的话（新浪微博@滚石，欢迎大家一起讨论）]
  
由于公司目前没有专业的技术美术(Technical Artist)，在最近的项目开发中碰了不少壁。于是我就特地做了一些关于美术方面优化的研究，听了一些讲座，阅读了一些文章，你即将阅读的这篇就是其中之一，我觉得不错，并在阅读中获得了共鸣，所以也就决定将这片文章翻译为中文。一来希望在公司中，对于我们的美术设计师作为一个参考，另外分享出来也希望，如果能有那么一点点帮助到跟我们遇到同样困扰的团队或者美术或者任何同仁们，我将万分荣幸。

由于我是一个程序猿，所以翻译中难免有些话或者美术专业术语译的比较二的地方，还请谅解。

原文地址：[Optimising Your Mobile Game (Less Lag, More Awesome)](http://indiebits.com/optimising-mobile-games/)

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

你是否曾经开发了一个手机游戏，在你把它递交给reviewer时，本来满心希望它像百米冲刺一样高速运行，结果却步履蹒跚，甚至最后慢得跟爬行一样。

这很烂，不是么？我们意识到这本来是可以避免的，但很多小的错误和疏忽不断堆积，最终造成了程序的瘫痪：它太慢了！

介于我们一直这么粗心犯错，我们一定是忘了给自己的”角色属性表“加上预知未来的能力了 🙂

如果一直这样，那我们开发的游戏——AlieNation，将变得根本不能玩。幸运的是，我们停止了错误，并开始搜集大家的想法，对游戏进行优化（优化成为了我们开发中的新王牌武器！）

<a href="/wp-content/uploads/2013/05/alienation.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="alignnone size-medium wp-image-1740" alt="alienation" src="/wp-content/uploads/2013/05/alienation-300x253.jpg" width="300" height="253" srcset="/wp-content/uploads/2013/05/alienation-300x253.jpg 300w, /wp-content/uploads/2013/05/alienation.jpg 319w" sizes="(max-width: 300px) 100vw, 300px" /></a>

### 为什么我们从2D转向3D

考虑到从测试玩家和本地媒体获得的认可，我们决定创建一个3D摧毁型游戏（destruction game），以填补市场上的空白。很惊讶大多数的摧毁型游戏都是2D的，但3D才是摧毁型游戏应该的样子，因为3D版本的感觉是如此的棒。当然，你需要做更多的工作来优化你的美术流水线(art pipeline)。

高效和优化让我在一些甚至不知晓的领域中感觉到从容温暖，所以在这里有一些事情要和你们分享，我铭记它们于脑中，并且一直提醒我的团队遵循它们，以保证我们的3D游能够持续运行流畅。

### 何时选择3D（何时保留2D）

作为一个3D游戏美术，我得到的第一个关键教训就是，让所有的东西尽可能的保持低模——不要使用额外的不必要的顶点，如果你确实要让你的模型拥有精度，把它放置在贴图中。比如说，我们的建筑物都是用简单的方块创建而成，然后贴上带有质感的细节纹理。

当我们要为Vogel教授（它只在我们游戏的GUI和HUD中出现）建模时，首先尝试的是3D模型，但是最终，我们还是把它分解为像Flash中那样的多层2D平面。它运行非常流畅，看上去也更棒，事实最终的结果比用3D模型赞非常多！使用带有动画的2D平面，可以很容易的创造细节，使得Vogel教授看起来非常酷，并吻合整个游戏的艺术风格。

现在每次我看到它时（下图的左下角就是Vogel教授），还会忍不住幸福热泪 ;-(

<a href="/wp-content/uploads/2013/05/vogelingame.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="alignnone size-medium wp-image-1741" alt="vogelingame" src="/wp-content/uploads/2013/05/vogelingame-300x185.jpg" width="300" height="185" srcset="/wp-content/uploads/2013/05/vogelingame-300x185.jpg 300w, /wp-content/uploads/2013/05/vogelingame.jpg 512w" sizes="(max-width: 300px) 100vw, 300px" /></a>

### 不要在贴图（纹理）优化上偷懒！

贴图大小是最容易被大多数美术设计师忽视优化的，要知道，**贴图会占用非常非常多的空间**！

通过跟几个设计师聊天，我发现他们中很多人都习惯于创建他们觉得舒适的尺寸，比如512&#215;512，这符合常理，没什么错误，但唯一的问题就是，设计师不会在导出贴图之前，将它们降低到一个更加合理的尺寸。

一个更小更高效的贴图文件，在放置到游戏中后，可以和一个超大的贴图文件看起来效果一样好！但是如果设计师所有的工作都是在错误的贴图尺寸中进行的，很不幸，所有设计师花费了大量时间绘制的漂亮美术细节，在游戏中被降低尺寸后，会被无情的抹杀 🙁

为了完全避免这个问题，我建议设计师应该在降低尺寸后对贴图做一些额外处理，亦或者是在创建时直接使用和在游戏中一样的分辨率。因为这样的话，设计师才能够精确掌控贴图中的每一个像素点。

### 文件格式，贴图重用和Shaders

PNG相比于其它很多格式没那么“邪恶”。相比于JPEG的有损压缩，它的压缩是无损的；它虽然对于Alpha透明的处理不像TGA那么出色，但是它既压缩文件同时又能保证足够好的Alpha映射，确实比其它的格式胜出一筹。

<a href="/wp-content/uploads/2013/05/texfilesize.jpg" class="highslide-image" onclick="return hs.expand(this);"><img alt="texfilesize" src="/wp-content/uploads/2013/05/texfilesize-300x111.jpg" width="300" height="111" /></a>

很多团队的教训是类似的：

**不合理的贴图尺寸 + 不合理的文件格式 = 占用了太多的纹理空间 = 更慢的游戏 = 找到纹理并降低尺寸 = 丢失细节 = 浪费时间 = 非常沮丧的美术设计师 = 非常愤怒的美术总监** 🙁

另外一个关于贴图非常有用的建议是——**尽可能的重用它们**。很显然，这个不光对贴图，对其它任何游戏资源都应该如此。在我们创建Alienation的过程中，我们没有创建新的贴图用于几乎很难被看到的武器和金属物体上，而重用了建筑物的贴图用在这些非常小而琐碎的物体上。只需要一点UV技巧，你永远不会发觉它们的贴图其实是被重用的。

非常重要的一点，你要选择合适的Shader程序。当然，法线高光Shader（Bumped Specular Shader）用到物体上时，看起来太他妈的赞了，但是应该使用特定的手机定制shader，让游戏更高效。在Unity中，这比较容易做到，因为Unity内置了一整套专门针对手机的shader。

### 什么时候采用Alpha贴图，什么时候使用Mesh（网格模型）

Alpha贴图的卓越优点是它可以让一个物体的部分透明，非常容易，并且也相对的比较经济(cheaply)[作者注：这里说“经济”是相对的，是从处理消耗(processing cost）的角度说的。每一个像素，多边形，shader，light，粒子和骨骼，都会消耗你的游戏的处理性能。]

然而，在有些情况下，mesh比alpha贴图要更高效。举例说，当我们为汽车创建一个轮子时，为了表现轮子中间的缺口，建立轮子的网格模型要比使用贴图+透明的方式，来得高效的多。

我意识到，在这里我有点自相矛盾了，因为之前我说过，要把更多的细节放在贴图中，而不是网格模型上。记住一点，没有绝对的优化技术，如果你拥有这样的能力可以在特定时候选择使用最合理的优化技术，就造就了你不仅仅是一个游戏美术设计师，而且是一个伟大的牛逼的设计师 😀

<a href="/wp-content/uploads/2013/05/thumbsup.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="alignnone size-medium wp-image-1761" alt="thumbsup" src="/wp-content/uploads/2013/05/thumbsup-300x231.jpg" width="300" height="231" srcset="/wp-content/uploads/2013/05/thumbsup-300x231.jpg 300w, /wp-content/uploads/2013/05/thumbsup.jpg 400w" sizes="(max-width: 300px) 100vw, 300px" /></a>

### 粒子系统，draw calls，还有物理

对于任何游戏，视觉上的反馈(visual feedback)对于吸引玩家是异常重要的。如果对于玩家的操作缺少反馈，游戏会变得非常无聊，令人昏睡。

说到反馈：粒子效果是一个非常好的给玩家传递信息的方式。可能是一个子弹打到墙上产生的火花粒子效果，又或者是子弹射击一个纳粹分子的血溅粒子效果。

但是对于粒子，有些事情很容易犯错。第一点是对于粒子的贴图大小，对于贴图大小问题，本文的前半部分已经提到，如果你跳过了那部分，强烈建议你回到前面去看一看，我保证那部分内容能让你马上在贴图优化上变成一个更性感的人（而不是一个胡乱贴图的人）:D

对于粒子系统的第二点是，很多人没有意识到，增加粒子个数，根本不会增加粒子效果的牛逼度——只会让游戏变得更慢。一个人可以用两种方式创建粒子效果：第一种是用尽可能少的发射数量，来达到一个最好的效果；另一种人就是搞2000个粒子，然后&#8230;慢慢跑吧&#8230;

你很少在现实游戏中见到过一个发射器包含2000个粒子效果，唯一的原因就是draw calls。

draw call是针对场景中每一个不同的物体而创建的（多一个draw call就会多增加一点游戏的处理时间）。对于粒子系统而言，在draw call上，很容易超载。

我喜欢制作一些粒子效果，但是每当我看到一些初级的设计师，看到一个漂亮而高效的粒子效果问：“只有10个粒子？太少了，这个如果用2000个粒子会更好”，我就巨他妈的沮丧，甚至肉体难受 🙁

<a href="/wp-content/uploads/2013/05/particles.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="alignnone size-medium wp-image-1764" alt="particles" src="/wp-content/uploads/2013/05/particles-300x209.jpg" width="300" height="209" srcset="/wp-content/uploads/2013/05/particles-300x209.jpg 300w, /wp-content/uploads/2013/05/particles.jpg 512w" sizes="(max-width: 300px) 100vw, 300px" /></a>

对于上图，厄&#8230;.这&#8230;..你为什么要这么搞，非常差劲的初级美术。

### 物理

没有什么比准确的射击到一个建筑物，并看到它优雅壮丽的倒下更酷了。

为什么会这样？所有的这一切都是因为甜美而性感的物理系统。

当说到物理系统时，程序猿可是这方面的专家。所以接下来的一段话，可能是一种程序猿专用的非常难以解析的语言，所以如果您是一位美术，在这里就忍忍吧 😉

在每一帧，物理系统都会更新很多次，物理会更新受其影响物体的位置，速度和其他一切跟运动相关的属性。每一帧物理的更新次数会直接影响到游戏的运行速度。所以加快游戏运行的一个方式就是减少每一帧物理系统被更新的次数。

这当然是一种可以尝试的方式，但是带来的坏处是会影响你的物理系统的准确性，所以平衡好每帧的更新次数，以获得最佳并最高效的物理效果是非常重要的。

一个例子，当我们减少了每帧物理更新的次数时，我们游戏中的一个球体可能穿过了一道墙，这&#8230;我觉得球穿墙可是不真实的，你呢？

所以要调节到一个最优的物理更新帧率是不容易的，这可花了我们公司的程序猿们好一段时间。

### 光照和天空盒

两件事情我会快速过下——光照和天空盒。

关于天空盒，很称奇的一点是其实你根本不需要使用它们作为天空！（我对吗?!?!）

一个非常棒的方式是在天空中包含背景艺术（background art），这使得能很经济简单的加入非常多的背景艺术和气氛到游戏中。

光照有点神秘，所以我直接了当，对于手机，最好使用平行光(directional light），或者如果你够幸运，加几盏点光（point light）也可以。在Alienation游戏中，在对light做了日以继日的研究后（我撒谎了，其实我就是google了一下unity light;-)），我发现聚光灯（spotlight）在手机游戏上是最消耗性能的光源，慎用。

### 结束这一切

本文中提到的所有建议，独立采纳其中的一条并不会让你的游戏马上得到改观。

但是如果你综合采纳所有的这些优化，你的游戏品质一定会马上得到质的提高，到时候，嗯哼，等着幸福的哭吧~

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

[译者的话]
  
个人觉得，这些建议非常好，并在游戏开发中时刻提醒自己遵循它们。但是因为大家开发的游戏都是各式各样的，使用的引擎也完全不同。不要把某种优化建议当成教条，应该选择最适合你们团队的。尽量多使用游戏引擎的profiling，以及时观察分析游戏中影响游戏系能的因素，而不要教条式的麻木follow各种优化建议。