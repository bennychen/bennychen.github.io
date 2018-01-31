---
id: 669
title: 'Quotes from <<Clean Code: A Handbook of Agile Software Craftsmanship>> (1)'
date: 2010-01-20T17:57:40+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=669
permalink: /2010/01/quotes-from-clean-code-a-handbook-of-agile-software-craftsmanship-1/
syntaxhighlighter_encoded:
  - "1"
podPressPostSpecific:
  - 'a:6:{s:15:"itunes:subtitle";s:15:"##PostExcerpt##";s:14:"itunes:summary";s:15:"##PostExcerpt##";s:15:"itunes:keywords";s:17:"##WordPressCats##";s:13:"itunes:author";s:10:"##Global##";s:15:"itunes:explicit";s:2:"No";s:12:"itunes:block";s:2:"No";}'
sfw_pwd:
  - 2dEieCbWTyg6
categories:
  - Some Experiences
tags:
  - clean code
  - 代码简洁之道
---
最近在读这本Robert Martin的Clean Code，中文译本是《代码简洁之道》，我觉得非常好看。我一直比较注重写出简洁出色的代码，并且一直以为自己在这方面做的还不错，但随着这本书的阅读，虽然我的一些观点和想法被得到验证，但另外一些却完全被颠覆。也让我意识到离一个出色的专业程序员还有多大的差距。

> We’ve all looked at the mess we’ve just made and then have chosen to leave it for another day. We’ve all felt the relief of seeing our messy program work and deciding that a working mess is better than nothing. We’ve all said we’d go back and clean it up later. Of course, in those days we didn’t know LeBlanc’s law: Later equals never.

这样的经历真是太普遍了，我们写了一堆烂代码并且实现了某个功能，颇有成就感，但同时我们也意识到这是一堆垃圾代码，并且提醒自己等未来有空了一定要回来收拾这堆垃圾。但——Later equals never，我们再也没回来过。
  
所以，垃圾要及时处理，不然就这样遗臭下去了。

> Replace the temptation to create noise with the determination to clean your code. You’ll find it makes you a better and happier programmer.

程序员避免忧郁症的好方法就是遵循简洁之道。

> And now note the word that Bjarne uses to describe the consequence of that inelegance. He uses the word “tempt.” There is a deep truth here. Bad code tempts the mess to grow! When others change bad code, they tend to make it worse.

当面对一堆烂代码时,很少有人愿意去收拾这堆烂摊子,结果就是让它们烂到极致.(让我想起之前做过的一个flash项目，接手时是噩梦般的代码，我只能东补西凑，到最后功能加的越多，越是恐怖的混乱，几乎完全无法维护）

> You get the drift. Indeed, the ratio of time spent reading vs. writing is well over 10:1. We are constantly reading old code as part of the effort to write new code. Because this ratio is so high, we want the reading of code to be easy, even if it makes the writing harder. Of course there’s no way to write code without reading it, so making it easy to read actually makes it easier to write.

这和我以前一直所想的观点正好契合，写只有1次，而读会有N次（N会随着不同的项目有着巨大的差别），所以为了那更多更多次的读，多花时间在写上是绝对值得的。而Martin给出了读写的比例至少在10：1的样子，他还提出了“要想写容易，得先容易读“。

> It was the bad code that brought the company down.

烂代码让这家公司歇菜了！烂代码的威力如此恐怖，对于这点，我坚信不疑。

> Clean code is simple and direct. Clean code reads like well-written prose.

简洁的代码读起来应该像优美的散文。

> One difference between a smart programmer and a professional programmer is that the professional understands that clarity is king. Professionals use their powers for good and write code that others can understand.

聪明的程序员不等于专业的程序员。

> In short, a programmer who writes clean code is an artist who can take a blank screen through a series of transformations until it is an elegantly coded system.

我热爱艺术，所以我热爱简洁漂亮的代码。我梦想成为一个艺术家，从程序员入手是个不错的选择。