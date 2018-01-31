---
id: 1654
title: 为SyntaxHighlighter添加新语言
date: 2011-09-04T13:36:03+08:00
author: Benny Chen
layout: post
guid: /?p=1654
permalink: /2011/09/add-new-brush-for-syntaxhighlighter/
sfw_pwd:
  - UKowFg1YKSXn
categories:
  - Some Experiences
tags:
  - lua
  - new brush
  - plugin
  - syntaxhighlighter
  - wordpress
  - 新语言
  - 添加
---
因为经常要在博客里贴一些Lua代码，但是所使用的SyntaxHighlighter插件默认不支持Lua语言，所以去研究了一下如何为SyntaxHighlighter添加并激活一个新的语言，这里将过程和有同样需求的童鞋分享。（因为我添加的是Lua语言，下面的过程描述会以Lua为例，在添加你所需要的语言时，你只要将相应的项更换为你的自定义设置即可）

1. 从这篇博客里寻找所需要的语言：<http://www.undermyhat.org/blog/2009/09/list-of-brushes-syntaxhighligher/>；
  
2. 下载对应的shBrushXXX.js脚本，比如我下载的是shBrushLua.js，它看起来像这样：

<pre class="brush: jscript; collapse: true; light: false; title: ; toolbar: true; notranslate" title="">SyntaxHighlighter.brushes.Lua = function()
{
	var keywords =	'break do end else elseif function if local nil not or repeat return and then until while this';
	var funcs = 'math\\.\\w+ string\\.\\w+ os\\.\\w+ debug\\.\\w+ io\\.\\w+ error fopen dofile coroutine\\.\\w+ arg getmetatable ipairs loadfile loadlib loadstring longjmp print rawget rawset seek setmetatable assert tonumber tostring';

	this.regexList = [
		{ regex: new RegExp('--\\[\\[[\\s\\S]*\\]\\]--', 'gm'),		css: 'comments' },
		{ regex: new RegExp('--[^\\[]{2}.*$', 'gm'),			    css: 'comments' },	// one line comments
		{ regex: SyntaxHighlighter.regexLib.doubleQuotedString,     css: 'string' },    // strings
		{ regex: SyntaxHighlighter.regexLib.singleQuotedString,     css: 'string' },    // strings
		{ regex: new RegExp(this.getKeywords(keywords), 'gm'),		css: 'keyword' },	// keyword
		{ regex: new RegExp(this.getKeywords(funcs), 'gm'),		    css: 'func' },		// functions
		];
}

SyntaxHighlighter.brushes.Lua.prototype	= new SyntaxHighlighter.Highlighter();
SyntaxHighlighter.brushes.Lua.aliases = ['lua'];
</pre>

3. 使用FTP工具登陆到WordPress空间，进入到wp-content/plugins目录，新建一个目录，取一个有意义的名字，比如syntaxhighlighter-lua；
  
4. 将shBrushLua.js上传到新创建的目录；
  
5. 在该目录创建一个另一个shBrushLua.php文件，添加如下内容：

<pre class="brush: php; title: ; notranslate" title="">&lt;?php
/*
Plugin Name: SyntaxHighlighter Evolved: Lua 
Description: Adds support for the Lua language to the SyntaxHighlighter Evolved plugin.
Author: Benny 
Version: 1.0.0
*/
 
// SyntaxHighlighter Evolved doesn't do anything until early in the "init" hook, so best to wait until after that
add_action( 'init', 'syntaxhighlighter_lua_regscript' );
 
// Tell SyntaxHighlighter Evolved about this new language/brush
add_filter( 'syntaxhighlighter_brushes', 'syntaxhighlighter_lua_addlang' );
 
// Register the brush file with WordPress
function syntaxhighlighter_lua_regscript() {
    wp_register_script( 'syntaxhighlighter-brush-lua', plugins_url( 'shBrushLua.js', __FILE__ ), array('syntaxhighlighter-core'), '1.1.1' );
}
 
// Filter SyntaxHighlighter Evolved's language array
function syntaxhighlighter_lua_addlang( $brushes ) {
    $brushes['lua'] = 'lua';
    return $brushes;
}
?&gt;
</pre>

6. 文件都准备完了，OK，进入到WordPress后台管理的Plugins下，应该能看到新添加的一项syntaxhighlighter-lua，激活它。

Done! It should work now!

其实新添加的js和php文件也可以放到SyntaxHighlighter插件本身的目录下，但是让它独立成插件的好处是，当SyntaxHighlighter升级时，你的个人配置不会因为覆盖而丢失。