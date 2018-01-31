---
id: 276
title: ConstructGSWithSO
date: 2009-07-01T17:07:35+08:00
author: Benny Chen
layout: post
guid: /?p=276
permalink: /2009/07/constructgswithso/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - dpCcEvENw8Go
categories:
  - Computer Graphics
tags:
  - ConstructGSWithSO
  - DX10
---
如果要使用SO(Stream Output)，则在shader中必须使用ConstructGSWithSO函数来构造SO的GS，ConstructGSWithSO有两个参数。

  * **VertexShader/GeometryShader** &#8211; shader变量，因为SO的数据可以来自GS，也可以来自VS（如果GS为NULL），所以该参数可以是一个VertexShader，也可以是一个GeometryShader。（shader变量一般通过CompileShader函数获得）
  * **Semantics** &#8211; 描述SO的数据的semantic，该semantic须与上一个参数(shader变量)的shader函数的输出相一致。这个参数的格式要求比较变态，所以举例子说明比较好。

现有一个VS如下，需要SO它的输出数据：

<pre class="brush: cpp; gutter: false; title: ; notranslate" title="">struct VSInput
{
    ……（略）
};
struct VSOutput
{
    float3 pos : POSITION;
    float2 tex : TEXCOORD;
};
VSOutput VS VSInput
{
    VSOutput o;
    ……（略）
    return o;
}
</pre>

那么ConstructGSWithSO函数应该这么写：

GeometryShader gsSkinningSO = ConstructGSWithSO( CompileShader( vs\_4\_0, VSSkinning_SO() ), &#8220;**POSITION.xyz; TEXCOORD.xy**&#8221; );

看粗体部分的第二个参数，首先是双引号的字符串形式的，双引号内逐个列出了VSOutput的semantic，每个semantic同时要用.xyz,.xy这样的形式标明出它的维数（特别注意，如果只是一个float或uint的一维数据，同样也需要用.x来标出它是一维的！）。各个semantic之间用分号隔开，但注意，最后一个semantic后面不需要加分号（不然创建shader失败！）。