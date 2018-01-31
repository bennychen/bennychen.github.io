---
id: 738
title: '[译]Google的C++风格参考指南（1）- 头文件'
date: 2010-03-28T20:15:20+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=738
permalink: '/2010/03/%e8%af%91google%e7%9a%84c%e9%a3%8e%e6%a0%bc%e5%8f%82%e8%80%83%e6%8c%87%e5%8d%97%ef%bc%881%ef%bc%89-%e5%a4%b4%e6%96%87%e4%bb%b6/'
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:7:"Default";s:12:"itunes:block";s:7:"Default";}'
sfw_pwd:
  - zWk1MjEJ5Nud
categories:
  - C++
tags:
  - C++
  - Google
  - 参考指南
  - 头文件
  - 风格
---
**译者的话：**

在一个开发团队中，很重要的一点是要遵循一个统一的风格，这样才能便于相互之间在代码上进行快速的交流和理解。反之，如果团队中的每个人只是按照自己的个性去写属于个人风格的代码，这必然会造成项目代码的五花八门，互相之间难以沟通，整个项目的代码质量低下，BUG率攀高，软件的可维护率也极差。长此以往，国将不国，团队将不团队，最终软件极有可能以一种极其混乱的状态收尾，这真是一个悲剧。

每一个C++程序员都有自己的编程风格，虽然大家的风格迥异，但也一定有一些共通的地方。我希望能找到一个对于C++风格的归纳和总结，这样在以后我的工作中，对我自己，甚至对我工作的团队，当然也对于正在阅读的你，能够有一个帮助和参考。所以，我决定翻译Google的这篇C++风格参考指南。当然，真正促使我去翻译这篇文章的原因是，我是一个C++技术和简洁代码风格的狂热追捧者。

注意，这只是一篇参考，它是Google的C++风格，你并不一定严格的遵循所有的规则，永远记住很重要的一点，风格永远跟着你的团队走。

本人能力有限，所以不一定每个地方都翻译的准确，另外一些不太好翻译的专业名词首次出现时，我会标注英文原文。

原文：<a title="Google C++ Style Guide" href="http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml" target="_blank">Google C++ Style Guide</a>

## 背景 （Background） {#rgs3}

C++是很多Google开源项目所使用的开发语言。正如每一个C++程序员所熟知的，C++有很多强大的功 能，但功能强大也同时意味着复杂性(complexity)，使得代码bug率更高且难以阅读和维护。

这篇参考指南 (guide)的目的在于，通过详细描述应该做的(dos)和不应该做的(don&#8217;ts)，来控制我们撰写C++代码时的复杂性。这些规则令程序员在高效利用C++语言特性的同时，还能让手工写出的所有代码(code base)易于管理。

风格(style)，或者可 读性(readability)，我们把它称作为“用来管理我们C++代码的常用惯例(convention)”。不过用“风格”这个术语有些不当，因为这些惯例并不只是指代码源文件的格式。

让代码可管理，一种方式是加强代码的一致性(consistency)。令任何一个程序员能够快速的理解另一个程序员所写的代码是非常重要的。如果代码保持统一的风格并且遵循惯例，我们可以更加轻松的使用“模式匹配”(pattern- matching)推断出，不同的符号分别代表什么。创造这种代码公共性(common)需要习语(idiom)和模式(pattern)，使得代码易于理解。在某些情况下，也许有好的理由来改变某些风格规定，但为了获得一致性，不管怎样我们还是得保持风格。

这篇参考指南所关注的另一点是C++的功能膨胀问题。C++是一个有很多高级特性的庞大语言。在某些情况下，我们限制甚至禁止使用一些特性。这样做是为了保持代码的简洁，并避免因使用这些特性而带来各种常见的错误和问题。指南里会列出这些特性，并解释为何限制使用它们。

Google 所开发的开源项目遵守这篇参考指南里所列出的规定。

注意，这篇指南不是C++教程，我们认为所有正在阅读的同志们都是熟悉C++的。

## 头文件（Header Files） {#kcsk}

一般而言，每个.cc文件都有一个关联.h文件。但也有一些例外，比如用于单元测试(unit test)的.cc文件或者只包括一个main()函数的小型.cc文件。

正确的使用头文件可以让代码的可读性、规模(scale)和性能(performance)得到很大的改观。

接下来介绍的一些规则将会引导你越过使用头文件时的各种陷阱。

### #define防护（The #define Guard）

所有的头文件都应该使用#define防护来防止多重包含(multiple inclusion)，格式是<span style="font-family: Courier New;"><em><PROJECT></em>_<em><PATH></em>_<em><FILE></em>_H_</span>

<div id="zwnn">
  <span style="font-family: Courier New;">为了保证唯一性(uniqueness)，该格式应该基于源文件在整个项目中的路径。比如说，项目foo中的头文件foo/src/bar/baz.h应该用如下的格式：<br /> <span style="font-family: Courier New;">#ifndef FOO_BAR_BAZ_H_<br /> #define FOO_BAR_BAZ_H_</p> 
  
  <p>
    &#8230;
  </p>
  
  <p>
    #endif // FOO_BAR_BAZ_H_
  </p>
  
  <p>
    </span></span>
  </p>
  
  <h3>
    头文件依赖（Header File Dependencies）
  </h3>
  
  <p>
    <span style="font-family: Courier New;">如果使用前置声明(forward declaration)就可以解决依赖，就不要用#include。</span>
  </p>
  
  <div id="pl-d">
    <p>
      <span style="font-family: Courier New;">当你#include一个头文件的时候，你同时也引入了依赖关系————每当头文件有任何的修改，包含该头文件的代码也要重新编译。如果你的头文件还包含了其他的头文件，那任何对于这些文件的修改会导致任何包含了你的头文件的代码重新编译。因此，我们倾向于尽量减少使用#include，尤其避免在一个头文件中包含另一个头文件。</span>
    </p>
    
    <p>
      <span style="font-family: Courier New;">使用前置声明，可以显著的减少在你的头文件中#include其他头文件的次数。比如说，如果你的头文件要用到class File，但你不需要访问class File的详细定义，那你的头文件只需前置声明class File即可，而不是#include &#8220;file/base/file.h&#8221;。</span>
    </p>
    
    <p>
      那在哪些情况下，我们是只使用一个类（比如 class Foo），而无需访问它的定义呢？
    </p>
  </div></div> 
  
  <ul>
    <li>
      <span style="font-family: Courier New;"> 声明类数据成员类型<span style="font-family: Courier New;">为 Foo*</span> 或者<span style="font-family: Courier New;"> Foo&</span> </span>
    </li>
    <li>
      <span style="font-family: Courier New;">声明（但不定义）参数或返回值为Foo的函数<br /> </span>
    </li>
    <li>
      <span style="font-family: Courier New;">声明静态数据成员类型为Foo。这是因为静态数据成员在class之外定义</span>
    </li>
  </ul>
  
  <p>
    另 一方面，如果你的类继承自Foo或者你有一个类数据成员是Foo，那就必须包含Foo的头文件。
  </p>
  
  <p>
    有些时候，使用指针 （scoped_ptr 更佳）成员而不是对象成员(object member)是明智的。然而这却降低了代码的可读性，并且导致性能损失。所以，如果这么做的目的只是减少头文件中的#include，那就别这么做。
  </p>
  
  <p>
    当 然，.cc文件必须需要它所使用的类的定义，所以它会#include比较多的头文件。
  </p>
  
  <h3>
    内联函数（Inline Functions）
  </h3>
  
  <p>
    只当函数很小，比如只有10行或更少时，才定义它为内联函数。
  </p>
  
  <div id="om_.">
    <p>
      解释：你可以以这种方式定义函数，该方式允许编译器将函数内联展开，而不是以传统的函数调用机制去调用它们。
    </p>
    
    <p>
      优点：内联函数可以产生更加高效的目标代码(object code），但前提是内联函数要足够小。你可以随意的将这些函数设置为内联函数：访问函数(accessor)，设置函数(mutator)，以及其他的短小的并与性能紧密相关的函数。
    </p>
    
    <p>
      缺点：过度使用内联函数会导致程序变慢。内联一个函数时，根据该函数的大小，内联会增加或降低代码的规模。内联一个很小的访问函数通常会降低代码规模，而内联一个非常庞大的函数则会大大增加代码的规模。在现代的处理器 (processor)上，因为使用指令缓存(instruction cache)的缘故，短小的代码通常跑的更快。
    </p>
    
    <p>
      结论：
    </p>
    
    <p>
      一个不错的经验法则是，不要内联超过10行的函数。要小心析构函数，它们通常要比表面上看到的要长很多，因为它们会隐式的调用成员或者基类的析构函数。
    </p>
    
    <p>
      另一个有用的经验是，内联带有循环或者switch语句的函数是低效的（除非循环或switch语句从未被执行过）。
    </p>
    
    <p>
      还有重要的一点是，就算函数被声明为内联，但它不一定就被内联。比如说，虚函数或者递归函数通常都不被内联。一般我们也不应该声明递归函数为内联函数。而通常我们把一个虚函数声明为内联函数的主要原因是，将它的定义放在类定义内，这样是为了图方便或者将它文档化(document)的行为，比如说在访问函数和设置函数上我们就是这么干的。
    </p>
    
    <h3>
      -inl.h文件（The -inl.h Files）
    </h3>
    
    <p>
      当你需要使用复杂的内联函数时，你可以使用文件名 后缀为-inl.h的文件来定义它。<a href="http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml?showone=The_-inl.h_Files#The_-inl.h_Files"></a>
    </p>
    
    <div id="tk9n">
      <p>
        内联函数的定义要放在头文件里，这样编译器在函数调用点才能访问到定义用于内联。然而，函数的实现代码通常放在.cc中，而且我 们也不喜欢把太多的实际代码放在.h文件中，除非这么做有利于可读性或性能。
      </p>
      
      <p>
        如果一个内联函数的定义很短， 里面几乎没有任何逻辑，你应该把这样的定义代码放到.h文件中。比如说，访问函数和设置函数应毫无疑问地放在类定义中。稍复杂一些的内联函数定义也可能放 在.h文件中，这样能方便实现者(implementer)和调用者(caller)。尽管这样做可能使得.h文件有些笨重，但你可以把代码放到另一个 -inl.h文件中。这将实现(implementation)从类定义中分离，而且使得实现能够在需要的地方被包含。
      </p>
      
      <p>
        -inl.h 文件还可以用于函数模板的定义，这使得你的模板定义易于阅读。
      </p>
      
      <p>
        当然不要忘了，像其他的头文件一样，-inl.h文件 也需要#define防护。
      </p>
      
      <h3>
        函数参数顺序（Function Parameter Ordering）
      </h3>
      
      <p>
        当定义一个函数时，参数的顺序应该是：先输入参数，再输出参数。
      </p>
      
      <div id="gv51">
        <p>
          C/C++函数的参数可以是函数的输入，函数输出，或者两者兼有。输入参数通常是值(values)或者常量引用（const reference)，而输出参数和输入/输出参数是非常量指针(non-const pointer)。对于函数参数的排序，将纯输入参数放在任何输出参数之前。尤其不要因为一个参数是新添加的，就把它放在函数参数的最后，要将纯输入参数放在输出参数之前。
        </p>
        
        <p>
          这不是个硬性规则。那种既是输入又是输出的参数（通常是class或者struct)会让情况变 得更复杂，还有一点，就像前面所说的一样，为了与相关函数保持一致性，也会需要违背这一规则。
        </p>
      </div>
      
      <h3>
        文件包含的名字和顺序（Names and Order of Includes）
      </h3>
      
      <p>
        请使用以下标准顺序以保证可读性，并且避免隐藏的依赖：C库，C++库，其他库的.h，你的项目的.h。
      </p>
      
      <div id="kfl1">
        <p>
          所有项目的头文件应作为项目源文件目录的后代列出，不要使用UNIX中的目录快捷方式 . (当前目录）和 .. (父目录）。
        </p>
        
        <p>
          举个例子，<span style="font-family: Courier New;">google-awesome-project/src/base/logging.h</span>应该以<span style="font-family: Courier New;">#include &#8220;base/logging.h&#8221;</span>的格式包含。
        </p>
        
        <p>
          在 dir/foo.cc中，该文件的主要目的是实现或者测试在dir2/foo2.h中的东西，文件包含应按照如下排序：
        </p>
        
        <ol>
          <li>
            <span style="font-family: Courier New;">dir2/foo2.h</span> (首选位置(preferred location) — 请见下面的详细说明).
          </li>
          <li>
            C system files.
          </li>
          <li>
            C++ system files.
          </li>
          <li>
            Other libraries&#8217; <span style="font-family: Courier New;">.h</span> files.
          </li>
          <li>
            Your project&#8217;s <span style="font-family: Courier New;">.h</span> files.
          </li>
        </ol>
        
        <p>
          首选的排序（preferred ordering，译者注：在以上例子中为<span style="font-family: Courier New;">dir2/foo2.h</span> ）降低了隐藏的依赖。我们希望每个头文件能够自我编译。最简单的方式就是确保它们中的每一个都在某个.cc文件中是第一个被#include的。
        </p>
        
        <p>
          <span style="font-family: Courier New;">dir/foo.cc</span> and <span style="font-family: Courier New;">dir2/foo2.h</span>通常在同一目录下（比如<span style="font-family: Courier New;">base/basictypes_unittest.cc</span> and <span style="font-family: Courier New;">base/basictypes.h</span>），但也可能在不同的目录下。
        </p>
        
        <p>
          在每个部分(section)中，根据字母顺序排列是不错的方式。
        </p>
        
        <p>
          举个例子，<span style="font-family: Courier New;">google-awesome-project/src/foo/internal/fooserver.cc</span>中的文件包含顺 序可能是这样：
        </p>
        
        <div>
          <p>
            <span style="font-family: Courier New;">#include &#8220;foo/public/fooserver.h&#8221; // 首选位置</span>
          </p>
          
          <p>
            #include <sys/types.h><br /> #include <unistd.h>
          </p>
          
          <p>
            #include <hash_map><br /> #include <vector>
          </p>
          
          <p>
            #include &#8220;base/basictypes.h&#8221;<br /> #include &#8220;base/commandlineflags.h&#8221;<br /> #include &#8220;foo/public/bar.h&#8221;
          </p>
          
          <p>
            未完待续&#8230;
          </p>
        </div>
      </div>
    </div>
  </div>