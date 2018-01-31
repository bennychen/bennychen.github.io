---
id: 243
title: std::bad_alloc?
date: 2009-06-29T17:34:51+08:00
author: Benny Chen
layout: post
guid: /?p=243
permalink: /2009/06/stdbad_alloc/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - vrHPWLXFkSbW
categories:
  - Some Experiences
---
今天遇到一诡异的Bug，在某段代码之后，只要一执行vector的push\_back函数，就报出异常——std::bad\_alloc。

bad_alloc？这一般是new申请内存不足而扔出的异常么，内存耗光？绝对不可能。

折腾了很久，最后发现的错误根源是如此的愚蠢。

对于类的一个成员变量 QuadNode *m_pChild[4]，我对它的初始化居然是这样的：

<pre class="brush: cpp; gutter: false; title: ; notranslate" title="">ZeroMemory( m_pChild, sizeof( QuadNode ) * 4 );
</pre>

很显然，sizeof( QuadNode )绝对是大错特错，因为m_pChild是一个指针数组，而不是QuadNode的数组。

其实这个错误不值得拿出来一说，只不过是Bug所表现出来的症状有些奇特，所以留文以作警示。