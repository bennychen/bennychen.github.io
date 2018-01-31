---
id: 307
title: Some Tests
date: 2009-07-08T21:30:01+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=307
permalink: /2009/07/some-tests/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - 8BOW461nfBUm
categories:
  - Computer Graphics
tags:
  - frustum culling
  - instancing
  - skinning
---
在最近的项目中，尝试进行了一些小实验，并记录了数据作为比较。实验机器的显卡是NVidia Geforce 8800 GT。

### <span style="font-family: Arial;"><span style="font-size: medium;">1. Skinning</span></span>

<p style="text-align: left;">
  <a href="http://www.bennychen.cn/wp-content/uploads/2009/07/dhptbxnp_91f5tnxxcv_b.jpg" class="highslide-image" onclick="return hs.expand(this);"><img height="264" width="330" title="skinning on CPU" src="http://www.bennychen.cn/wp-content/uploads/2009/07/dhptbxnp_91f5tnxxcv_b.jpg" alt="skinning on CPU" /></a>
</p>

<p style="text-align: left;">
  <strong>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; skinning on CPU</strong>
</p>

<p style="text-align: left;">
  <a href="http://www.bennychen.cn/wp-content/uploads/2009/07/dhptbxnp_92dggphdg7_b.jpg" class="highslide-image" onclick="return hs.expand(this);"><img height="267" width="330" title="skinning on GPU" src="http://www.bennychen.cn/wp-content/uploads/2009/07/dhptbxnp_92dggphdg7_b.jpg" alt="skinning on GPU" /></a>
</p>

<p style="text-align: left;">
  <strong>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; skinning on GPU</strong>
</p>

从图中可以清晰的看出，对于骨骼蒙皮计算，CPU和GPU的差距可见一斑，GPU比CPU要快上10倍之多！这就是GPU并行计算的魅力！

### <span style="font-family: Arial;"><span style="font-size: medium;">2. Instancing & Stream Output</span></span>

人物模型延用上面的这个模型（该模型差不多有1300个面），采用instancing技术渲染上千人（所有的Instance的动画在每一帧保持一致），并且实现了SO的版本，对它们的FPS进行比较。

**Instancing without SO**

<img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/Primary Instancing_1000.jpg" />&nbsp; <img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/Primary Instancing_2000.jpg" />&nbsp; <img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/Primary Instancing_3000.jpg" />

&nbsp;&nbsp;&nbsp;&nbsp; 1,000 Instances&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2,000 Instances&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3,000 Instances

**Instancing with SO**

<img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/SO Instancing_1000.jpg" />&nbsp; <img height="116" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/SO Instancing_2000.jpg" />&nbsp; <img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/SO Instancing_3000.jpg" />

&nbsp;&nbsp;&nbsp;&nbsp; 1,000 Instances&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2,000 Instances&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3,000 Instances

<div style="text-align: left;">
  因为所有的Instance的动画在每一帧都是一致的，所以如果不使用SO技术，则不得不对每个instance都需要进行一次蒙皮动画，这显然是一种浪费。Stream Output技术使得所有的Instance的蒙皮动画只需要执行一次，所以在效率上得到了一定的提高，如下表。
</div>

<table cellspacing="0" cellpadding="3" bordercolor="#000000" border="1" width="100%" id="t3_o">
  <tr bgcolor="#d0e0e3">
    <td width="33%">
      <p style="text-align: center;">
        <b>Number of Instances</b>
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        <b>FPS(Without SO)</b>
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        <b>FPS(With SO)</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="33%">
      <p style="text-align: center;">
        1,000
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        40
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        56
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="33%">
      <p style="text-align: center;">
        2,000
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        19
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        36
      </p>
    </td>
  </tr>
  
  <tr>
    <td width="33%">
      <p style="text-align: center;">
        3,000
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        13
      </p>
    </td>
    
    <td width="33%">
      <p style="text-align: center;">
        24
      </p>
    </td>
  </tr>
</table>

### <span style="font-family: Arial;"><span style="font-size: medium;">&nbsp;3. Frustum Culling</span></span>

<p style="text-align: left;">
  换了一个低质量的模型（只有不到200个面），基本上万人级别是没有问题了，加上了Frustum Culling，则可以跑得更快！
</p>

<p style="text-align: left;">
  使用3种方法实现了Frustum Culling，前两个都是基于CPU的，最后一个是基于GPU
</p>

  1. 顺序执行（最简单的对所有Instance遍历执行Frustum Culling算法）
  2. 四叉树（四叉树裁剪，我这里用四叉树用得有点牵强，因为人物不能移动且必须保持边长为2的N次方的阵型。这么简单的处理四叉树，只是为了做实验比较而已）
  3. GPU上的Frustum Culling（算法基本上跟1是一致的，只不过是在GPU上执行）

三个方法在同一个视角，使得它们截取和渲染的数量是一致的。总共65536个Instance，当前绘制都是15485个。

<img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/16.jpg" />&nbsp; <img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/43.jpg" />&nbsp; <img height="117" width="150" alt="" src="http://www.bennychen.cn/wordpress/wp-content/uploads/32.jpg" />

**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 顺序执行&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;** **四叉树&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;** **在GPU**

效率比较如下表。

<div>
  <table cellspacing="0" cellpadding="3" bordercolor="#000000" border="1" id="rh57" style="width: 597px; height: 101px;">
    <tr bgcolor="#d0e0e3">
      <td width="25%" style="text-align: center;">
        <b>Current Rendering / Number of Instances<br /> </b>
      </td>
      
      <td width="25%" style="text-align: center;">
        <b>FPS<br /> (<span class="word">Sequential</span> Frustum Culling)<br /> </b>
      </td>
      
      <td width="25%" style="text-align: center;">
        <b>FPS<br /> (Frustum Culling With Quad-tree)<br /> </b>
      </td>
      
      <td width="25%" style="text-align: center;">
        <b>FPS<br /> (Frustum Culling on the GPU)<br /> </b>
      </td>
    </tr>
    
    <tr>
      <td width="25%" style="text-align: center;">
        4388/16384
      </td>
      
      <td width="25%" style="text-align: center;">
        61
      </td>
      
      <td width="25%" style="text-align: center;">
        143
      </td>
      
      <td width="25%" style="text-align: center;">
        102
      </td>
    </tr>
    
    <tr>
      <td width="25%" style="text-align: center;">
        15485/65536
      </td>
      
      <td width="25%" style="text-align: center;">
        16
      </td>
      
      <td width="25%" style="text-align: center;">
        43
      </td>
      
      <td width="25%" style="text-align: center;">
        32
      </td>
    </tr>
  </table>
  
  <p>
    发现最快的是使用四叉树，而GPU的算法反而相对比较慢。原因很有可能是后者需要处理GPU和CPU的同步问题，在GPU进行Frustum Culling需要CPU从GPU读取Frustum Culling执行的结果（为了知道有多少个instance需要被渲染），在DX10的SDK文档里可清楚的写着，从GPU向CPU进行Map读取操作，是比较严重影响效率的，因为总有一方要停下来去等待另外一方的数据。
  </p>
</div>