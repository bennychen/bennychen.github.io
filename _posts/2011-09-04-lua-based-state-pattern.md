---
id: 1638
title: 基于Lua的State Pattern
date: 2011-09-04T12:56:21+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=1638
permalink: /2011/09/lua-based-state-pattern/
sfw_comment_form_password:
  - 4NQyJfNJBDnW
sfw_pwd:
  - KbngH5lmd1dT
categories:
  - Game Programming
  - Lua
tags:
  - finite state machine
  - FSM
  - lua
  - state pattern
  - 状态模式
---
代码来自于最近写的Pacman，更多请查看 &#8211; <a href="https://github.com/bennychen/Moai-based-Pacman" target="_blank">https://github.com/bennychen/Moai-based-Pacman</a>

class.lua实现了在Lua中创建类的模拟，非常方便。class.lua参考自<a href="http://lua-users.org/wiki/SimpleLuaClasses" target="_blank">http://lua-users.org/wiki/SimpleLuaClasses</a>

<pre class="brush: lua; collapse: true; light: false; title: ; toolbar: true; notranslate" title="">-- class.lua
-- Compatible with Lua 5.1 (not 5.0).

function class(base, init)
   local c = {}    -- a new class instance
   if not init and type(base) == 'function' then
      init = base
      base = nil
   elseif type(base) == 'table' then
    -- our new class is a shallow copy of the base class!
      for i,v in pairs(base) do
         c[i] = v
      end
      c._base = base
   end
   -- the class will be the metatable for all its objects,
   -- and they will look up their methods in it.
   c.__index = c

   -- expose a constructor which can be called by &lt;classname&gt;(&lt;args&gt;)
   local mt = {}
   mt.__call = function(class_tbl, ...)
   local obj = {}
   setmetatable(obj,c)

-- below 2 lines are updated based on the Comments from 'http://lua-users.org/wiki/SimpleLuaClasses'
--   if init then
--      init(obj,...)
   if class_tbl.init then
      class_tbl.init(obj,...)
   else 
      -- make sure that any stuff from the base class is initialized!
      if base and base.init then
      base.init(obj, ...)
      end
   end
   return obj
   end
   c.init = init
   c.is_a = function(self, klass)
      local m = getmetatable(self)
      while m do 
         if m == klass then return true end
         m = m._base
      end
      return false
   end
   setmetatable(c, mt)
   return c
end
</pre>

State基类，包含三个stub函数，enter()和exit()分别在进入和退出state时被执行，onUpdate()函数将会在state被激活时的每帧被执行。

<pre class="brush: lua; title: ; notranslate" title="">require "class"

State = class()

function State:init( name )
	self.name = name
end

function State:enter()
end

function State:onUpdate()
end

function State:exit()
end
</pre>

StateMachine类，该类集成了<a href="http://getmoai.com" target="_blank">Moai</a>的MOAIThread类。MOAIThread类似于Lua中的coroutine，但是在Moai中被yield的MOAIThread，会在game loop的每帧中被自动resume，见StateMachine:updateState函数，利用此特点，来实现每帧执行State:onUpdate函数。

<pre class="brush: lua; title: ; notranslate" title="">require "State"

StateMachine = class()

function StateMachine:init()
	self.currentState = nil
	self.lastState = nil
end

function StateMachine:run()
	if ( self.mainThread == nil )
	then
		self.mainThread = MOAIThread.new()
		self.mainThread:run( self.updateState, self )
	end
end

function StateMachine:stop()
	if ( self.mainThread )
	then
		self.mainThread:stop()
	end
end

function StateMachine:setCurrentState( state )
	if ( state and state:is_a( State ) )
	then
		if ( state == self.currentState )
		then
			print( "WARNING @ StateMachine::setCurrentState - " ..
				   "var state [" .. state.name .. "] is the same as current state" )
			return
		end
		self.lastState = self.currentState
		self.currentState = state
		if ( self.lastState )
		then
			print( "exiting state [" .. self.lastState.name .. "]" )
			self.lastState:exit()
		end
		print( "entering state [" .. self.currentState.name .. "]" )
		self.currentState:enter()
	else
		print( "ERROR @ StateMachine::setCurrentState - " ..
			   "var [state] is not a class type of State" )
	end
end

function StateMachine:updateState()
	while ( true )
	do
		if ( self.currentState ~= nil )
		then
			self.currentState:onUpdate()
		end
		coroutine.yield()
	end
end
</pre>

如何利用State和StateMachine类的示例，首先定义两个state。
  
SampleState.lua

<pre class="brush: lua; title: ; notranslate" title="">require "State"

State1 = class( State ) 

function State1:init()
	State.init( self, "State1" )
end

function State1:enter()
	self.i = 0
end

function State1:exit()
	self.i = 0
end

function State1:onUpdate()
	print( self.name .. " is updated" )
	self.i = self.i + 1
	print( "self.i=" .. self.i )
	if ( self.i == 10 )
	then
		print( state2 )
		SM:setCurrentState( state2 )
		self.i = 0
	end
end

-----------------------

State2 = class( State ) 

function State2:init()
	State.init( self, "State2" )
end

function State2:onUpdate()
	print( "State2 is updated" )
end
</pre>

test.lua

<pre class="brush: lua; title: ; notranslate" title="">require "StateMachine"
require "SampleState"

SM = StateMachine()
SM:run()
state1 = State1()
state2 = State2()
SM:setCurrentState( state1 )
</pre>