---
title: Linux命令-scp
tags:
  - scp
  - Linux
category: Linux
abbrlink: 41503
date: 2020-07-15 19:21:18
modified: 2020-07-15 19:21:18
---

## SCP命令
> 远程拷贝文件的命令

> 类似的命令有cp，不过cp只是在本机进行拷贝不能跨服务器

参数 | desc
---|---
-1 | 使用ssh协议版本1；
-2 | 使用ssh协议版本2；
-4 | 使用ipv4；
-6 | 使用ipv6；
-B | 以批处理模式运行；
-C | 使用压缩；
-F | 指定ssh配置文件；
-l | 指定宽带限制；
-o | 指定使用的ssh选项；
-P | 指定远程主机的端口号；
-p | 保留文件的最后修改时间，最后访问时间和权限模式；
-q | 不显示复制进度；
-r | 以递归方式复制。


1、从服务器上下载文件
```
scp username@servername:/path/filename  /var/www/local_dir（本地目录）
```
例如scp root@192.168.0.101:/var/www/test.txt 把192.168.0.101上的/var/www/test.txt 的文件下载到/var/www/local_dir（本地目录）
————————————————

2、上传本地文件到服务器
```
scp /path/filename username@servername:/path
```
例如scp /var/www/test.php  root@192.168.0.101:/var/www/ 把本机/var/www/目录下的test.php文件上传到192.168.0.101这台服务器上的/var/www/目录中
————————————————

3、上传下载目录 需要加 -r 参数
```
scp -r root@192.168.0.101:/var/www/test  /var/www/  
```
从服务器下载test目录 到 本地/var/www



## 参考
【1】[scp远程拷贝文件的命令](https://man.linuxde.net/scp)