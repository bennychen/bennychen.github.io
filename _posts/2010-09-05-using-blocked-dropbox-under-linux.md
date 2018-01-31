---
id: 1055
title: Using Blocked Dropbox under Linux
date: 2010-09-05T12:19:47+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=1055
permalink: /2010/09/using-blocked-dropbox-under-linux/
sfw_pwd:
  - msFiftW0poL9
categories:
  - Linux
tags:
  - blocked
  - daemon
  - dropbox
  - gstm
  - linux
  - proxychains
  - ssh
---
Recently I&#8217;ve been starting to use Ubuntu. I am just a newbie to Linux, so everything seems hard for me on the first time. No matter how familiar we are with Windows, when transferred to the Linux world, we become a computer idiot again. But everything is also fresh and attractive for me. When I get my first Ubuntu 10.04 (the latest version at this time) work on my laptop, I was totally shocked, really bloody cool~ (I learned the word &#8220;bloody&#8221; from a British TV show called IT Crowd, which I am watching recently)

Dropbox is a perfect software to sync your files among different computers and I use it quite often under Windows. And now under Linux, I want it, too. But here in China, Dropbox is violently blocked. So in order to use it, I need to get out of the firewall first.

**Get an SSH tunnel**
  
It&#8217;s a common way to use SSH to get over the wall. Getting an SSH tunnel is easy, there are some for free. And you could also buy one for the sake of stability and speed. I bought a Puff (which is a kind of software using SSH) account from China&#8217;s biggest C2C shopping site Taobao.com.

**Use gSTM as the SSH tunnel manager under Linux**
  
Get gSTM from here: <http://sourceforge.net/projects/gstm/>
  
Configure gSTM:

  * click &#8216;Add&#8217; button to add a tunnel;
  * input the host and login username of the tunnel you get from the last step;
  * click &#8216;Add&#8217; button to add a port redirection, set Type=dynamic, Port=7070 (the common SSH port), To host=n/a, To Port=n/a.

Start the tunnel you&#8217;ve just added. If you set your browser&#8217;s proxy as SOCK5, 127.0.0.1:7070, you should be able to surf the sites like Facebook and Twitter now. If so, certainly you can log on to www.dropbox.com too!

**Download Dropbox for Linux**
  
Go to <https://www.dropbox.com/downloading>, and download a Dropbox for linux. I&#8217;m using Ubuntu, so I downloaded the Ubuntu (x86 .deb). Double click the downloaded .deb file, it will start the Ubuntu&#8217;s package installer to install Dropbox.

**Download Dropbox daemon**
  
After installation and starting Dropbox, it will prompt you to download the Dropbox daemon, but the proxy we set up before only works for the browser, so I cannot get the downloading work. The problem here is how could I let an arbitrary software to access the Internet with proxy. The solution is to use ProxyChains.

**Use ProxyChains**
  
Install ProxyChains: sudo apt-get install ProxyChains
  
Configuration

  * open configuration file ( /etc/proxychains.conf )
  * modify the last line from &#8220;socks4 127.0.0.1 9050&#8221; to &#8220;socks5 127.0.0.1 7070&#8221;

Now you should be able to use the SSH proxy to download Dropbox daemon.
  
Open terminal, and enter: proxychains dropbox start -i

This will start Dropbox with the SSH proxy. After the daemon is downloaded and installed, we could enjoy Dropbox again.