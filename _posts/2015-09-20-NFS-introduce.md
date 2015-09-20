---
layout: post
title:  "NFS introduce"
date:   2015-08-15 15:54:42
categories: Linux, Net
---

#NFS: Network file system
---
NFS可以使在网络上的机器共享一个文件夹，在client可以使用mount命令将其挂载到某个文件上，然后就可以像使用本地文件一样对它进行操作。
	
	mount -t nfs /local directory remote ip@shared dir

#Install server
---
因为NFS是一个RPC服务,所以我们首先要安装RPC-bind的一个RPM包，并且在启动NFS之前首先要启动RPC（Remote procedure call），因为NFS服务的端口是随机在1024以内的几个端口上，所以它在启动时会想RPC注册它的服务在那一个端口上，而客户端发起请求之前，首先要向RPC（监听111端口）请求获取NFS的相关信息。
NFS使用步骤：
	1）客户端向服务器端的RPC（port 111）发出NFS文件访问功能的查询要求
	2）服务器端找到对应的已注册的NFS daemon端口后，发送给客户端
	3）客户端了解正确的端口后，就可以直接与NFS daemon连接

	yum install rpcbind.x86-64
	yum install nfs-utils.x86-64

服务器端需要启动几个服务，rpc.nfsd、rpc.mountd、rpc.lockd（非必要的）、rpc-statd（非必要的）。	

##nfs的配置文件 `/etc/exports`
	
	#exports 文件的配置如下
	/tmp  *(rw, no_root_squash） 

##nfs文件维护命令 `exportfs`

	exportfs [-aruv]
		-a 重新挂载或者卸载 /etc/exports文件中的设置
		-r 重新挂载/etc/exports中的文件设置，此外，同步更新/etc/exports以及 /var/lib/nfs/xtab中的内容
		-u 卸载某一目录
		-v 将贡献显示在屏幕上

##共享资源的日志文件 `/var/lib/nfs/*tab`
nfs主要的配置文件有两个
	1)`etab`记录nfs export的相关信息

	/tmp	*(rw,sync,wdelay,hide,nocrossmnt,secure,root_squash,no_all_squash,no_subtree_check,secure_locks,acl,anonuid=65534,anongid=65534,sec=sys,rw,root_squash,no_all_squash)
	

	2)`xtab`记录nfs客户端访问的相关信息。

##客户端查询服务器共享资源命令 `showmount`

	`showmount -a ip/host_name`

##启动NFS

	/etc/init.d/rpcbind start
	/etc/init.d/nfs start
	/etc/init.d/nfslock start

	chkconfig rpcbind on
	chkconfig nfs on
	chkconfig nfslock on

查看服务器是否启动成功`cat /var/log/message`

##查看nfs注册相关信息

	rpcinfo -t localhost nfs

	program 100003 version 2 ready and waiting
	program 100003 version 3 ready and waiting
	program 100003 version 4 ready and waiting
	

##nfs安全性 
	nfs监听在几个随机的端口上，所以防火墙设置比较难，可是我们可以修改`/etc/sysconfig/nfs`，将nfs固定在某几个端口上。


#NFS客户端设置

我们可以通过`showmount -e ip` 查看服务器export的相关信息，然后可以通过`mount -t nfs ip:/remote directory local directory`将其挂载到local directory.

我们可以向`/etc/rc.d/rc.local`中添加`mount -t nfs ip:/remote dir local dir`，在开机的时候就将remote dir挂载到local dir。

##自动挂载autofs

autofs支持在需要使用nfs的时候再挂载nfs文件，而在不需要的时候就unmount掉。

	/etc/auto.master

	/home/nfsfile	/etc/auto.nfs
	#本地文件夹       挂载配置文件

	/etc/auto.nfs
	public		-rw, bg 	remote ip:/remote dir
	#本地文件夹	挂载参数		远程Ip以及文件夹

设置完这些就可以启动autofs了`/etc/init.d/autofs start`


