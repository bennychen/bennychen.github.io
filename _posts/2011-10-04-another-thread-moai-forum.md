---
id: 1677
title: Another Thread @ Moai Forum
date: 2011-10-04T11:07:48+08:00
author: Benny Chen
layout: post
guid: /?p=1677
permalink: /2011/10/another-thread-moai-forum/
sfw_pwd:
  - D3GOhTkEkrdn
categories:
  - Game Programming
  - Lua
tags:
  - callWithDelay
  - lua
  - Moai
  - setTimeout
---
### Does Moai support an API like &#8216;setTimeout&#8217;?

<a href="http://getmoai.com/forums/moai-sdk-developer-support/does-moai-support-an-api-like-settimeout/" target="_blank">http://getmoai.com/forums/moai-sdk-developer-support/does-moai-support-an-api-like-settimeout/</a>

**Me**
  
Hi,
  
Does Moai support an API like JavaScript&#8217;s setTimeout, which schedules a function execution after a specified time. I noticed there is a Moai class called MOAITimer, so I&#8217;m wondering if this class could be used for this purpose? I know a &#8216;setTimeout&#8217; function could be easily realized on Lua level using MOAISim:getElapsedTime() and Lua&#8217;s &#8216;pcall&#8217; function, but I&#8217;m just lazy and wonder if there is a shortcut that Moai has provided?
  
Thank you~

**Josh**
  
Moai doesn&#8217;t support that, but for convenience I&#8217;ll share the Lua function I use for this exact purpose:
  
(Hide some code)
  
EDIT: updated while investigating bug mentioned below. Will ensure this code works once bug is fixed.

**Me**
  
Thank you for the sharing, but the function gives the following error after I run:

> cannot use &#8216;…&#8217; outside a vararg function near &#8216;…&#8217;

I think &#8216;…&#8217; are also needed for &#8220;function()&#8221; in the 5th line, so I added it.
  
However, after I ran the following code

<pre class="brush: lua; title: ; notranslate" title="">function printAStr( str )	
    print( str )
end
callWithDelay( 1, printAStr, "helloWorld" )
</pre>

It prints &#8220;userdata: 0x3192c4&#8221; instead of &#8220;helloWorld&#8221;, any clue for this? I think the core issue is how to pass arguments to the callback function of timer&#8217;s listener?

**Josh**
  
I made two mistakes in my function, which I have corrected above. You are correct I forgot to pass the … properly, and I also forgot that the timer listener callback receives the timer as the first parameter, which is why you were seeing the userdata print out in your test.

**Me**
  
seems still unworkable for the arguments pass…
  
Ok, so my question is how to pass arguments to callback function of timer&#8217;s listener?

**Josh**
  
It looks like there may be a bug in the listener with the last release. I&#8217;ve opened a bug on the issue. I also realized yet another error in my above code, and have corrected it. Hopefully that&#8217;s the last edit I have to do. 🙂

**Patrick**
  
Thanks for finding the bug. Won&#8217;t be able to get to it for a day or so. Sorry for the trouble!

**Patrick**
  
OK, finally had time to look at this. It looks like it&#8217;s working correctly. The code I used is below.

<pre class="brush: lua; title: ; notranslate" title="">MOAISim.openWindow ( "test", 320, 480 )
function callWithDelay ( delay, func, ... )
  local timer = MOAITimer.new ()
  timer:setSpan ( delay )
  timer:setListener ( MOAITimer.EVENT_TIMER_LOOP,
    function ()
      timer:stop ()
      timer = nil
      func ( unpack ( arg ))
    end
  )
  timer:start ()
end

function printAStr ( str ) print( str ) end
callWithDelay ( 1, printAStr, "helloWorld" )
</pre>

**Me**
  
It works! Thanks a lot 🙂