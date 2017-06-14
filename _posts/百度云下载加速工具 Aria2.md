---
title: 百度云下载加速工具 Aria2
date: 2017-06-14
tags: [tools]
---

1. 在 https://github.com/aria2/aria2/releases 下载安装 aria2.
2. 在 ~/.aria2/aria.conf 中添加配置：

	```shell
	# Basic Options
	dir=/Users/XXX/Downloads  # replace XXX to username
	input-file=/Applications/aria2c/session.dat
	log=/Applications/aria2c/aria2.log
	max-concurrent-downloads=15
	max-connection-per-server=15
	check-integrity=true
	continue=true
	
	# BitTorrent/Metalink Options
	bt-enable-lpd=true
	bt-max-open-files=16
	bt-max-peers=8
	dht-file-path=/opt/var/aria2/dht.dat
	dht-file-path6=/opt/var/aria2/dht6.dat
	dht-listen-port=6801
	#enable-dht6=true
	listen-port=6801
	max-overall-upload-limit=0K
	seed-ratio=0
	
	# RPC Options
	enable-rpc=true
	rpc-allow-origin-all=true
	rpc-listen-all=true
	rpc-listen-port=6800
	#rpc-secret=123456
	#rpc-secure=true
	
	# Advanced Options
	daemon=true
	disable-ipv6=true
	#enable-mmap=true
	force-save=true
	file-allocation=none
	log-level=warn
	max-overall-download-limit=0K
	save-session=/Applications/aria2c/session.dat
	always-resume=true
	split=10
	min-split-size=10M
	
	#百度盘
	user-agent=netdisk;5.2.6;PC;PC-Windows;6.2.9200;WindowsBaiduYunGuanJia
	referer=http://pan.baidu.com/disk/home
	```
1. 执行以下命令：

	```
	mkdir /Applications/aria2c
	touch /Applications/aria2c/session.dat
	touch /Applications/aria2c/aria2.log
	```
	
1. 执行 /usr/local/aria2c/bin/aria2c（完整的启动命令是：`aria2c --conf-path="~/.aria2/aria2.conf" -D`）, 用`ps aux|grep aria2c`确认 aria2c 启动成功。
2. 安装 BaiduExpoter.crx Chrome 插件：https://github.com/acgotaku/BaiduExporter/releases
	
	安装插件之后，百度云网页上会出现“导出下载”按钮，如下图所示。点击 ARIA2 RPC，aria2 就可以在后台开始下载了。
	![](https://i.imgsafe.org/132a8da2aa.png)
3. 在 http://binux.github.io/yaaw/demo/ 中添加如图所示的设置，可以监控下载进度。

	![](https://i.imgsafe.org/132a8da2aa.png)