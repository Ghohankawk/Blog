---
title: nginx入门系列二
---

 
# 今天要分享的内容是还是nginx相关
 <!-- more --> 


# Nginx 重写规则指南
既然你看到这里，我想，不用问，你也肯定知道，nginx是运维要玩的东西，开发不直接管，那么

记住一句话：
不懂程序的运维，不是好运维；不懂运维的开发，不是 好开发。

对于重写规则 说到底就是正则匹配，同时，如果想把 apache 转换成 nginx，重写规则也是要改的咯。

1. rewrite 模块介绍
   
   nginx 的重写模块是一个简单的正则表达式匹配与一个虚拟堆叠机结合。依赖于 PCRE 库，因此需要安装 pcre。根 据相关变量重定向和选择不同的配置，从一个 location 跳转到另一个 location，不过这样的循环最多可以执行 10 次，超过后 nginx 将返回 500 错误。同时，重写模块包含 set 指令，来创建新的变量并设其值，这在有些情景 下非常有用的，如记录条件标识、传递参数到其他 location、记录做了什么等等。
 
2. rewrite 模块指令
   
       break 
       语法：break 默认值：none
       使用字段：server, location, if 完成当前设置的重写规则，停止执行其他的重写规则。
       
       if
       语法：if (condition) { … } 默认值：none 
       使用字段：server, location
       注意：尽量考虑使用 trp_files 代替。 判断的条件可以有以下值：
       1. 一个变量的名称：空字符传”“或者一些“0”开始的字符串为 false。
       2. 字符串比较：使用=或!=运算符
       3. 正则表达式匹配：使用~(区分大小写)和~*(不区分大小写)，取反运算!~和!~*。
       4. 文件是否存在：使用-f 和!-f 操作符
       5. 目录是否存在：使用-d 和!-d 操作符
       7. 文件、目录、符号链接是否存在：使用-e 和!-e 操作符
       8. 文件是否可执行：使用-x 和!-x 操作符
   
       return 
       语法：return code 默认值：none
       使用字段：server, location, if
       停止处理并为客户端返回状态码。非标准的 444 状态码将关闭连接，不发送任何响应头。】
       可以使用的状态码有：
       204，400，402-406，408，410, 411, 413, 416 与 500-504。如果状态码附带文字段落，该文本将被放置在响应 主体。相反，如果状态码后面是一个 URL，该 URL 将成为 location 头补值。没有状态码的 URL 将被视为一个 302 状态码。
       
       rewrite
       语法：rewrite regex replacement flag 默认值：none
       使用字段：server, location, if
       按照相关的正则表达式与字符串修改 URI，指令按照在配置文件中出现的顺序执行。可以在重写指令后面添加标记。
       注意：如果替换的字符串以 http://开头，请求将被重定向，并且不再执行多余的 rewrite 指令。 
      
       尾部的标记(flag)可以是以下的值：
       last – 停止处理重写模块指令，之后搜索 location 与更改后的 URI 匹配。 
       break – 完成重写指令。
       redirect – 返回 302 临时重定向，如果替换字段用 http://开头则被使用。 
       permanent – 返回 301 永久重定向。
       
       rewrite_log 语法：rewrite_log on | off 默认值：rewrite_log off
       使用字段：server, location, if 变量：无
       启用时将在 error log 中记录 notice 级别的重写日志。
       
       set
       语法：set variable value 默认值：none  
       使用字段：server, location, if 为给定的变量设置一个特定值。
       uninitialized_variable_warn 
       语法：uninitialized_variable_warn on|off 默认值：uninitialized_variable_warn on 
       使用字段：http, server, location, if 控制是否记录未初始化变量的警告信息。


3. 重写规则组成部分
 
- 任何重写规则的第一部分都是一个正则表达式 可以使用括号来捕获，后续可以根据位置来将其引用，位置变量值取决于捕获正则表达式中的顺序，$1 引用第一 个括号中的值，$2 引用第二个括号中的值，以此类推。如：
        
        ^/images/([a-z]{2})/([a-z0-9]{5})/(.*)\.(png|jpg|gif)$
$1 是两个小写字母组成的字符串，$2 是由小写字母和 0 到 9 的数字组成的 5 个字符的字符串，$3 将是个文件名，
$4 是 png、jpg、gif 中的其中一个。

-  重写规则的第二部分是 URI
请求被改写。该 URI 可能包含正则表达式中的捕获的位置参数或这个级别下的 nginx 任何配置变量。如：
/data?file=$3.$4
如果这个 URI 不匹配 nginx 配置的任何 location，那么将给客户端返回 301(永久重定向)或 302(临时重定向)的状 态码来表示重定向类型。该状态码可以通过第三个参数来明确指定。
 

- 重写规则的第三部分
第三部分也就是尾部的标记(flag)。 last 标记将导致重写后的 URI 搜索匹配 nginx 的其他 location，最多可循 环 10 次。如：

        rewrite	'^/images/([a-z]{2})/([a-z0-9]{5})/(.*)\.(png|jpg|gif)$'	/data?file=$3.$4 last; break 指令可以当做自身指令。如：
        if ($bwhog) {
        limit_rate 300k; break;
        }
        
另一个停止重写模块处理指令是 return， 来控制主 HTTP 模块处理请求。 这意味着，nginx 直接返回信息给客户 端，与 error_page 结合为客户端呈现格式化的 HTML 页面或激活不同的模块来完成请求。如果状态码附带文字段 落，该文本将被放置在响应主体。相反，如果状态码后面是一个 URL，该 URL 将成为 location 头补值。没有状态 码的 URL 将被视为一个 302 状态码。如：

        location = /image404.html {
        return	404	"image not found\n";
        }

4. 例子
```angularjs
http {
#  定义 image 日志格式
log_format imagelog '[$time_local] ' $image_file ' ' $image_type ' ' $body_bytes_sent ' ' $status;
#  开启重写日志
rewrite_log on;

server {
root /home/www;

location / {
#  重写规则信息
error_log logs/rewrite.log notice;
#  注意这里要用‘’单引号引起来，避免{}
rewrite '^/images/([a-z]{2})/([a-z0-9]{5})/(.*)\.(png|jpg|gif)$' /data?file=$3.$4;
#  注意不能在上面这条规则后面加上“last”参数，否则下面的 set 指令不会执行
set $image_file $3; set $image_type $4;
}
location /data {
#  指定针对图片的日志格式，来分析图片类型和大小
access_log logs/images.log mian; root /data/images;
#   应用前面定义的变量。判断首先文件在不在，不在再判断目录在不在，如果还不在就跳转到最
 
后一个 url 里

}
 

try_files /$arg_file /image404.html;
 
location = /image404.html {
#  图片不存在返回特定的信息
return 404 "image not found\n";
}
}


```

5. nginx 逻辑运算
   nginx 的配置中不支持 if 条件的逻辑与&& 逻辑或|| 运算 ，而且不支持 if 的嵌套语法，否则会报下面的错误： nginx: [emerg] invalid condition。
   我们可以用变量的方式来间接实现。 要实现的语句：
       
       if	($arg_unitid = 42012 && $uri ~/thumb/){ echo		"www.ttlsa.com";
       }
    
   
   如果按照这样来配置，就会报 nginx: [emerg] invalid condition 错误。 可以这么来实现，如下所示：
   
       set	$flag 0;
       if ($uri ~ ^/thumb/[0-9]+_160.jpg$){ set $flag "${flag}1";
       }
       if ($arg_unitid = 42012){ set $flag "${flag}1";
       }
       if ($flag = "011"){ echo "www.ttlsa.com";
       }

6. 隐藏 Nginx 版本号的安全性与方法

        # vim nginx.conf
        在 http {—}里加上 server_tokens off;	如：
        
7. nginx 正向代理
   
   我们平时用的最多的最常见的是反向代理。那么 nginx 的正向代理是如何配置的呢？
   
       server {
       listen 8090; location / {
       resolver 218.85.157.99 218.85.152.99;
       resolver_timeout 30s;
       proxy_pass http://$host$request_uri;
       }
       access_log	/data/httplogs/proxy-$host-aceess.log;
       }
   就这么简单哈。  
   
   resolver 指令
   语法: resolver address … [valid=time]; 默认值: none
   配置段: http, server, location
   配置 DNS 服务器 IP 地址。可以指定多个，以轮询方式请求。
   nginx 会缓存解析的结果。默认情况下，缓存时间是名字解析响应中的 TTL 字段的值，可以通过 valid 参数更改。
   
   resolver_timeout 指令
   语法: resolver_timeout time; 默认值: resolver_timeout 30s; 
   配置段: http, server, location 解析超时时间。

8. nginx的最大好处，例子解释

由于公司内网有多台服务器的 http 服务要映射到公司外网静态 IP，如果用路由的端口映射来做，就只能一台内网 服务器的 80 端口映射到外网 80 端口，其他服务器的 80 端口只能映射到外网的非 80 端口。非 80 端口的映射在访
问的时候要域名加上端口，比较麻烦。并且公司入口路由最多只能做 20 个端口映射。肯定以后不够用。
然后 k 兄就提议可以在内网搭建个 nginx 反向代理服务器，将 nginx 反向代理服务器的 80 映射到外网 IP 的 80， 这样指向到公司外网 IP 的域名的 HTTP 请求就会发送到 nginx 反向代理服务器，利用 nginx 反向代理将不同域名 的请求转发给内网不同机器的端口，就起到了“根据域名自动转发到相应服务器的特定端口”的效果，而路由器 的端口映射做到的只是“根据不同端口自动转发到相应服务器的特定端口”，真是喜大普奔啊。


# 最后

关于一些，高级的概念，还有第三方模块，这些内容就不属于入门级别的了

这两篇的意义，在于，你能初步，能看懂nginx的配置文件，每个配置的含义，以及初步的开发跳转，基本足够了

等后续，碰到这些高级的概念或者应用的时候，在去看

不是为了单纯的学习而学习。