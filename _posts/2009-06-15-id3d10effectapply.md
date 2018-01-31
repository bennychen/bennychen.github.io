---
id: 208
title: ID3D10EffectPass::Apply
date: 2009-06-15T17:13:43+08:00
author: Benny Chen
layout: post
guid: /?p=208
permalink: /2009/06/id3d10effectapply/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - QRKgSJabtbEE
categories:
  - Computer Graphics
tags:
  - Apply
  - DX10
  - ID3D10EffectPass
---
因为没有很好的理解这个函数的作用，而导致被一个Bug纠缠了半天。

这个Bug是这样的：渲染一个人物模型和一个地板模型，它们分别有不同的纹理，但渲染出来的结果却是——人物的纹理贴到了地板上，地板的纹理贴到了人物上，纹理错位了！

我明明在渲染前都分别将各自的纹理视图(ID3D10ShaderResourceView)设置到shader的纹理接口(ID3D10EffectShaderResource)上了啊，怎么会出现如此诡异的现象。

调的很崩溃，freaking me out&#8230;对于bug源的推理，我磨了好久，才想到了去打破我的思维定势——真正启动Shader开始执行的是Draw函数，所以对于渲染状态的设置只需要在Draw函数前设置就是有效的。

前半句没错，后半句，非也！

我忽视了Apply函数的作用，它不仅仅只是挑选某个pass而已。

这是DirectX SDK Document对ID3D10EffectPass::Apply的描述：

> **Set the state contained in a pass to the device.**

将状态提交到设备。马上我就恍然大悟，我设置纹理的语句是在Apply之后进行的。把设置纹理的语句提到Apply函数之前，于是，纹理物归原主。

调试BUG，除了需要很好的逻辑推理排错能力，还要勇于去怀疑一些思维定势（当然也没必要怀疑一切），有时，或许越是深信不疑的某些东西就是错误的根源，打破它，错误也便迎刃而解。

<a href="/wp-content/uploads/2009/06/texture-bug.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="alignnone size-full wp-image-212" title="texture-bug" src="/wp-content/uploads/2009/06/texture-bug.jpg" alt="before bug was fixed" srcset="/wp-content/uploads/2009/06/texture-bug.jpg 326w, /wp-content/uploads/2009/06/texture-bug-300x232.jpg 300w" sizes="(max-width: 326px) 100vw, 326px" /></a>
  
**before bug was fixed**

<a href="/wp-content/uploads/2009/06/texture-bug-fixed.jpg" class="highslide-image" onclick="return hs.expand(this);"><img class="alignnone size-full wp-image-213" title="texture-bug-fixed" src="/wp-content/uploads/2009/06/texture-bug-fixed.jpg" alt="after bug was fixed" srcset="/wp-content/uploads/2009/06/texture-bug-fixed.jpg 326w, /wp-content/uploads/2009/06/texture-bug-fixed-300x233.jpg 300w" sizes="(max-width: 326px) 100vw, 326px" /></a>
  
**after bug was fixed**