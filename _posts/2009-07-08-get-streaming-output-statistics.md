---
id: 300
title: Get streaming output statistics
date: 2009-07-08T13:33:08+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=300
permalink: /2009/07/get-streaming-output-statistics/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - XIcgTazEA1lD
categories:
  - Computer Graphics
tags:
  - D3D10_QUERY_SO_STATISTICS
  - ID3D10Query
  - stream output
---
有时候需要知道stream output了多少个数据，这需要借助于DX10中的ID3D10Query，步骤如下：

  1. 创建D3D10\_QUERY\_DESC结构，设置D3D10\_QUERY\_DESC::Query为D3D10\_QUERY\_SO\_STATISTICS，表明要调查的是SO的数据，设置D3D10\_QUERY_DESC::MiscFlags为0；
  2. 通过ID3D10Device::CreateQuery()创建ID3D10Query；
  3. 用ID3D10Query::Begin()和ID3D10Query::End()函数包裹需要调查的SO代码；
  4. 通过ID3D10Query::GetData()获取SO的统计数据，填充在一个D3D10\_QUERY\_DATA\_SO\_STATISTICS

示例代码如下：

<pre class="brush: cpp; title: ; notranslate" title="">// 创建ID3D10Query
ID3D10Query *d3dQuery;
D3D10_QUERY_DESC d3dQueryDesc;
d3dQueryDesc.Query = D3D10_QUERY_SO_STATISTICS;
d3dQueryDesc.MiscFlags = 0;
m_pD3DDevice-&gt;CreateQuery( &d3dQueryDesc, &d3dQuery );

// ...... ......

// 统计SO信息
d3dQuery-&gt;Begin();
// ......draw something with SO......
d3dQuery-&gt;End();

// ......最好在这里放置一些代码，填置CPU的空闲......

// 获取SO信息
D3D10_QUERY_DATA_SO_STATISTICS soData;
while ( S_OK != d3dQuery-&gt;GetData( ( void* )&soData, sizeof( soData ), 0 ) );
</pre>

值得注意的是，GetData一定要用while包裹，因为ID3D10Query从GPU获取数据是异步的（ID3D10Query继承自ID3D10Asynchronous）。这也是为什么最好能在GetData之前放置一些代码，因为CPU要等待GPU的数据，不如让CPU先去做些其他的事情，不要把CPU浪费在毫无意义的while循环等待上。