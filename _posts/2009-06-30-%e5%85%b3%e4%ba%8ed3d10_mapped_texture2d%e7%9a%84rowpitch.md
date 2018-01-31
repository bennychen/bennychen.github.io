---
id: 247
title: 关于D3D10_MAPPED_TEXTURE2D的RowPitch
date: 2009-06-30T17:02:04+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=247
permalink: '/2009/06/%e5%85%b3%e4%ba%8ed3d10_mapped_texture2d%e7%9a%84rowpitch/'
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - 5GOQCUJz4qGD
categories:
  - Computer Graphics
tags:
  - D3D10_MAPPED_TEXTURE2D
  - ID3D10Texture2D
  - RowPitch
  - width
---
当对一个ID3D10Texture2D进行Map操作时，会遇到D3D10\_MAPPED\_TEXTURE2D结构。该结构有一个属性是UINT RowPitch，如果没有很好的理解这个属性的含义，Map操作的结果很有可能是不对的。

这是DX10 SDK文档对RowPitch的解释：

> The pitch, or width, or physical size (in bytes), of one row of an uncompressed texture.

一个普通texture一行的字节总数就是它的RowPitch。但要特别注意的是：RowPitch并不就等于Texture2D的width乘以其每个纹元(texel)的字节数，即：

RowPitch ≠ width* sizeof (pixelFormat)

RowPitch总是大于等于后者，并且一般是等于一个2的n次幂。从上面也可以看出Pitch是以字节为单位，而width是以像素为单位的。

举例说明：

一个ID3D10Texture2D，创建它时所使用的D3D10\_TEXTURE2D\_DESC结构的Format属性是DXGI\_FORMAT\_R32G32B32A32\_FLOAT，即一个纹元占16(4&#215;4)个字节，Width属性是400，即每一行有400个纹元，则可计算每一行16 * 400 = 6400bytes。但如果对Texture2D进行Map操作时，可以发现，Map后所得到的D3D10\_MAPPED_TEXTURE2D结构的RowPitch的值却是8192(是大于6400的最小的2的n次幂）。

所以在进行Map操作时，需要针对RowPitch，而不要依赖定义texture时的width。

但是，在fx文件中对纹理进行采样的时候，针对的则是width，见如下fx代码。其中offset是相对于起点的偏移量，g_TexWidth是一个二维纹理的width，可见为了获得offset在纹理中的uv坐标，计算都是相对于width的，这时不用考虑pitch。

<pre class="brush: cpp; gutter: false; title: ; notranslate" title="">uint baseU = offset % g_TexWidth;
uint baseV = offset / g_TexWidth;
</pre>