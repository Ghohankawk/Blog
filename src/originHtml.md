layout: false
title: "关于hexo中使用自己定义的html页面方法"
---
<html>
<head>
    <meta charset="UTF-8" />
    <title>hexo中实用原生html的两种方法</title>
</head>
<body>
<h1>hexo中实用原生html的两种方法</h1>
<h1>方法一</h1>
<p>
你现在看到的这个页面，就是自己原生写的html文件。
直接在你的markdown里面的头部，写上一句，layout:false
就可以写你的熟悉的html页面了。
但是，确定也是很明显的，就是所有的标签页，那些提示都写用不了啦，变成了原生的人肉
没有ide自己写页面，当然痛苦，而且样式不可以控制
</p>
<br>
 
 
<h1>方法二</h1>
<p>在_config.yml中配置一个，skip_render属性，默认是source根部下面的
因此，我这里，提前弄好了一个自定义的小黄人html页面

<iframe   width="100%" onload="this.height=window.getComputedStyle(this).width;" frameborder="0" src="/origin/test.html"></iframe>
</p>
<br>
</body>
</html>