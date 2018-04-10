---
title: nginx入门系列一
---

 
# 今天要分享的内容是nginx的基本知识
<!-- more --> 
 

# 先看一下conf文件解释
```angularjs
# vi nginx.conf

user	nobody nobody; #	运行 nginx 的所属组和所有者

worker_processes	2; #	开启两个 nginx 工作进程,一般几个 CPU 核心就写几 

error_log	logs/error.log	notice; #	错误日志路径

pid	logs/nginx.pid; # pid 路径 

events {
    worker_connections	1024; #	一个进程能同时处理 1024 个请求
    }
http {

include		mime.types; 

default_type	application/octet-stream;

log_format	main	‘$remote_addr – $remote_user [$time_local] “$request” ‘ ‘$status $body_bytes_sent “$http_referer” ‘
‘”$http_user_agent” “$http_x_forwarded_for”‘; 

access_log	logs/access.log	main; #	默认访问日志路径 sendfile		on;

keepalive_timeout	65; # keepalive 超时时间

#	开始配置一个域名,一个 server 配置段一般对应一个域名 

server {
    
listen	80; 

#	在本机所有 ip 上监听 80,也可以写为 192.168.1.202:80,这样的话,就只监听 192.168.1.202 上的 80 口 

server_name	www.heytool.com; #	域名

root		/www/html/www.heytool.com; #	站点根目录（程序目录）

index	index.html index.htm; #	索引文件

location / {	#	可以有多个 location
    root	/www/html/www.heytool.com; #	站点根目录（程序目录）
    }
    
error_page	500 502 503 504	/50x.html;

#	定义错误页面,如果是 500 错误,则把站点根目录下的 50x.html 返回给用户 

location = /50x.html {
    root	/www/html/www.heytool.com;
        }
}

#	开始配置站点
 
bbs.heytool.com server {
    listen	80;
    server_name	bbs.heytool.com;  
    root	/www/html/bbs.heytool.com;
    index	index.html index.htm; #	索引文件 
    location / {
    root	/www/html/bbs.heytool.com;
      }
    error_page	500 502 503 504	/50x.html; 
    location = /50x.html {
    root	/www/html/bbs.heytool.com;
        }
    }
}

```

# nginx 启动，测试，关闭，重启

    # /usr/local/nginx-1.0.6/sbin/nginx	//启动 nginx
    # /usr/local/nginx-1.0.6/sbin/nginx –t //测试 nginx 配置文件的准确性
    # /usr/local/nginx-1.0.6/sbin/nginx –s reload //重载 nginx
    # /usr/local/nginx-1.0.6/sbin/nginx –s stop //关闭 nginx
# nginx 配置虚拟主机

也就是我们通常说的配置域名.

    server_name：虚拟主机的域名，可以写多个域名，类似于别名，比如说你可以配置成
    server_name b.ttlsa.com c.ttlsa.com d.ttlsa.com，这样的话，访问任何一个域名，内容都是一样的 listen 80，监听 ip 和端口，这边仅仅只有端口，表示当前服务器所有 ip 的 80 端口，如果只想监听 127.0.0.1 的 80，写法如下：
    listen 127.0.0.1:80
    root /data/site/b.ttlsa.com：站点根目录，你网站文件存放的地方。注：站点目录和域名尽量一样，养成一个 好习惯
    access_log /data/logs/nginx/b.ttlsa.com-access.log main：访问日志 location /{}

# nginx location 配置
语法规则： location [=|~|~*|^~] /uri/ { … }

1. = 表示精确匹配,这个优先级也是最高的
2. ^~ 表示 uri 以某个常规字符串开头，理解为匹配 url 路径即可。
nginx 不对 url 做编码，因此请求为/static/20%/aa，可以被规则^~ /static/ /aa 匹配到（注意是空格）。
3. ~	表示区分大小写的正则匹配
4. ~* 表示不区分大小写的正则匹配(和上面的唯一区别就是大小写)
5. !~和!~*分别为区分大小写不匹配及不区分大小写不匹配的正则
6. / 通用匹配，任何请求都会匹配到，默认匹配. 

多个 location 配置的情况下匹配顺序为：

首先匹配 =，其次匹配^~, 其次是按文件中顺序的正则匹配，最后是交给 / 通用匹配。当有匹配成功时候，停止 匹配，按当前匹配规则处理请求。
```angularjs
例子，有如下匹配规则： 
location / 
    {
        echo "/"; //需要安装 echo 模块才行,这边大家可以改成各自的规则
    }
location = / 
    { 
    echo "=/";
    }
location = /nginx 
    { 
    echo "=/nginx";
    }
location ~ \.(gif|jpg|png|js|css)$ 
    { echo "small-gif/jpg/png";
    }
location ~* \.png$ 
    { echo "all-png";
    }
location ^~ /static/ 
    { echo "static";
    }

```
那么
1. http://a.ttlsa.com/，完全匹配=/
2. http://a.ttlsa.com/nginx,完全匹配了”=/nginx”
3. http://a.ttlsa.com/xxx/1111.PNG （注意,这是大写），最后匹配到了~* \.png$
4. http://a.ttlsa.com/static/1111.png,虽然 static 放在最后面,但是因为有^的缘故,他是最匹配的.

# nginx root&alias 文件路径配置
nginx 指定文件路径有两种方式 root 和 alias，root 与 alias 主要区别在于 nginx 如何解释 location 后面的 uri，这会使两者分别以不同的方式将请求映射到 服务器文件上。
```angularjs
例子一：
location ~ ^/weblogs/ {
root /data/weblogs/www.ttlsa.com; autoindex on;
auth_basic	"Restricted"; auth_basic_user_file	passwd/weblogs;
}
如果一个请求的 URI 是/weblogs/httplogs/www.ttlsa.com-access.log 时，web 服务器将会返回服务器上的
/data/weblogs/www.ttlsa.com/weblogs/httplogs/www.ttlsa.com-access.log 的文件。 [info]root 会根据完整的 URI 请求来映射，也就是/path/uri。[/info] 因此，前面的请求映射为 path/weblogs/httplogs/www.ttlsa.com-access.log。

例子二：


location ^~ /binapp/ {
limit_conn limit 4; limit_rate 200k; internal;
alias /data/statics/bin/apps/;
}
alias 会把 location 后面配置的路径丢弃掉，把当前匹配到的目录指向到指定的目录。如果一个请求的 URI 是
/binapp/a.ttlsa.com/favicon 时，web 服务器将会返回服务器上的
/data/statics/bin/apps/a.ttlsa.com/favicon.jgp 的文件。 [warning]1. 使用 alias 时，目录名后面一定要加”/”。
2. alias 可以指定任何名称。
3. alias 在使用正则匹配时，必须捕捉要匹配的内容并在指定的内容处使用。
4.	alias 只能位于 location 块中。[/warning]

```

总结一下，就是，root相当于是根，特别之处根是在什么地方，

而alias是别号，也就是映射的那个路径，还可以是其他的别的地方，因此本身要去掉

# nginx变量

ngx_http_core_module 模块提供了大量的变量
    
    参数名称	注释
    $arg_PARAMETER	HTTP 请求中某个参数的值，如/index.php?site=www.ttlsa.com，可以用$arg_site 取 得 www.ttlsa.com 这个值.
    $args HTTP	请求中的完整参数。例如，在请求/index.php?width=400&height=200 中，$args 表示 字符串 width=400&height=200.
    $binary_remote_addr    二进制格式的客户端地址。例如：\x0A\xE0B\x0E
    $body_bytes_sent       表示在向客户端发送的 http 响应中，包体部分的字节数
    $content_length         表示客户端请求头部中的 Content-Length 字段
    $content_type	表示客户端请求头部中的 Content-Type 字段
    $cookie_COOKIE          表示在客户端请求头部中的 cookie 字段
    $document_root          表示当前请求所使用的 root 配置项的值
    $uri	表示当前请求的 URI，不带任何参数
    $document_uri	与$uri 含义相同
    $request_uri	表示客户端发来的原始请求 URI，带完整的参数。$uri 和$document_uri 未必是用户的 原始请求，在内部重定向后可能是重定向后的 URI，而$request_uri 永远不会改变，始终是客户端的原始 URI.
    $host	表示客户端请求头部中的 Host 字段。如果 Host 字段不存在，则以实际处理的 server
    （虚拟主机）名称代替。如果 Host 字段中带有端口，如 IP:PORT，那么$host 是去掉端口的，它的值为 IP。$host 是全小写的。这些特性与 http_HEADER 中的 http_host 不同，http_host 只取出 Host 头部对应的值。
    $hostname	表示 Nginx 所在机器的名称，与 gethostbyname 调用返回的值相同
    $http_HEADER	表示当前 HTTP 请求中相应头部的值。HEADER 名称全小写。例如，示请求中 Host 头部 对应的值	用 $http_host 表
    $sent_http_HEADER	表示返回客户端的 HTTP 响应中相应头部的值。HEADER 名称全小写。例如，用 $sent_ http_content_type 表示响应中 Content-Type 头部对应的值
    $is_args	表示请求中的 URI 是否带参数，如果带参数，$is_args 值为 ?，如果不带参数，则是 空字符串
    $limit_rate	表示当前连接的限速是多少，0 表示无限速
    $nginx_version          表示当前 Nginx 的版本号
    $query_string	请求 URI 中的参数，与 $args 相同，然而 $query_string 是只读的不会改变
    $remote_addr	表示客户端的地址
    $remote_port	表示客户端连接使用的端口
    $remote_user	表示使用 Auth Basic Module 时定义的用户名
    $request_filename     表示用户请求中的 URI 经过 root 或 alias 转换后的文件路径
    $request_body	表示 HTTP 请求中的包体，该参数只在 proxy_pass 或 fastcgi_pass 中有意义
    $request_body_file      表示 HTTP 请求中的包体存储的临时文件名
    $request_completion	当请求已经全部完成时，其值为 “ok”。若没有完成，就要返回客户端，则其值为空字 符串；或者在断点续传等情况下使用 HTTP range 访问的并不是文件的最后一块，那么其值也是空字符串。
    $request_method        表示 HTTP 请求的方法名，如 GET、PUT、POST 等
    $scheme	表示 HTTP scheme，如在请求 https://nginx.com/中表示 https
    $server_addr	表示服务器地址
    $server_name	表示服务器名称
    $server_port	表示服务器端口
    $server_protocol        表示服务器向客户端发送响应的协议，如 HTTP/1.1 或 HTTP/1.0


# nginx 日志配置
日志对于统计排错来说非常有利的。 日志格式通过 log_format 命令来定义。ngx_http_log_module 是用来定义请求日志格式的。

1. access_log
语法: access_log path [format [buffer=size [flush=time]]]; 
默认值: access_log logs/access.log combined;
2. log_format 指令
语法: log_format name string …; 
默认值: log_format combined “…”
有一个默认的无需设置的 combined 日志格式，相当于 apache 的 combined 日志格式，如下所示：
 
    
    log_format	combined	'$remote_addr - $remote_user	[$time_local]	'
    ' "$request"	$status	$body_bytes_sent	' ' "$http_referer"	"$http_user_agent" ';


    log_format	proxy	'$http_x_forwarded_for - $remote_user	[$time_local]	' ' "$request"	$status $body_bytes_sent '
    ' "$http_referer"	"$http_user_agent" '; 

3. 日志格式变量


    $remote_addr, $http_x_forwarded_for 记录客户端 IP 地址
    $remote_user 记录客户端用户名称
    $request 记录请求的 URL 和 HTTP 协议
    $status 记录请求状态
    $body_bytes_sent 发送给客户端的字节数，不包括响应头的大小； 该变量与 Apache 模块 mod_log_config 里的 “%B”参数兼容。
    $bytes_sent 发送给客户端的总字节数。
    $connection 连接的序列号。
    $connection_requests 当前通过一个连接获得的请求数量。
    $msec 日志写入时间。单位为秒，精度是毫秒。
    $pipe 如果请求是通过 HTTP 流水线(pipelined)发送，pipe 值为“p”，否则为“.”。
    $http_referer 记录从哪个页面链接访问过来的
    $http_user_agent 记录客户端浏览器相关信息
    $request_length 请求的长度（包括请求行，请求头和请求正文）。
    $request_time 请求处理时间，单位为秒，精度毫秒； 从读入客户端的第一个字节开始，直到把最后一个字符发送 给客户端后进行日志写入为止。
    $time_iso8601 ISO8601 标准格式下的本地时间。
    $time_local 通用日志格式下的本地时间。

4. open_log_file_cache 指令
   
   语法: open_log_file_cache max=N [inactive=time] [min_uses=N] [valid=time]; open_log_file_cache off;
   默认值: open_log_file_cache off; 
   配置段: http, server, location
   对于每一条日志记录，都将是先打开文件，再写入日志，然后关闭。可以使用 open_log_file_cache 来设置日志 文件缓存(默认是 off)，格式如下：
   参数注释如下：
   max:设置缓存中的最大文件描述符数量，如果缓存被占满，采用 LRU 算法将描述符关闭。 inactive:设置存活时间，默认是 10s
   min_uses:设置在 inactive 时间段内，日志文件最少使用多少次后，该日志文件描述符记入缓存中，默认是 1 次 valid:设置检查频率，默认 60s
   off：禁用缓存 实例如下：
        
        open_log_file_cache max=1000 inactive=20s valid=1m min_uses=2;

5. log_not_found 指令
   
    语法: log_not_found on | off;
    默认值: log_not_found on; 
    配置段: http, server, location
    是否在 error_log 中记录不存在的错误。默认是。

6. log_subrequest 指令
   
   语法: log_subrequest on | off; 
   默认值: log_subrequest off;
   配置段: http, server, location
   是否在 access_log 中记录子请求的访问日志。默认不记录。

7. rewrite_log 指令
   
   由 ngx_http_rewrite_module 模块提供的。用来记录重写日志的。对于调试重写规则建议开启。	Nginx 重写规则 指南
   语法: rewrite_log on | off; 默认值: rewrite_log off;
   配置段: http, server, location, if
   启用时将在 error log 中记录 notice 级别的重写日志。

8. error_log 指令
   
   语法: error_log file | stderr | syslog:server=address[,parameter=value] [debug | info | notice | warn | error | crit | alert | emerg];
   默认值: error_log logs/error.log error; 
   配置段: main, http, server, 
   location 配置错误日志。
