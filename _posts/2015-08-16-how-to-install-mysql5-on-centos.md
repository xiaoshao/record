---
layout: post
title:  "How to install mysql 5.5 on centos"
date:   2015-08-15 15:54:42
categories: linux
---

#配置remi源
---

	rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/i386/epel-release-5-4.noarch.rpm #因为remi源依赖epel，所以我们首先要配置epel源

	rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-5.rpm

	rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm

	rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm


#enable remi 源
---
	yum --enablerepo=remi,remi-test list mysql mysql-devel mysql-server


#install mysql server & client
	
	yum --enablerepo=remi,remi-test install mysql mysql-server

	/etc/init.d/mysqld start