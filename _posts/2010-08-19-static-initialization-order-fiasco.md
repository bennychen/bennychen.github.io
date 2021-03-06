---
id: 999
title: Static Initialization Order Fiasco
date: 2010-08-19T12:59:45+08:00
author: Benny Chen
layout: post
guid: /?p=999
permalink: /2010/08/static-initialization-order-fiasco/
sfw_pwd:
  - EEIUBTYc6Wbj
categories:
  - C++
tags:
  - crash
  - static initialization order fiasco
  - 初始化
  - 崩溃
  - 静态变量
  - 顺序
---
Static Initialization Order Fiasco (SIOF)，我也是最近才知道了这个说法，因为在开发程序的时候被它bug了：对于一个static变量，不管它是全局的或者是类的成员变量，访问它的时候不一定总是成功的，甚至会造成程序crash，因为不能保证它在被访问时已经被初始化了（跟初始化的顺序有关，所以称为初始化顺序的Fiasco）。以下将制造一个非常简单的SIOF情形：

Whatever.h

<pre class="brush: cpp; title: ; notranslate" title="">#include &lt;vector&gt;
#include &lt;string&gt;
class Whatever
{
public:
	Whatever()
	{
		cout &lt;&lt; "Construct Whatever" &lt;&lt; endl;
		Display();
	}
	~Whatever()
	{
		cout &lt;&lt; "Destruct Whatever" &lt;&lt; endl;
		Display();
	}
	void Display()
	{
		cout &lt;&lt; "static int:" &lt;&lt; i &lt;&lt; endl;
		cout &lt;&lt; "static string:" &lt;&lt; m_str &lt;&lt; endl;
		cout &lt;&lt; "static vector:" &lt;&lt; m_vec.front() &lt;&lt; endl;
	}

private:
	static int i;
	static std::string m_str;
	static std::vector&lt;char&gt; m_vec;
};
</pre>

Whatever.cpp

<pre class="brush: cpp; title: ; notranslate" title="">#include "Whatever.h"

int Whatever::i = 500；
string Whatever::m_str = "something";
vector&lt;char&gt; Whatever::m_vec = vector&lt;char&gt;( 10, 'a' );
</pre>

一个简单的类，Whatever，包含几个static成员变量，然后在构造函数和析构函数中都分别打印这些静态变量的值，乍一看似乎没什么问题，但却有潜在的SIOF的风险。我们容易默认为在调用Whatever的构造函数的时候，Whatever空间中的static的成员变量已经被初始化了，其实不然，现在制造一个SIOF引起crash的情形：

<pre class="brush: cpp; title: ; notranslate" title="">#include "Whatever.h"
Whatever g_whatever; 
int main()
{
	...
}
</pre>

因为g_whatever是global变量，所以最先被初始化，在调用Whatever的构造函数的时候，Whatever空间的静态成员变量还未被初始化，所以访问这些静态变量肯定出错。在VS的编译器下测试的结果：

> Construct Whatever
  
> static int:5
  
> static string:
  
> （调用m_vec.front()导致程序crash）</blockquote> 

奇怪的是对于int这种built-in的类型却能得到正确的值，不知编译器在背后都做了哪些手脚，猜想可能是在程序编译的时候他们就被值替换了。而string和vector应该都属于自定义类型（初始化需要调用构造函数），未初始化之前访问肯定是错误的，所以打印出的string是个空值，而访问一个空的vector的front元素则直接造成程序crash。

根据初始化和释放的对称关系，所以在析构函数中访问这些静态变量同样也是失败的，因为在析构g_whatever的时候，Whatever空间的静态变量已经被解决掉了。

SIOF是非常难于检测的问题，这个例子是一种最简单的情形，在我的项目中，我并没有定义什么global的成员，但是因为使用了很多前置声明（forward declaration），还有一些Singleton，造成了一个非常隐蔽的SIOF，花了很大的力气才找到，痛苦的过程。

要解决SIOF问题，需要用一个function来包装static变量，即利用函数内static变量的construct-on-first-use特性。

修改后的Whatever.h

<pre class="brush: cpp; title: ; notranslate" title="">class Whatever
{
public:
	Whatever()
	{
		cout &lt;&lt; "Construct Whatever" &lt;&lt; endl;
		Display();
	}
	~Whatever()
	{
		cout &lt;&lt; "Destruct Whatever" &lt;&lt; endl;
		Display();
	}
	void Display()
	{
		cout &lt;&lt; "static vector:" &lt;&lt; GetStaticVector().front() &lt;&lt; endl;
	}

private:
	vector&lt;char&gt;& GetStaticVector()
	{
		static vector&lt;char&gt; vec = vector&lt;char&gt;( 10, 'a' );
		return vec;
	}
};
</pre>

用GetStaticVector来包装之前所需要的静态的vector，就能保证在调用的时候，它一定已经被初始化了。再次运行之前的测试程序，OK了。

总之，我们对于static变量的使用要保持一颗警惕的心，如果不确定在使用时它是否已经被初始化，就要使用函数包装static变量来防止Static Initialization Order FIASCO!
  
Fiasco, what a cool word.