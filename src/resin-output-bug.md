---
title: 关于resin容器HttpServletResponse接口getOutputStream对象的write方法bug解决办法
photos: [https://lh3.googleusercontent.com/-jeVkuXRyY2U/WkU7va-I2dI/AAAAAAAACtU/Mq8zHnXBN3IQ4Tz-sYgjNBb3bDUUpdxBgCJoC/w530-h663-n-rw/50425_23164038_163249994272721_204712082791202816_n.jpg,https://lh3.googleusercontent.com/-ffUP6ygBZjs/WkVrrcVAWFI/AAAAAAAAIjc/kwTa3YCuDv0c4VDDGMkNd7Eyw9sVBbqYQCJoC/w530-h750-n-rw/4257458dea04a1d6d17df4b118bde90f.jpg,https://lh3.googleusercontent.com/-kKbWXIuf30E/WkTWohmF7uI/AAAAAAACl0A/2GEDtOaTmhwkYzpsc-A_-jNNtNOWlSp3wCJoC/w530-h568-n-rw/KiuMbY5S6U.png]
---
这个地方是我在开发中实际遇到的真实问题，非常的坑，记录一下和大家分享一下，有遇到问题的可以看一下。
=============
这个地方有几个前提，你看一下是否是这样的场景。

1.必须是resin容器（tomcat不存在问题）

2.输出二进制流文件，比如image图片，或者woff2这类字体（字符串类的不存在问题）
<!-- more --> 

## 直接看bug的三张图表现比较直观

图1，准备好的测试代码
![准备好的测试代码](https://i.loli.net/2017/12/29/5a461204c828b.png)
图2，本地tomcat容器测试环境，输出的内容，符合预期，abc
![本地tomcat测试环境](https://i.loli.net/2017/12/29/5a4612052f4d2.png)
图3，linux服务器resin容器测试环境，输出的内容，在最后默认多写一个20，ascii表示空格的意思
![linux服务器resin容器测试环境](https://i.loli.net/2017/12/29/5a46120799f76.jpg)

以上就是resin服务器上，对response对象输出流，write时出的bug问题了。

    这个问题，在你content-type是正常的css和js，或者其他的字符类型的没问题，
    而当你要输出，二进制的流的时候，即使多一个空格，也会导致有些文件解析不了。
    
图片比较特殊，如png或者svg这类的，即使多了二进制的空格，还是正常展示。

而woff2这类字体的话，多了空格，浏览器就会报错，字体无法解析，而你在浏览器里面也能看到对应的web字体，就是解析不了。

这个问题困扰了我一个星期，主要有以下几个方面：

1. 第一是resin这个服务器，不象tomcat那种国内普及，相应的中文资料不全，难以查询。
2. 就是这类问题不该如何描述，更别说，如何搜索了，因为你都说不清楚，怎么搜？
3. 以上两点就造就了这个问题，不好姐解决，总不能说，resin容器在最后输出的时候，多了一个二进制20吧？

鉴于以上的这几点，我简单看了一下resin的源码想找出答案，可是那个也是坑，完全大海捞针。

为什么这么说只有在resin上有问题？

因为这里我部署了resin和tomcat进行了测试，验证过，tomcat没有问题，而resin就在最后写一个20