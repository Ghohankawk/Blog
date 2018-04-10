---
title: redis学习系列之——安装篇
---

 
# 今天要分享的内容是redis的使用安装篇
这里主要分享，Windows下的安装使用，其他的系统，自己找
<!-- more --> 

# Window 下安装

![](http://www.runoob.com/wp-content/uploads/2014/11/3B8D633F-14CE-42E3-B174-FCCD48B11FF3.jpg)
下载地址：https://github.com/MSOpenTech/redis/releases。
Redis 支持 32 位和 64 位。这个需要根据你系统平台的实际情况选择，这里我们下载 Redis-x64-xxx.zip压缩包到 C 盘，解压后，将文件夹重新命名为 redis。

# 安装步骤二，启动服务器
打开一个 cmd 窗口 使用cd命令切换目录到 C:\redis 运行 redis-server.exe redis.windows.conf 。
如果想方便的话，可以把 redis 的路径加到系统的环境变量里，这样就省得再输路径了，后面的那个 redis.windows.conf 可以省略，如果省略，会启用默认的。输入之后，会显示如下界面：
![](http://www.runoob.com/wp-content/uploads/2014/11/redis-install1.png)

# 安装步骤三，启动客户端
这时候另启一个cmd窗口，原来的不要关闭，不然就无法访问服务端了。
切换到redis目录下运行 redis-cli.exe -h 127.0.0.1 -p 6379 。
设置键值对 set myKey abc
取出键值对 get myKey
![](http://www.runoob.com/wp-content/uploads/2014/11/redis-install2.jpg)

顺便附带一个[菜鸟redis教程传送门](http://www.runoob.com/redis/redis-install.html)
# 最后强调一句，默认的数据是存储在db0里面
