---
id: 234
title: 复习了一下Frustum Culling
date: 2009-06-25T18:17:48+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=234
permalink: '/2009/06/%e5%a4%8d%e4%b9%a0%e4%ba%86%e4%b8%80%e4%b8%8bfrustum-culling/'
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - lSLy8QUAWbhY
categories:
  - Computer Graphics
tags:
  - frustum culling
---
上次跟frustum culling的亲密接触还是两年前的事情，那时的一个游戏Demo里实现了quad-tree地形，并使用frustum culling显著减少三角形面的渲染。

<p style="text-align: center;">
  <a href="http://www.bennychen.cn/wp-content/uploads/2009/06/catcher-in-the-rye.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="aligncenter size-full wp-image-235" title="catcher-in-the-rye" src="http://www.bennychen.cn/wp-content/uploads/2009/06/catcher-in-the-rye.jpg" alt="catcher-in-the-rye" width="400" height="314" srcset="http://www.bennychen.cn/wp-content/uploads/2009/06/catcher-in-the-rye.jpg 808w, http://www.bennychen.cn/wp-content/uploads/2009/06/catcher-in-the-rye-300x235.jpg 300w, http://www.bennychen.cn/wp-content/uploads/2009/06/catcher-in-the-rye-381x300.jpg 381w" sizes="(max-width: 400px) 100vw, 400px" /></a><strong>两年前的游戏Demo：麦田里的守望者</strong>
</p>

这一丢就是两年了，最近的大规模人群渲染项目，逼得我再次对frustum culling发出了呼唤，凭着模糊的记忆，再把frustum的一些原理复习了一下，不用1个小时，我就重拾了frustum culling的相关核心概念和技术，并获得了新的理解。

这是我从两年前就开始膜拜的Chad Vernon([www.chadvernon.com](http://www.chadvernon.com))大大的一段话：

When we tell DirectX to render geometry, it will perform a culling test to see if a vertex is within the view frustum before rendering the vertex. DirectX will only render a vertex if it is within the view frustum. However, this test occurs after the geometry has been transformed and lit by the world and view matrices. In more complex scenes, thousands of vertices would be transformed just to be rejected by the frustum test in DirectX. We can speed up performance quite a bit if we implement a view frustum culling test prior to our render calls.

DirectX本身在其pipeline中就会对顶点进行culling test的，但这要在顶点被&#8221;顶点变换与光照”(Transform&Lighting)之后。Vernon在写这段话的时候，应该还是DX9的时代。在DX10的文档里也赫然写着：(Rasterizer Stage)the stage clips vertices to the view frustum，是在VS,GS这些之后才进行。

而自己手工实现frustum culling的好处，就是可以将大量的非可视的顶点在送进渲染管线之前就被拒掉~

下面的这条链接对frustum culling有比较基础而详细的介绍(这哥们爆了好多粗口……)，同时进行了一系列的优化，这也让我对frustum culling有了更深的理解。里面所链接的那篇讲解如何构造frustum的文章，当我再次翻开它的时候，马上就从我大脑中的碎片中搜索并意识到，我两年前曾经读过这篇文章。记忆总是在某个似曾相识的环境下被突然的激活。

<http://www.flipcode.com/archives/Frustum_Culling.shtml>

另外DX10的时代早已来临，AMD的那篇March of Froblins的论文里，Frustum Culling和LOD已经全部是在GPU里进行了，通过了Geometry Shader的帮忙。在如今这个时代，貌似把任何运算转移到GPU，一切皆有可能。

打算最近把frustum culling相对于我目前所进行的人群渲染项目，在CPU和GPU都实现一个版本，并进行一些性能的比较。在我现在的项目里，估计实现后GPU的版本不一定就比CPU的跑的快，因为我的GPU已经承载了大量的人群渲染任务，而CPU到目前为止还基本是空闲的。