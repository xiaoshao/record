---
layout: post
title:  "How to mount local directory to boot2docker"
date:   2015-08-15 15:54:42
categories: docker
---

#Background
---
当我们在mac上使用docker的时候，我们首先需要装一个boot2docker，其实boot2docker是一个安装了docker的linux虚拟机。本质上我们还是在一个虚拟机上进行相关操作。
所以我们想将本地的文件挂到docker 
container中，我们首先要将我们的文件挂载到boot2docker虚拟机上，下面我们就按照这个思路去做。

#将本地文件设置为共享文件
---
System Preference >> Share
![set local directory shared](/assets/img/share.png)

#将本地文件挂载到boot2docker虚拟机上
---

	boot2docker ssh #登录到boot2docker虚拟机

	sudo sshfs username@xx.xx.xx.xx:shared directory /home/docker/osx_share


#将boot2docker中的文件挂载到container中
---

	docker run -t -i -v /home/docker/osx_share/:/opt/shared centos:7 /bin/bash

至此我们就把本地 share文件挂载到docker container中的/opt/shared

