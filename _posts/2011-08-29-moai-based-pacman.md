---
id: 1621
title: 基于Moai的Pacman
date: 2011-08-29T22:49:48+08:00
author: Benny Chen
layout: post
guid: /?p=1621
permalink: /2011/08/moai-based-pacman/
sfw_pwd:
  - SEiLKdM2xGiU
categories:
  - Game Programming
  - Lua
  - My Projects
tags:
  - 2d
  - github
  - Moai
  - oo
  - pacman
---
最近一两个星期，在业余时间利用Moai引擎重写了当年曾经用DirectX实现过的Pacman游戏。基于Lua的代码基本上是重写，只是保留使用了之前的2D资源。重写的目的一是通过这个来熟悉Moai引擎，二是希望用一个比较好的OO结构来重新实现Pacman，三则纯粹因为好玩。

把所有的代码,资源和文档都已经上传到了github，github真的是个好东东。

<a href="https://github.com/bennychen/Moai-based-Pacman" target="_blank">https://github.com/bennychen/Moai-based-Pacman</a>

介绍：

This is a prototype game based on the Moai game platform &#8211; <a href="http://getmoai.com" target="_blank">http://getmoai.com</a>. With various APIs for drawing and handling different 2D elements that Moai has supported, it clones a 2D Pacman game. 

I programmed the game for several objectives. Firstly, because I am just new to Moai, I try to familiarize myself with Moai&#8217;s various APIs and game development based on it. I did encounter some issues when programming the game. Some were because I am a newbie for Moai, and some were testified later that were from Moai engine itself. Although the process was time-consuming, but to my pleasure, this is somewhat a contribution to the open source Moai engine itself. I feel a great joy to be part of it. Secondly, the whole game is based on OO. So I tried to program the whole game with a decent OO structure. Most importantly, some of the Lua classes or files, hopefully, can be reused in my other future potential games. 

All in all, I programmed Moai-based Pacman for fun and share my code, related game assets and documents here. If there can be any help for you who are interested in Moai and OO game programming, that would be my best honer.

<a href="/wp-content/uploads/2011/08/pacman.png" class="highslide-image" onclick="return hs.expand(this);"><img src="/wp-content/uploads/2011/08/pacman-262x300.png" alt="" title="pacman" width="262" height="300" class="alignnone size-medium wp-image-1626" srcset="/wp-content/uploads/2011/08/pacman-262x300.png 262w, /wp-content/uploads/2011/08/pacman.png 559w" sizes="(max-width: 262px) 100vw, 262px" /></a>