---
id: 789
title: My Footsteps of Programming 3DS Max Exporter Plug-in (continued)
date: 2010-05-12T16:21:45+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=789
permalink: /2010/05/my-footsteps-of-programming-3ds-max-exporter-plug-in-continued/
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - 82iBjDLTILvh
categories:
  - Computer Graphics
tags:
  - 3ds max
  - exporter
  - plug-in
  - 导出插件
---
Oh my goodness, how long has it been since I wrote the first part of this article? What on hell was I buzzing around for during this period which seemed years to me? This plug-in is nearly becoming a legacy for me. Now it&#8217;s time to bring it back and finish the remaining parts. And I will also upload the whole project&#8217;s source code and related docs later.

First part of this article: <http://www.bennychen.cn/?p=640>

### Predefine mesh file format

After we&#8217;ve got some basic concepts about 3DS Max, now it&#8217;s time to define our own mesh file format. It&#8217;s very hard to directly define a format from the scratch. Researching some already typical and mature file formats is a good starting point. Recommended by my tutor, I chose .md2 & .md3 formats as my learning material which are famous for being used in the Quake series game. Based on several days&#8217; research on them, I defined my own mesh file format, which is sort of like .md2 & .md3 but also differs from them at some point.

I did not just define one format. They are a suite of formats, see the following table.

<table style="height: 174px;border:1px solid #000;margin-bottom:10px" dir="ltr" border="1" cellspacing="0" cellpadding="0" width="463" bordercolor="#000000" >
  <tr style="text-align: center;background:#000;">
    <td width="204" height="26" >
      <div>
        <span style="color: #ffffff; font-size: small;"><strong>File Type</strong></span>
      </div>
    </td>
    
    <td width="204" height="26">
      <div>
        <span style="color: #ffffff; font-size: small;"><strong>File Format</strong></span>
      </div>
    </td>
  </tr>
  
  <tr style="text-align: center;">
    <td width="204" height="26">
      <div>
        <span style="font-size: small;">Basic mesh file</span>
      </div>
    </td>
    
    <td width="204" height="26">
      <div>
        <span style="font-size: small;">*.CSM (Crowd Simulation Model)</span>
      </div>
    </td>
  </tr>
  
  <tr style="text-align: center;">
    <td width="204" height="26">
      <div>
        <span style="font-size: small;">Texture</span>
      </div>
    </td>
    
    <td width="204" height="26">
      <div>
        <span style="font-size: small;">*.jpg, *.bmp, </span><span style="font-size: x-small;">……</span>
      </div>
    </td>
  </tr>
  
  <tr>
    <td width="204" height="26">
      <div style="text-align: center;">
        <span style="font-size: small;">Animation</span>
      </div>
    </td>
    
    <td width="204" height="26">
      <div style="text-align: center;">
        <span style="font-size: small;">*.AM</span>
      </div>
    </td>
  </tr>
  
  <tr>
    <td width="204" height="26">
      <div style="text-align: center;">
        <span style="font-size: small;">Animation Configuration</span>
      </div>
    </td>
    
    <td width="204" height="26">
      <div style="text-align: center;">
        <span style="font-size: small;">*.CFG</span>
      </div>
    </td>
  </tr>
  
  <tr>
    <td width="204" height="26">
      <div style="text-align: center;">
        <span style="font-size: small;">Mounting Configuration</span>
      </div>
    </td>
    
    <td width="204" height="26">
      <div style="text-align: center;">
        <span style="font-size: small;">*.CSM_PACK</span>
      </div>
    </td>
  </tr>
</table>

There are 5 parts in total. But some of them are not necessary. Only one file is essential &#8211; the basic mesh file, which is named as CSM( Crowd Simulation Model ), because at that time I programmed this plug-in mainly for a crowd simulation project. The texture part is the textures file used by the mesh. If a mesh includes animation, the AM file( short for animation ) and animation configuration file(.CFG) are also needed. My format also supports mesh mounting, so if a mesh is mounted by several sub-meshes, .CSM_PACK is used to store the mount info.

These are some of the features of my format.

  * Support multi-texture
  * Support skinned animation
  * Decouple mesh and animation
  * Support animation configuration
  * Support mesh mounting

And the 2 figures below are the inside structure of CSM and AM. If you want to know more detail of them, later I will upload the docs.

![](http://docs.google.com/File?id=dhptbxnp_183dphcs5dv_b)

I stored my animation data in .AM file, bone by bone, animation by animation, and frame by frame.

<div id="ktgu">
  <img src="http://docs.google.com/File?id=dhptbxnp_184g3wp83cp_b" alt="" />
</div>

### Export static mesh data

OK, so far, all the preparation work is done. We can finally start the programming process.

For the last 2 steps, I will not present very detailed info here. I will only talk on the difficulties needed to overcome when I programmed them. All the programming job is strictly based on the format we defined last step. For more detail, please turn to my source code.

Firstly we need to handle the static mesh data. In this step, some difficulties are.

**1. Transformation between different types of coordinate system.**
  
Basically 3DS Max uses right-hand coordinate system. But some common rendering system( i.e. DirectX ) uses left-hand coordinate system. They have totally different coordinate systems, see figure below. So, the coordinate data needs to be transformed before outputting. See the figure below, the left is a typical rendering system&#8217;s coordinate system, and the right is of 3DS Max.

![](http://docs.google.com/File?id=dhptbxnp_185d4h7tnf6_b)

About how to transform, you can refer to this paper &#8211; 《[一种不同坐标系之间的变换矩阵的转换方法](http://d.wanfangdata.com.cn/Periodical_jsjfzsjytxxxb200001011.aspx)》by 杨卫东&刘玉树.

**2. Handle mesh with multiple textures.**
  
This is a tricky problem. We know that a single vertex could be shared by different faces, so if these faces are attached with different textures, the vertex should have multiple UV coordinates. This leads to the fact that we need to split the vertex into multiple vertices, so that we can ensure one vertex only maps to one UV coordinate. And the whole mesh needs to be reorganized.

From my CSM format definition, you may have found that a mesh is comprised of multiple submeshes. Actually the submeshes are split based on textures, that is, vertices using the same texture are categorized into one submesh. So if an original vertex is using 2 textures, this vertex is firstly split into 2 vertices and will exist under 2 submeshes respectively.

**3. Vertex normal**
  
This is another painful issue. About this topic, I once wrote an article in my blog before. Turn to it here.

### Export skinned animation

Now, we have come to the most complex, most sticky part of the whole work. I can still remember the time, the pain and the effort when I tried to tackle this problem. Those memories are so unforgettable. But they all deserve.

Why I did it so painfully was that, firstly I was still not familiar with the mechanism of bone animation at that time, secondly the mechanism of 3DS Max skeletal system is hard to figure out, whether its bone system or biped system, lastly, decouple the vertex data and the animation data is another challenge.

About the computational process of bone animation, thanks to this blog, it is the key article that helped me accomplish the whole work.
  
[http://www.cnblogs.com/neoragex2002/archive/2007/09/13/Bone_Animation.html](http://www.cnblogs.com/neoragex2002/archive/2007/09/13/Bone_Animation.html "http://www.cnblogs.com/neoragex2002/archive/2007/09/13/Bone_Animation.html"){#o9bx}

### Screenshots

The figure below is my exporter plug-in UI. The upper part is used to include animations, which will later generate .CFG file. Ant the lower part is used to configure mesh mounting, which will later generate .CSM_PACK file.

<img src="http://docs.google.com/File?id=dhptbxnp_187k2fj93gf_b" alt="" width="301" height="351" />

And here is an example of exporting. On the back is the 3ds max software interface, rendering a mesh with or without some animation. And on the front is the mesh viewer developed by me, which renders the same mesh with the same animation.

<img src="http://docs.google.com/File?id=dhptbxnp_188crjxqxd2_b" alt="" width="494" height="385" />

This is another example, the Tiananmen Gate.

<div id="lzn2">
  <img src="http://docs.google.com/File?id=dhptbxnp_186cz8gxxhb_b" alt="" />
</div>