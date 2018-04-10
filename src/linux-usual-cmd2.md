---
title: Linux每日一命令学习系列总结
photos: [https://lh3.googleusercontent.com/-jeVkuXRyY2U/WkU7va-I2dI/AAAAAAAACtU/Mq8zHnXBN3IQ4Tz-sYgjNBb3bDUUpdxBgCJoC/w530-h663-n-rw/50425_23164038_163249994272721_204712082791202816_n.jpg,https://lh3.googleusercontent.com/-ffUP6ygBZjs/WkVrrcVAWFI/AAAAAAAAIjc/kwTa3YCuDv0c4VDDGMkNd7Eyw9sVBbqYQCJoC/w530-h750-n-rw/4257458dea04a1d6d17df4b118bde90f.jpg,https://lh3.googleusercontent.com/-kKbWXIuf30E/WkTWohmF7uI/AAAAAAACl0A/2GEDtOaTmhwkYzpsc-A_-jNNtNOWlSp3wCJoC/w530-h568-n-rw/KiuMbY5S6U.png]
---
这里是罗列出最常用的命令组合，而不是一个个详细介绍每个参数的用法，适合当你面对linux的终端的时候，知道有哪些命令，可是不知道加啥组合参数有啥效果的时候使用。
=============
首页先要说的是，今天是2017年的年末的最后一个工作日，马上就要元旦过新年了，大家这一年过得怎么样？

不过如何，虽然又大了一岁，改变了很多事情，还是要说新年元旦快乐。

回到正文，居然没有人看，图片版本的上篇文章，那么只好弄一篇markdown版本的帮助你们复制粘贴了，原文可以去这里看，www.cnblogs.com/peida/tag/每日一linux命令/

说明，文中的，三个减号(---)代表linux的管道符合（|），因为markdown的表格冲突，暂时这样写
<!-- more --> 

## 正式开始

| 命令       |使用场景 |  组合参数用法  |
| ---------- |         |                |  
| ls         |列出某一个目录下的子目录以及文件              |               `ls -lRF  / `                               |
|            |因为total的原因不够直观，那么可以代替上面这个 |               `find $PWD ---xargs ls -ldF`                |
|            |如果还是不够直观，这次加颜色参数              |       `find $PWD --- xargs ls -ldF --color`               |
|            |当前文件目录最新修改的文件排序最前            |                `ls -ltr`                                  |
|            |直观的看出目录于名称后加"/", 可执行档于名称后加"*"  |          `ls -F`                                    |
| ---------- |         |                |  
| cd         | 进入当前登录用户的目录                       |  `cd     `                                                  | 
|            |  进入刚才的目录                              | `` cd -        ``                                             |
|            |  后退上一级目录                              | `` cd ..      ``                                              |
| ---------- |         |                |  
|pwd      |当前工作目录的完整路径（软连接，-P物理地址）| `pwd`|
| ---------- |         |                |  
|mkdir   |创建目录 （递归创建目录-p）|`mkdir -p hank/a/b/c`|
| ---------- |         |                |  
|rm         |强制删除当前目录以及里面的文件|           `rm -rf  *`|
| ---------- |         |                |  
|rmdir       |删除空目录（删除前目录必须为空）递归删除包括自身  | `rmdir -p`（不实用）|
| ---------- |         |                |  
|mv        |文件改名|                                                  `mv test.log test.txt`|
|           |   文件移动|                                                  ` mv test.log(文件)   test(文件夹)`|
|            |  目录移动|                                                   `mv testa(文件夹)   testb(文件夹)`|
|            |  移动当前文件夹下的所有文件到上一级目录|           ` mv * ../`|
| ---------- |         |                |  
|cp         |复制单个文件到目标目录|                           `cp test.log(文件)    test(文件夹)`|
|           |复制整个目录 (testb不存在那么就没有testa)|  `cp -a testa(文件夹)   testb(文件夹)`|  
| ---------- |         |                |  
|touch   |创建文件|                                                       `touch a b c`|
|        |修改文件时间戳|                                           `touch a`|
| ---------- |         |                |  
|cat        |查看文件|                                                       `cat  a  b  c`|
|           | 生成文件 |                                                     ` cat >test.log<<EOF`|
|          |合并文件 |                                                    `cat  a  b  c  > d`|
| ---------- |         |                |  
|nl          |显示行号不管是否为空行   |                  `nl -b a test.log`|
|             |让行号左边开头补全零   |                    `nl -b a -n rz test.log`|
| ---------- |         |                |  
|more    |向后翻页|空格|             
|             | 向前翻页|b键|
|             | 从第n页开始     |                             `more        +n             文件`|
|             | 每页设定n条记录 |                          `more         -n             文件`|
|             | 查找一个指定字符串|                     `more        +/字符串     文件`|
| ---------- |         |                |  
|less     |升级版的more，低级版的vi，不需要像more一样全部加载文件||
|             |查看文件 |           `less           文件（其他按键操作看vi）`|              
| ---------- |         |                |  
|head    |显示文件的前5行              |          `head          -n        5       log.test`|
 |           |  显示文件的除了最后5行的全部内容   |    `head    -n   -5      log.test`      |
| ---------- |         |                |  
|tail       |显示行末的最后5行|                     `tail             -n        5        log.test`|
|            |刷新查看最新的文件内容   |    `tail              -f                  log.test(超实用)`|
|            | 显示文件第5行开始的内容 |      `tail              -n       +5      log.test`|
| ---------- |         |                |  
|which   |查询Path变量路径下的，某个系统可执行（不是任意的文件）的文件的位置| |
|         ||`which    ls`|
|           |   |`which    which`|
 |          |   |`which    cd`|
| ---------- |         |                |  
|whereis   |搜索程序名包括，二进制文件，man说明文件，源代码文件三类（不要搞错）||
|        || `whereis  svn`|
|        ||      `whereis  tomcat`|
|         ||     `whereis  resin`|
| ---------- |         |                |  
|locate    |快速查询档案系统是否有指定的档案（这里我也不太懂什么叫档案）||
|          |     查询pwd相关的文件      |                            `locate      pwd`  |  
 |           |   查询etc目录下所有m开头的文件    |        `locate      /etc/m`        |
| ---------- |         |                |  
|find        |查询24小时内修改过的文件  |                  `find           -atime            -1`|
|            |    根据关键字查询文件(文件夹) |                 `find           -name            "*han*"`|
|             |   只查询文件不包括文件夹     |                   `find  -type f  -name       "*han*"`|
|              |  查询当前目录下的所有子目录 |                `find    .   type     d`|
 |              | 查询当前目录下大于1k的文件 |                `find    .   -type   f  -size +1000c`|
 |              | 列出更加详细的文件信息组合命令 |       `find    .   -type   f  -exec ls -l {} \;`|
 |              | 把查找到的文件移动到指定目录| `find . -type f  -name  "*han*" -exec mv {} hello`|
 |              | 把查找到的文件复制到指定目录| `find . -type f  -name  "*han*" -exec cp {} hello`|
 |              | 查找当前目录文件并显示属于哪类文件|          `find . -type f --- xargs  file`|
 |               |查找当面目录文件中包括关键字 |          `find .  -type  f --- xargs   grep  "hank"`|
 |               |在a目录查询避开b目录查询     |          `find   a  -path   "b" -prune -o -print`|
 |               |优先在当前目录查找然后去子目录|      `find / -name "file" -depth -print`|
| ---------- |         |                |  
|chmod    |给文件一组默认执行权限|              `chmod     751           test.log`|
 |                |给文件一组默认读权限|                  `chmod      444          test.log`|
 |                |给文件一组读写执行权限|               `chmod     777           test.log`|
| ---------- |         |                |  
|tar          |  搞清楚打包和压缩两个概念，tar是打包用的，但是可以参数进行压缩||
|                |  解开一个tar包还原里面的文件|           `tar xvf     file.tar`|
|                 | 把文件和目录达成一个tar包|                `tar cvf   file.tar   对应目录`|
|                  |解开tar包的压缩包版本 （gzip）|        `tar zxvf    file.tar.gz`|
|                  |压缩打包后的tar包 （bzip）  |        `tar zcvf     file.tar.gz     对应目录`|
|                  |产一个gz的压缩包|                              `tar -ztvf   file.tar.gz`|
| ---------- |         |                |  
|chgrp       |群组去/etc/group里面看||                        
|             |     根据某个文件改变其他所属的类别|         `chgrp      --reference=a      b`|
| ---------- |         |                |  
|chown     |更改指定目录及其子目录所有的拥有者和群组|       `chown -R hank:han   test.log`|
| ---------- |         |                |  
|gzip         |压缩当前目录下的所有文件（文件夹忽略）|           `gzip *`|
 |            |   解压当前目录下的压缩文件|                                      `gzip  -d   *`|
 |            |     压缩一个tar包  |                                                   `gzip -r test.tar`|
 |            |     递归压缩目录   |                                                            `gzip  -r       test/`|
| ---------- |         |                |  
|df             |显示磁盘使用情况|                                 `df`|
|                | 查看文件系统类型 |                                 `df   -T`|
|                |更方面人理解的列表（H表示1000而不是1024）|       `df -h`|
| ---------- |         |                |  
|du           |查看当前目录下各个目录占用空间|                     `du`|
|            |查看当前目录下文件+目录各自占用空间|     `du -a`|
|          |用人能看懂的方式展示|                                  `du -h`|
|          |只看一级的子目录各自占用的空间大小|          `du -h --max-depth=1`|
| ---------- |         |                |  
|ln         |软硬链接的区别，软的是一种快捷方式，硬的直接指向文件所在位置||
|            ||       `ln -s     test.log       test.soft.link`
|            ||       `ln          test.log       test.hard.link`
| ---------- |         |                |  
|diff        | 比较2个文件(文件夹)，推荐方式|            `diff a  b  -y`|
 |             |      生成补丁    |                                          `diff a  b > a.patch`| 
 |              |     打补丁     |                                             `patch a a.patch`|
| ---------- |         |                |  
|date        |  日期    |                                                  `date "+%D"`|
|           |        时间   |                                                  `date "+%T"`|
| ---------- |         |                |  
|cal          | 本月的日历 |                                        `cal`|
|            |全年的阳历  |                                    `cal -y 2017`|                      
| ---------- |         |                |  
|grep        |查找指定进错|                              `ps -ef --- grep svn`|
|          |   从a文件中读取规则去b文件中找|      `cat b --- grep -f a`|
|          |            从文件中找关键字|                         `grep 'han' test.log`|
|          |            查找ssh同时排除grep|               `ps aux--- grep ssh --- grep -v 'grep'`|
|          |            查找包含a或者b或者c的内容行|    `grep -E  'a---b---c'      test.log`|
| ---------- |         |                |  
|wc          |统计行数，单词数，字节数，文件名|                `wc         test.log`|
|            |只统计行数 |                                         `wc    -l  test.log` | 
| ---------- |         |                |  
 |ps                  | 显示所有进程信息（常见） |            `ps -ef` | 
   |                   |    查找特定进程（常用） |               `ps -ef ---grep svn` | 
    |                  |  列出当前用户登录的pid相关 |         `ps -l` | 
  |                   | 显示所有内存当中的程序（实用） |       `ps aux` | 
| ---------- |         |                |  
|kill |               彻底杀死进程（超常用） |               `kill -9 进程号`|
| ---------- |         |                |  
|killall |          杀死进程通过名字（可能误杀） |    `killall -9  进程名称`  |                                         
| ---------- |         |                |  
|top|                显示完整命令(默认cpu排序，实用)|                                   `top  -c`|
| ---------- |         |                |  
|free|               查看内存 |                                              `free -m`|
| ---------- |         |                |  
|vmstat   |查看内存使用的详细信息    |         `vmstat    -s`|
| ---------- |         |                |  
|iostat|             查看所有设备负载情况|                      `iostat`| 
| ---------- |         |                |  
|watch       |      每隔一秒高亮显示网络中链接数的变化 |        `watch -n 1 -d netstat -ant`|
| ---------- |         |                |  
|at          |            一次性的定时任务   |                   `at        时刻`|
|              |            打开定时任务    |                        `/etc/init.d/atd start`|
|                  |        查看定时任务  |                           `atq`|
|                  |      删除定时任务   |                                `atrm         任务号`|
| ---------- |         |                |  
 |crontab    |打开crond服务             |               `service crond start` |
   |                  |       每日执行一命令   |                       `crontab -e`  |        
  |                  | 最后一行添加如hello |            `* * * * * /bin/echo 'hello' > /root/han/cron`|
| ---------- |         |                |  
|lsof    |               通过进程号显示该进程打开的文件  |             `lsof -p 进程号`|
|                         | 列出哪些进程在使用这个文件   |                    `lsof     /bin/bash`|
 |                         |列出tcp网络连接信息|                                     `lsof -i tcp`|
 |                         |查询谁在使用某个端口 |                                 `lsof -i :8080`|
 |                         |主要解决了，这个进程打开了哪些文件，这个文件是由那个进程打开的||
| ---------- |         |                |  
|ifconfig    |        显示网络设备信息  |                                      `ifconfig`|
| ---------- |         |                |  
|route     |         查看路由信息      |                                           `route -n`|
| ---------- |         |                |  
|ping                |向指定的ip发送指定次数|                    `ping  -c  10  127.0.0.1`|
| ---------- |         |                |  
|traceroute  |      向指定的域名发送请求探测出路径|            `traceroute  www.sogo.com`|
| ---------- |         |                |  
|netstat     |         显示监听的套接口|                                        `netstat -l`|
 |                 |          显示所有udp的连接|                                       `netstat -u`  |
 |                   |        显示所有tcp的连接    |                                 `netstat -t`|
 | |统计机器中网络连接各个状态个数| `netstat -a -- awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'`|
  |                 |         找出程序运行的端口      |     `netstat -ap --- grep ssh`|
  |                |           显示pid和进程名字     |                               `netstat -p`|
  |               |           显示ip地址而不是域名   |                            `netstat -n`|
  |                |           通过端口找到进程      |      `netstat -ap--- grep '80'`|
| ---------- |         |                |  
|ss                   | 显示tcp链接  |                                          `ss  -t  -a`|
|                       | 列出所有打开的网络连接端口|       `ss      -l`|
| ---------- |         |                |  
|scp                   |本地文件（文件夹）到远程 |            `scp -r local    remoteIP:remote`|
|                     |远程到本地            |                                 `scp -r remoteIP:remote     local`|