---
id: 221
title: indexed triangle lists最快
date: 2009-06-16T17:30:19+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=221
permalink: '/2009/06/indexed-triangle-lists%e6%9c%80%e5%bf%ab/'
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - XqJWeEIqVrel
categories:
  - Computer Graphics
tags:
  - 三角形列
  - 三角形带
---
一直以为，因为一条三角形带(triangle strip)会把处理与传输m个三角形的代价从3m个顶点降到（m＋2）个顶点，所以它是最高效的。今天从Gamedev的一篇帖子才知道，索引三角形列表(indexed triangle lists)才是最快的。

确实，三角形带减少了输入显卡的顶点数，但对于现今的显卡来说，带宽早就不是问题了！

至于为什么三角形带是最快的，因为在处理顶点时它可以最大化显卡缓存的使用率(cache hit ratio)。

下面这段话摘自Tom Forsyth的论文[_Linear-Speed Vertex Cache Optimisation_](http://home.comcast.net/~tom_forsyth/papers/fast_vert_cache_opt.html)，如何分配三角形的序列，以使的cache得到最好的利用。算法是贪心性质的，速度可以达到O(N)，有兴趣可以研究研究。

> <p class="MsoNormal">
>   <strong>Indexed primitives</strong> have replaced strips and fans as the most common rendering primitive in graphics hardware today. When rendering each triangle, the vertices it uses must be processed (for example, transformed to screen space and lit in various ways) before rendering. <strong>Indexed rendering hardware typically uses a small cache of vertices to avoid re-processing vertices that are shared between recently-rendered triangles.</strong> Using a moderately-sized cache (anywhere between 12 and 24 vertices is common) reduces the load on the vertex processing pipeline far more than the older non-indexed strips and fans model – often halving the amount of work required per triangle.
> </p>