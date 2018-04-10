---
title: java中string的不可不知的编码知识
---

 
# 今天要分享的内容是java的string的字符串编码
<!-- more --> 

# 首先记住一句话，字符串在 java 内存中总是按 unicode 编码存储的

# 先看一个测试用例
```angularjs
/**
 * @author hankun
 * @create 2017-06-27 14:24
 */
public class unicode {

    /**
     * 字符串转换unicode
     */
    public static String string2Unicode(String string) {

        StringBuffer unicode = new StringBuffer();
        for (int i = 0; i < string.length(); i++) {

            // 取出每一个字符
            char c = string.charAt(i);

            // 转换为unicode
            unicode.append("\\u" + Integer.toHexString(c));
        }

        return unicode.toString();
    }


    /**
     * unicode 转字符串
     */
    public static String unicode2String(String unicode) {

        StringBuffer string = new StringBuffer();

        String[] hex = unicode.split("\\\\u");

        for (int i = 1; i < hex.length; i++) {

            // 转换出每一个代码点
            int data = Integer.parseInt(hex[i], 16);

            // 追加成string
            string.append((char) data);
        }

        return string.toString();
    }

    /**
     * 测试字符串长度
     */
    public static void stringLength(String string) {

        System.out.println("String is = " + string+" ,default length = "+ string.length());
        System.out.println("String is = " + string+" ,getBytes length = "+ string.getBytes().length);
        System.out.println("String 中包含 " + (string.getBytes().length-string.length())+" 个中文");

    }

    public static void main(String[] args) {
        String test = "中文ab";

        String unicode = string2Unicode(test);

        String string = unicode2String(unicode) ;

        System.out.println(unicode);

        System.out.println(string);
        String test1 = "中文";
        String test2 = "ab";
        stringLength(test);
        stringLength(test1);
        stringLength(test2);
    }
}

```
上面这个例子里面，还提供了一个，快速判断，字符串中，包含的中文个数方法

# 解释
首先，Java中的一个char是2个字节。java采用unicode，2个字节来表示一个字符，这点与C语言中不同，c语言中采用ASCII，在大多数系统中，一个char通常占1个字节，但是在0~127整数之间的字符映射，unicode向下兼容ASCII。而Java采用unicode来表示字符，一个中文或英文字符的unicode编码都占2个字节，但如果采用其他编码方式，一个字符占用的字节数则各不相同。
    
在 GB 2312 编码或 GBK 编码中，一个英文字母字符存储需要1个字节，一个汉字字符存储需要2个字节。 在UTF-8编码中，一个英文字母字符存储需要1个字节，一个汉字字符储存需要3到4个字节。在UTF-16编码中，一个英文字母字符存储需要2个字节，一个汉字字符储存需要3到4个字节（Unicode扩展区的一些汉字存储需要4个字节）。在UTF-32编码中，世界上任何字符的存储都需要4个字节。
    
       我的系统的默认编码方式为GBK，因此对于字符串 “中文ab”，
        如果调用length（）方法返回其长度，得到的结果将为：4。该方法返回的是字符串的字符数，无论是中文字符还是英文字符，都被看做是一个字符。
        如果将其转换为byte数组，而后返回byte数组的长度，得到的结果将为：6。因为在GBK编码中，中文占2个字节，而英文字符占1个字节。


# 科普

  字符：人们使用的记号，抽象意义上的一个符号。比如：‘1’,‘中’,‘a’
   
  字节：计算机中存储数据的单元，一个8位的二进制数，是一个很具体的存储空间
  
  字符集：使用哪些字符。也就是说哪些汉字，字母和符号会被收入标准中。所包含“字符”的集合就叫做“字符集”。
  
  编码：规定每个“字符”分别用一个字节还是多个字节存储，用哪些字节来存储，这个规定就叫做“编码” 
  
  平常我们所说的“字符集”，比如：GB2312, GBK, JIS 等，除了有“字符的集合”这层含义外，同时也包含了“编码”的含义。
# iso8859－1 

属于单字节编码，最多能表示的字符范围是 0-255，应用于英文系列。比如，字母 'a' 的编码为0x61=97。 很明显，iso8859-1 编码表示的字符范围很窄，无法表示中文字符。但是，由于是单字节编码，和计算机最基础的表示单位一致，所以很多时候，仍旧使用 iso8859-1 编码来表示。而且在很多协议上，默认使用该编码。比如，虽然"中文"两个字不存在 iso8859-1 编码，以 gb2312 编码为例，应该是"d6d0 cec4" 两个字符，使用 iso8859-1 编码的时候则将它拆开为 4 个字节来表示："d6 d0 ce c4"（事实上，在进行存储的时候，也是以字节为单位处理的）。而如果是 UTF 编码，则是 6 个字节 "e4 b8 ad e6 96 87"。很明显，这种表示方法还需要以另一种编码为基础。（unicode）

# GB2312/GBK 

这是汉字的国标码，专门用来表示汉字，是双字节编码，而英文字母和 iso8859-1 一致（兼容iso8859-1 编码）。其中 gbk 编码能够用来同时表示繁体字和简体字，而 gb2312 只能表示简体字，gbk是兼容gb2312 编码的。
 
# Unicode 

这是最统一的编码，可以用来表示所有语言的字符，而且是定长双字节（也有四字节的）编码，包括英文字母在内。所以可以说它是不兼容 iso8859-1 编码的，也不兼容任何编码。不过，相对于iso8859-1 编码来说，unicode 编码只是在前面增加了一个 0 字节，比如字母 'a' 为 "00 61"。 需要说明的是，定长编码便于计算机处理（注意 GB2312/GBK 不是定长编码），而 unicode 又可以用来表示所有字符，所以在很多软件内部是使用 unicode 编码来处理的，比如 java。

# UTF 

考虑到 unicode 编码不兼容 iso8859-1 编码，而且容易占用更多的空间：因为对于英文字母，unicode 也需要两个字节来表示。所以 unicode 不便于传输和存储。因此而产生了 utf 编码，utf 编码兼容 iso8859-1 编码，同时也可以用来表示所有语言的字符，不过，utf 编码是不定长编码，每一个字符的长度从1-6个字节不等。另外，utf 编码自带简单的校验功能。一般来讲，英文字母都是用一个字节表示，而汉字使用三个字节。注意，虽然说utf是为了使用更少的空间而使用的，但那只是相对于 unicode编码来说，如果已经知道是汉字，则使用 GB2312/GBK 无疑是最节省的。不过另一方面，值得说明的是，虽然 utf 编码对汉字使用3个字节，但即使对于汉字网页，utf 编码也会比 unicode 编码节省，因为网页中包含了很多的英文字符。
   
# getBytes(charset) 

    这是 java 字符串处理的一个标准函数，其作用是将字符串所表示的字符按照 charset 编码，并以字节方式表示。
    注意字符串在 java 内存中总是按 unicode 编码存储的。
    当Java程序从输入流、文件或字符文字量等途径获得字符串时，均会做字符编码的转换，例如InputStreamReader 的构造函数中就需要指定编码方式，而对于从文件和字符文字量中获得字符串时，均采用系统默认的编码方式对字符数据进行解码。
    
考虑下面一段代码： 

       String str=”中”;
       ① byte[] bytes = str.getBytes();
       ② bytes = str.getBytes(“ISO-8859-1”);

 语句①：将一个只含有一个字符“中”的字符串文字量赋给 String 类的一个对象 str，字符文字量“中”是按照操作系统默认编码方式进行编码，在中文 windows 系统中通常是“GBK”，“中”在GBK编码中是0xD6D0，在将该字符赋给str时，Java会对该字符串进行编码转换，即将GBK编码方式的“中”转换成Unicode编码方式的“中”，Unicode编码方式“中”的编码是0x4E2D，所以str在程序运行期间在内存中的二进制表示成16进制就是0x4E2D。
 
 语句②：获得str字符串的二进制形式。getBytes(String encoding)方法需要指定编码方式，表示获得该字符串在何种编码方式中的二进制形式。此语句中没有设置参数，表示采用操作系统默认的编码方式，即此处获得的bytes是“中”在GBK编码中的二进制形式，即bytes[0]=0xD6, bytes[1]=0xD0。
 
 语句③：该语句与语句②的区别就是指定了编码方式，此处指定的是ISO-8859-1，即通常所说的Latin-1，该编码采用8bit对字符编码，所以编码空间中只有256个字符。该编码中只包含了基本的ASCII码和一些扩展的其它西欧字符，所以该字符集中不可能包含中文的“中”字，也就是说Java虚拟机无法在ISO-8859-1编码集中找到“中”字对应的编码，针对这种情况，就只返回一个问号(?,0x3f)字符，所以此时bytes.length只有1，且bytes[0]=0x3f。
  
  
# new String(byte[] bytes, String encoding) 

    getBytes()方法从字符串获得二进制的字节数组。如果要从二进制的字节数组获得字符串，则就需要使用new String(byte[] bytes, String encoding)方法，该方法按照encoding编码方法对字节数组bytes中的二进制数组进行解析，生成一个新的字符串对象。
   
      byte[] bytes = {(byte)0xD6, (byte)0xD0, (byte)0x31};
      ① String str = new String(bytes);
      ② str = new String(bytes,”ISO-8859-1”);
   
语句①：定义一个字节数组。

语句②：将该字节数组中的二进制数据按照默认的编码方式（GBK）编码成字符串，我们知道GBK中0xD6 0xD0表示“中”，0x31表示字符“1”（GBK兼容ASCII，但不兼容ISO-8859-1除ASCII之外的部分），所以str得到的值是“中1”。

语句③：该句用ISO-8859-1编码方式对该字节数据进行编码，由于在ISO-8859-1编码方式中一个字节会被解析成一个字符，所以该字节数组会被解释成包含三个字符的字符串，但由于在ISO-8859-1编码方式中没有对应0xD6和0xD0的字符，所以前两个字符会产生两个问号，由于0x31在ISO-8859-1编码中对应字符“1”（ISO-8859-1也兼容ASCII），所以此语句得到str的值是“??1”。


# 最后

我相信看了，上面这么些背景知识，是不是以前，不清楚的东西，都有写明白了？多看几次，就更能理解了 
  

 