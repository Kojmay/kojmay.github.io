---
layout: post
title: "shadowsocks安装"
date: 2017-10-17 
description: "shadowsocks，ss"
tag: 工具 
---

最近，之前用linode搭建的shadowsocks无法使用了，因此上网查询解决方法。由于百度已将ss当成敏感词屏蔽，于是我便通过国内的一些第三方网站间接的使用google搜索，但是这些第三方不靠谱网站在显示结果列表的页面将连接直接指定到一个很恶心的广告网站，我只能默默的在心里给征夫点个赞。

Anyway，最终还是找到了问题的所在。最近网上流传较广的原因是X系统通过随机森林可以识别ss请求的准确率达到80%，因此导致大部分ss服务器无法访问。但是通过ssh登陆到服务器上，发现ss服务已经挂了，可能是由于udp流量爆发导致ss进程崩溃？这个问题有待思考。重启服务报错后，遂重启了服务器，然后再启动ss服务，搞定！但是再次链接的时候，发现还是无法上google，略郁闷。之后再次想到昨天在微博上看到的，有人说是某些国内的互联网商家检测到ss后记录下了对应的端口和服务器IP，最后导致该端口或者IP被封。因为可以ssh登录服务器，所以IP应该没有被封，难道是端口被封了？赶紧打开json文件，修改了对应的端口，然后修改本地连接的端口，重新打开Google，Bingo！

	vi /etc/shadowsocks.json //打开配置文件

因此我想在此记录一下shadowsocks的安装方式，以防以后重装时忘记，好记性不如烂笔头！

##Shadowsocks安装和配置
###通过pip安装shadowsocks

	$ sudo apt-get update
	$ sudo apt-get install python-gevent python-pip python-m2crypto python-wheel python-setuptools
	$ sudo pip install shadowsocks
###安装SS
由于ss作者已被请喝茶，遂ss版本停留在了2.8.2，我们可以使用如下命令来安装ss，很方便：

	pip install https://github.com/shadowsocks/shadowsocks/archive/master.zip
通过如下命令来查看ss的版本：
	sudo ssserver --version
###创建配置文件
创建配置文件：

	udo nano /etc/shadowsocks.json
复制并粘贴如下文件：

	{
   		"server":"::",
    	"server_port":8388,//注意修改改端口
    	"local_address": "127.0.0.1",
    	"local_port":1080,
    	"password":"mypassword",//注意修改密码
    	"timeout":300,
    	"method":"aes-256-cfb",
    	"fast_open": false
	}

###ss启动和停止

	service shadowsocks start //启动ss服务
	service shadowsocks stop  //关闭ss服务
	service shadowsocks status  //查看ss状态