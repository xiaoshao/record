---
layout: post
title:  "SSH Protocol"
date:   2015-08-15 15:54:42
categories: Linux, Net
---


#安装以及启动SSH服务
---
	yum install openssh-server #Install sshd
	yum install ssh

安装完成后会在服务器得/etc/init.d下出现一个新的sshd服务，然后我们就可以通过`service sshd start`或者`/etc/init.d/sshd`启动ssh服务。

SSH服务启动之后会自动生成相关得密钥文件(/etc/ssh)。


#通过ssh链接到远程服务器
---
	ssh username@192.168.0.xxx
连接得时候要输入密码。

还有一种不需要输入密码得连接方法。
生成密钥对
	```
	ssh-keygen
	ls -al ~/.ssh/
	test  test.pub
	```
将上面生成得公钥`test.pub`得内容拼接在服务器的`~/.ssh/authorized_keys`文件的末尾这样我们就可以不用密码登录了。

#sshd服务器详细配置文件在/etc/ssh/ssh_config文件中，可以修改sshd的相关配置，并重启sshd service使修改生效。

#还有两个基于ssh好用的两个工具
---
###scp快速的将文件copy到服务器或者从服务器copy到本地
	```
	scp username@xxx.xxx.xxx.xxx:directory ~/directory
	scp ~/directory username@xxx.xxx.xxx.xxx:directory 
	```
###sftp基于ssh连接到服务器，进行相关ftp的操作
	```
		sftp username@xxx.xxx.xxx.xxx
		lcd #本地cd命令
		cd  #服务器cdmingling
		put #向服务器上传文件
		get #从服务器下载文件
		...
	```	
###rsync同步远程文件
	```
		rsync -e ssh username@xxx.xxx.xxx:~/dir /tmp
		rsync -e ssh /tmp username@xxx.xxx.xxx:~/dir
	```






