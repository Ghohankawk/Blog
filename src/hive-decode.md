title: 关于hive中decode函数，默认转码没有GBK中文字符集的问题
---

今天要分享的内容是hive的select查询中的decode函数，对url进行转码的查询，看了官方文档，心都凉了，如下
string
[decode(binary bin, string charset)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)
Decodes the first argument into a String using the provided character set (one of 'US-ASCII', 'ISO-8859-1', 'UTF-8', 'UTF-16BE', 'UTF-16LE', 'UTF-16'). If either argument is null, the result will also be null. (As of Hive 0.12.0.)
<!-- more --> 
# 官方文档
上面的那句话，就是说，默认的编码类型，只能从中的一个字符类型，其他不在范围内的肯定识别不了    


# 比如说，%cb%d1%b9%b7

上面的这个转码以后是，搜狗

前提必须是，用gbk，gb1312，gb18030随便一个字符集就可以了，他们的三个区别，
基本就是gb1312<gbk<gb18030,就是说，一个包含的字符集比一个多，也就是更全

如果你用java的utf-8解码，肯定是乱码中文

如果你用js的decodeURI()的这个方法，会报错，因为他不是utf-8的转码

# 搜狗的utf-8的编码是多少？
    %e6%90%9c%e7%8b%97
因此，你无论用java的urldecoder的decode方法，或者
js中的decodeURI("%e6%90%9c%e7%8b%97")方法，都是能得到正确的编码的
# 那在hive中怎么办？

关于怎么办这个地方，真是被坑了好久了
1. 资料少，根本搜不到
2. 你也不会搜，也就是不会提问，当然搜不到
3. 总之，就是找不到答案
# 答案就是，另外的一个内建函数reflect

    select  reflect('java.net.URLDecoder', 'decode','%cb%d1%b9%b7' , "GBK") from tmp_table  
 
光看名字，就会让你想到，java中的反射，果不其然，这次就到了，你熟悉的地方了

因为网上的例子太少了，就连写出上面的查询，都是各种坑啊

# 最后一个无关的问题，charset.forName(),或者，decode函数，字符集区分大小写吗？

不区分，官方文档里面说了

# 如何做到不区分的？Charset gg = Charset.forName("gg");为什么识别是错的？

这个本来和hive是无关的，最开始运行的时候报错了，以为是这个地方的问题

最后最终调试了一下代码，总算是知道，为啥可以做到忽略大小写的问题，里面有一个很关键的排序比较器

网上的资源，太少了，根本没有一个讲这个部分的原理，看了半天硬是没有看明白

最终在不限的努力，找到了一个关键类

ASCIICaseInsensitiveComparator

就是他，起到了忽略大小写的关键原因，可以用ide反向最终一下，一看就明白

 