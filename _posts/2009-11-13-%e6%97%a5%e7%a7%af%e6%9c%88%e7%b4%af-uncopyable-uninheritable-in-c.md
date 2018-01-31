---
id: 614
title: '日积月累: Uncopyable && Uninheritable in C++'
date: 2009-11-13T16:45:27+08:00
author: Benny Chen
layout: post
guid: /?p=614
permalink: '/2009/11/%e6%97%a5%e7%a7%af%e6%9c%88%e7%b4%af-uncopyable-uninheritable-in-c/'
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - 5u69ChWehOoM
categories:
  - C++
  - Talking in Code
tags:
  - C++
  - 禁止复制
  - 禁止继承
---
如何在C++中阻止复制和继承呢，我做了个整理。

**Uncopyable**
  
有些类需要禁止被复制或者赋值，这需要显示的禁掉它们的拷贝构造和赋值函数。
  
摘自《Effective C++》_Item 6: Explicitly disallow the use of compiler-generated functions you do not want_

<pre class="brush: cpp; title: ; notranslate" title="">class Uncopyable 
{
protected:                                   // allow construction
    Uncopyable() {}                       // and destruction of
    ~Uncopyable() {}                     // derived objects...

private:
    Uncopyable(const Uncopyable&);             // ...but prevent copying
    Uncopyable& operator=(const Uncopyable&);
};

class UncopyableExample: private Uncopyable 
{ 
    // THIS CLASS IS UNCOPYABLE
}; 
</pre>

**Uninheritable**
  
Java提供了final关键字用来阻止被继承，而在C++中没有这个关键字，但阻止继承的类在C++中是同样可能的。
  
摘自：<http://www.csse.monash.edu.au/~damian/Idioms/Topics/04.SB.NoInherit/html/text.html>

<pre class="brush: cpp; title: ; notranslate" title="">template &lt;class OnlyFriend&gt;
class Uninheritable
{
    friend class OnlyFriend;
    Uninheritable(void) {}
};

class NotABase : virtual public Uninheritable&lt; NotABase &gt;
{
    // WHATEVER
};

class NotADerived: public NotABase
{
    // THIS CLASS GENERATES A COMPILER ERROR
    NotADerived(){}
};
</pre>