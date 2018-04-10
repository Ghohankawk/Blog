---
title: java中的pattern和matcher的用法
---

 
# 今天要分享的内容是java语言中的pattern和matcher的用法
java.util.regex是一个用正则表达式所订制的模式来对字符串进行匹配工作的类库包。
它包括两个类：Pattern和Matcher Pattern 一个Pattern是一个正则表达式经编译后的表现模式。
Matcher 一个Matcher对象是一个状态机器，它依据Pattern对象做为匹配模式对字符串展开匹配检查。 
首先一个Pattern实例订制了一个所用语法与PERL的类似的正则表达式经编译后的模式，
然后一个Matcher实例在这个给定的Pattern实例的模式控制下进行字符串的匹配工作。
 <!-- more --> 

# 先理解捕获组的概念

```html
捕获组可以通过从左到右计算其开括号来编号，编号是从1 开始的。
例如，在表达式 ((A)(B(C)))中，存在四个这样的组：
1        ((A)(B(C)))
2        (A)
3        (B(C))
4        (C)

组0始终代表整个表达式。
```

# 详解Pattern类
  
    Pattern类用于创建一个正则表达式,也可以说创建一个匹配模式,它的构造方法是私有的,不可以直接创建,但可以通过Pattern.complie(String regex)简单工厂方法创建一个正则表达式, 
    Java代码示例: 
    Pattern p=Pattern.compile("\\w+"); 
    p.pattern();//返回 \w+ 
  
# 详解Matcher类
    说了这么多,终于轮到Matcher类登场了,Pattern.matcher(CharSequence input)返回一个Matcher对象.
    Matcher类的构造方法也是私有的,不能随意创建,只能通过Pattern.matcher(CharSequence input)方法得到该类的实例. 
    Pattern类只能做一些简单的匹配操作,要想得到更强更便捷的正则匹配操作,那就需要将Pattern与Matcher一起合作.Matcher类提供了对正则表达式的分组支持,以及对正则表达式的多次匹配支持. 
    Java代码示例: 
  
    Pattern p=Pattern.compile("\\d+"); 
    Matcher m=p.matcher("22bb23"); 
    m.pattern();//返回p 也就是返回该Matcher对象是由哪个Pattern对象的创建的 
# 注意看下面这个例子，pattern方法返回的值
```
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class StringPattern {

    public static void main(String[] args) {
        Pattern p = Pattern.compile("\\d+");
        Matcher m = p.matcher("22bb23");
        Pattern pattern = m.pattern();//返回p 也就是返回该Matcher对象是由哪个Pattern对象的创建的
        String pattern1 = pattern.pattern();
        System.out.println(pattern1);//\d+
    }
}
```
# Matcher.matches()/ Matcher.lookingAt()/ Matcher.find()区别
1. Matcher类提供三个匹配操作方法,三个方法均返回boolean类型,当匹配到时返回true,没匹配到则返回false 
   matches()对整个字符串进行匹配,只有整个字符串都匹配了才返回true 
2. lookingAt()对前面的字符串进行匹配,只有匹配到的字符串在最前面才返回true 
3. find()对字符串进行匹配,匹配到的字符串可以在任何位置. 

# Mathcer.start()/ Matcher.end()/ Matcher.group()
  
1. 当使用matches(),lookingAt(),find()执行匹配操作后,就可以利用以上三个方法得到更详细的信息. 
2.  start()返回匹配到的子字符串在字符串中的索引位置. 
3.  end()返回匹配到的子字符串的最后一个字符在字符串中的索引位置. 
4.  group()返回匹配到的子字符串 
``` 
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class StringPattern {

    public static void main(String[] args) {
        Pattern p=Pattern.compile("([a-z]+)(\\d+)");
        Matcher m=p.matcher("aaa2223bb");
        System.out.println(m.find());   //匹配aaa2223

        System.out.println(m.groupCount());  //返回2,因为有2组
        System.out.println(m.start(1));   //返回0 返回第一组匹配到的子字符串在字符串中的索引号
        System.out.println(m.start(2));   //返回3
        System.out.println(m.end(1));   //返回3 返回第一组匹配到的子字符串的最后一个字符在字符串中的索引位置.
        System.out.println(m.end(2));   //返回7
        System.out.println(m.group(1));   //返回aaa,返回第一组匹配到的子字符串
        System.out.println(m.group(2));;   //返回2223,返回第二组匹配到的子字符串
    }
}
```
# find比较特殊，特此说明
find
1. public boolean find()尝试查找与该模式匹配的输入序列的下一个子序列。  
此方法从匹配器区域的开头开始，如果该方法的前一次调用成功了并且从那时开始匹配器没有被重置，则从以前匹配操作没有匹配的第一个字符开始。  
如果匹配成功，则可以通过 start、end 和 group 方法获取更多信息。  
2. 返回：
当且仅当输入序列的子序列匹配此匹配器的模式时才返回 true。

因此有下面这种，循环find的用法
```
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class StringPattern {

    public static void main(String[] args) {
        Pattern p=Pattern.compile("\\d+");
        Matcher m=p.matcher("我的QQ是:123 我的电话是:456 我的邮箱是:789@aaa.com");
        while(m.find()) {
            System.out.println(m.group());
        }
    }
}

```
# 最后强调
现在大家应该知道,每次执行匹配操作后start(),end(),group()三个方法的值都会改变,改变成匹配到的子字符串的信息,以及它们的重载方法,也会改变成相应的信息. 
注意:只有当匹配操作成功,才可以使用start(),end(),group()三个方法,否则会抛出java.lang.IllegalStateException,也就是当matches(),lookingAt(),find()其中任意一个方法返回true时,才可以使用.

 
