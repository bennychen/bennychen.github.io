---
id: 1118
title: 关于从DLL导出模板函数
date: 2010-11-01T00:10:47+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=1118
permalink: '/2010/11/%e5%85%b3%e4%ba%8e%e4%bb%8edll%e5%af%bc%e5%87%ba%e6%a8%a1%e6%9d%bf%e5%87%bd%e6%95%b0/'
sfw_pwd:
  - FpiQQ5R6MdVz
categories:
  - C++
tags:
  - DLL
  - Singleton
  - 模板函数
---
模板在C++中本来就是个比较复杂的领域，而当它和DLL结合到一起时，事情变得更加有点复杂而有趣，最近就遇到了这样一个问题。

我有一个用于生成单件的类模板，它声明在一个DLL项目中，如下。

<pre class="brush: cpp; title: ; notranslate" title="">#if defined MY_EXPORTS
#define EXPORT_API __declspec(dllexport)
#else
#define EXPORT_API __declspec(dllimport)
#endif

template &lt; typename T &gt;
class Singleton
{
public:
	EXPORT_API static T& GetSingleton()
	{
		static T singleton;
		return singleton;
	}

// ...省去其它代码，比如隐藏构造函数，禁止复制构造和赋值函数..
};
</pre>

我试图导出GetSingleton()函数，因为我**希望**在其他导入该DLL的模块中能够通过调用这个函数而得到单例。基于该Singleton类模板定义一个单件的类很简单，只需要继承实例化的类即可，比如我在DLL内部定义了两个单件类，LogManager和ResourceManager，他们的定义看起来像这样。

<pre class="brush: cpp; title: ; notranslate" title="">class LogManager : public Singleton&lt; LogManager &gt;
{
// ......
}
class ResourceManager : public Singleton&lt; ResourceManager &gt;
{
// 该类因为需要记录日志，会使用LogManager类
// ......
}
</pre>

OK，DLL编译通过。然后，我在另一个项目模块中导入该DLL，我需要在这个模块中使用LogManager类和ResourceManager类，由于它们都是单件，正如前面所说的，我通过GetSingleton()函数得到单例，以访问它们的其它接口。

<pre class="brush: cpp; title: ; notranslate" title="">LogManager::GetSingleton()-&gt;...
ResourceManager::GetSingleton()-&gt;...
</pre>

然而，状况出现了，build该模块时出现了链接错误：unresolved external symbol &#8220;public: static class ResourceManager & __cdecl Singleton::GetSingleton(void)&#8221;” ，提示死活链接不到ResourceManager的GetSingleton()函数，然而同样是使用Singleton模板，LogManager的GetSingelton()函数却是链接成功的。

如果足够了解template的机制，就很容易找到问题所在。问题的原因是，**除非你显示的实例化（或者调用）了某个模板函数，否则这个模板函数在编译生成代码中（.obj）是根本不存在的**。所以，尽管我们为模板函数打上了export的标签，但其实什么也没有导出。而LogManager能链接成功，是因为它在DLL中（ResourceManager中）有使用，从而编译器生成了LogManager::GetSingleton()并导出。

所以，为了能够同样也导出ResourceManager::GetSingleton()，我们必须在DLL中提前显示的实例化它（[explicit instantiation](http://msdn.microsoft.com/en-us/library/by56e477%28v=VS.80%29.aspx)）。

LogManager和ResourceManager都是在DLL内部定义的单件类，那能不能在DLL外部也基于Singleton模板快速定义单件类呢？可以，不过GetSingleton这个函数对于其它模块就是隐形的了（根本不存在），你只能在每一个单件类中重新定义一个GetSingleton函数，以避免链接错误。

这个问题有没有完美的解决方案呢，即我们能否让一个模板函数完全导出，使得在别的模块中也可以自由的基于任意类型实例化函数呢？

去google上试图寻求答案，无果。不过说这个问题还真不少，比如CodeProject上的这篇文章：<http://www.codeproject.com/KB/cpp/MemberTemplateDLL.aspx> 。它提出可以使用inline的方式来解决这个问题，但不保证结果，因为本来编译器对于inline的处理就是个未知的行为。他同时还提出，这可能是一个跟编译器相关的问题，因为对于模板的处理是个跟编译器密切相关的行为。该作者在VC6.0上遇到了这个问题，我的是在VS2005（VC8.0）上，在其他不同平台或版本的编译器上会怎样我也没有测试过。

总之，记住很重要的一点，当导出一个模板函数时，需要格外的警惕。在MSVC编译器上，只有在DLL中被显示的实例化的版本，相应的函数才会被导出。

_PS：写于Costa Coffee@中关村，卡布奇诺喝得有点儿恶心了:-)_