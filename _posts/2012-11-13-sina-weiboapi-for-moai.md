---
id: 1713
title: 新浪微博API for MOAI
date: 2012-11-13T13:54:50+08:00
author: Benny Chen
layout: post
guid: /?p=1713
permalink: /2012/11/sina-weiboapi-for-moai/
sfw_pwd:
  - vnPOaE8fG7vV
categories:
  - Game Programming
  - iOS
  - Lua
tags:
  - lua
  - Moai
  - weibo
  - 微博
  - 新浪
---
最近一个星期干了一件事情，因为游戏的需要，为Moai引擎集成了新浪微博的API，这样也算为开源又一次出了份力。

最近一年基本上是游走于游戏的脚本层，很久没有碰引擎代码了，这一周的工作充满了怀旧感，虽然复杂度不算高，但还是勾起了不少当年开发游戏引擎的记忆。

check it out from <https://github.com/bennychen/moai-dev>, branch: sina_weibo

同时该branch也集成了另一位Moai开发者的截屏API， from <http://getmoai.com/forums/post4555.html>

如何使用[sample code]

<pre class="brush: lua; title: ; notranslate" title="">MOAISim.openWindow ( "test", 320, 480 )

viewport = MOAIViewport.new ()
viewport:setSize ( 320, 480 )
viewport:setScale ( 320, 480 )

layer = MOAILayer2D.new ()
layer:setViewport ( viewport )
MOAISim.pushRenderPass ( layer )

gfxQuad = MOAIGfxQuad2D.new ()
gfxQuad:setTexture ( "moai.png" )
gfxQuad:setRect ( -64, -64, 64, 64 )

prop = MOAIProp2D.new ()
prop:setDeck ( gfxQuad )
prop:setLoc ( 0, 80 )
layer:insertProp ( prop )

font = MOAIFont.new ()
font:loadFromTTF ( "arialbd.ttf", " abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789,.?!", 12, 163 )

textbox = MOAITextBox.new ()
textbox:setFont ( font )
textbox:setRect ( -160, -80, 160, 80 )
textbox:setLoc ( 0, -100 )
textbox:setYFlip ( true )
textbox:setAlignment ( MOAITextBox.CENTER_JUSTIFY )
layer:insertProp ( textbox )

--MOAISinaWeiboIOS.init( "you app id", "you app secret", "your callback url" )

function openCompileDialog( txt, imgFileLoc )
	MOAISinaWeiboIOS.compileDialog( txt, imgFileLoc )
	MOAISinaWeiboIOS.setListener( MOAISinaWeiboIOS.DIALOG_POST_CANCEL_CLICKED, function()
		print( "user clicked cancel post" )
		MOAIFileSystem.deleteFile( imgFileLoc )
	end )
	MOAISinaWeiboIOS.setListener( MOAISinaWeiboIOS.REQUEST_RESPONSE_WITH_RESULT, function()
		print( "post successuflly" )
		MOAIFileSystem.deleteFile( imgFileLoc )
	end )
end


function test()
	local frameBounds={
		left=0, 
		top=0, 
		width=1024, 
		height=768
	}

    local tmpFilename=MOAIScreenShotIOS.snapshotToFile( MOAIScreenShotIOS.PORTRAIT, frameBounds )
    print( "Saved at location: ", tmpFilename );
	print( MOAIFileSystem.checkFileExists( tmpFilename ))

	if ( not MOAISinaWeiboIOS.isAuthValid() )
	then
		MOAISinaWeiboIOS.login()
		MOAISinaWeiboIOS.setListener( MOAISinaWeiboIOS.DIALOG_LOG_IN_CANCEL, function() 
			MOAIFileSystem.deleteFile( tmpFilename )
		end )
		MOAISinaWeiboIOS.setListener( MOAISinaWeiboIOS.SESSION_DID_LOGIN, function()
			print( "successfully logged in" )
			openCompileDialog( "test", tmpFilename )
		end )
	else
		print( "already logged in, post" )
		openCompileDialog( "test", tmpFilename )
	end
end

timer = MOAITimer.new()
timer:setSpan( 1 )
timer:setListener( MOAIAction.EVENT_STOP, test )
timer:start()

</pre>