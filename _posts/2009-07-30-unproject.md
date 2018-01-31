---
id: 483
title: Unproject
date: 2009-07-30T11:19:44+08:00
author: Benny Chen
layout: post
guid: /?p=483
permalink: /2009/07/unproject/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - o8ELl5jIW4Lw
categories:
  - Computer Graphics
tags:
  - D3DXVec3Unproject
  - unproject
  - 投影矩阵
  - 投影空间
  - 视图空间
---
Unproject即反投影，将一个坐标从投影空间中反投影到视图空间。对于Project操作，很容易，直接用投影矩阵乘以视图空间的坐标即可，即：

**posProj = posView * matrixProj**

其中设posView = (x, y, z, 1)是视图空间的某一位置点的坐标，posProj = (x&#8217;, y&#8217;, z&#8217;, w&#8217;)为投影空间中该点的坐标，matrixProj为投影矩阵。我们知道，因为投影矩阵的特殊性，有w&#8217; = z，即视图空间的深度值存储在投影空间的w值中，这样posPorj的xyz坐标值在除以了w值之后，就有了近大远小的透视效果。注意：只有在除以了w值之后才会有，x&#8217;/w&#8217;，y&#8217;/w&#8217;∈[-1,1]且z&#8217;/w&#8217;∈[0,1]，即在那个所谓的半个正方体中了。

那么对于Unproject操作呢，是不是直接posView = posProj * inverse( matrixProj )就可以了呢，如果知道x&#8217;y&#8217;z&#8217;w&#8217;值，当然是正确，但关键问题是，大多数情况下，我们所知道的只是x&#8217;/w&#8217;，y&#8217;/w&#8217;以及z&#8217;/w&#8217;的值。

DX提供了一个函数D3DXVec3Unproject，可以做反投影，不过它只能在C++代码里使用（shader里没法使用），而且需要注意的是，这个函数是对屏幕空间的坐标进行的反投影（所以这个函数还需要传入一个viewport参数），务必不要将投影空间的坐标作为参数传递给这个函数。

我需要写一个自己的反投影函数，不妨从上面的这个公式推导：posProj = posView \* matrixProj，因为我们现在已知的是x&#8217;/w&#8217;，y&#8217;/w&#8217;以及z&#8217;/w&#8217;的值，不妨设posProj&#8217; = (x&#8217;/w&#8217;, y&#8217;/w&#8217;, z&#8217;/w&#8217;, 1 )，所以这个公式改为：posProj&#8217; \* w&#8217; = posView * matrixProj。

因为一般投影矩阵是像这样一种形式：

<a href="/wp-content/uploads/2009/07/projmatrix.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="aligncenter size-full wp-image-488" title="projmatrix" src="/wp-content/uploads/2009/07/projmatrix.jpg" alt="projmatrix" /></a>

于是有拆分posProj&#8217; \* w&#8217; = posView \* matrixProj后，有：

posProj&#8217;.x \* w&#8217; = posView.x \* xScale;
  
posProj&#8217;.y \* w&#8217; = posView.y \* yScale;
  
posProj&#8217;.z \* w&#8217; = posView.z \* Q &#8211; Q*zn;
  
w&#8217; = posView.z

上面构成一个方程组，解这个方程组，可得：

**posView.z = Q*zn / ( Q &#8211; posProj&#8217;.z )
  
posView.x = posView.z * posProj&#8217;.x / xScale
  
posView.y = posView.z * posProj&#8217;.y / yScale**

至此反投影完毕。

反投影一般比较常见的是在鼠标拾取(picking)的例子中，而那里投影空间的点取的是近屏幕上的点(posProj&#8217;.z = 0)，因为这样上面的公式就会得到一个比较简化的特例：

posView.z = zn
  
posView.x = posProj&#8217;.x * zn / xScale
  
posView.y = posProj&#8217;.y * zn / yScale