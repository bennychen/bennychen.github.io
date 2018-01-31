---
id: 640
title: My Footsteps of Programming 3DS Max Exporter Plug-in
date: 2009-11-20T21:03:35+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=640
permalink: /2009/11/my-footsteps-of-programming-3ds-max-exporter-plug-in/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - jCwyBmHRUmO2
categories:
  - Computer Graphics
tags:
  - 3ds max
  - exporter
  - plug-in
  - 导出插件
---
Several months ago, I achieved to develop a 3DS Max exporter plug-in. But until recently, I didn’t get the time to jot down something about my experience while developing it. Finally, thank God, I am starting to write this article, in case that several years later I totally forget how I did it at that time.

As you know, 3DS Max is a powerful tool for creating 3D models, especially widely-used in games. But generally we cannot directly get our model from 3DS Max, otherwise, we need a sort of middleware to retrieve specified model data we need from 3DS Max and export them into a specific type of file. This middleware is right the exporter plug-in.

OK, with this concept in mind, let’s get started.

Step by step, I will roughly present my footsteps of programming a 3ds max exporter plug-in based on 3DS Max 9 SDK. 

**Get familiar with 3DS Max and SDK**

As we all know, there are a series of 3DS Max versions. Firstly you need to choose one to program based on. At the time of my embarking on this plug-in, the latest version is 3DS Max 2009. However, I chose Max 9 because it is already a widely-used and popular version. Most importantly, my laboratory was using Max 9.

Before doing some real programming jobs, it is pretty important to gain some basic concepts of 3ds max. We needn’t to be a perfect 3D designer ourselves, but these are the essential concepts we must know. 

  * what is a **Node** in 3ds max 
  * the hierarchical chart of nodes 
  * how materials are attached to a node 
  * what is a node’s Modifier 
  * key-frame animation and how bone nodes are attached to normal nodes 
  * the Biped technique

The fact is, the more we want to export from 3DS Max, the more we need to concern about it. For example, if we also want to export the data of camera or light from Max(meshes for games usually don’t export these data), we must apprehend what is a camera or light node in Max and how they work with other nodes.

But how the hell could we get the detail of these knowledge? Turn to the Max SDK Document, whenever you have any confusion with Max. Besides, we can also log on to Autodesk’s official Web site for more help.

BTW, the assumption here is that you are already familiar with some 3D concepts, like vertex, triangle, mesh, texture, space transformation, skinned animation, etc. If you are still a 3D newbie, you’d better figure them out firstly.

To be continued…

**Predefine mesh file format**

##### 

**Export static mesh data**

**Export skinned animation**