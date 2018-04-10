title: 如何展示自己定义的一些html页面？
---

今天我们说点什么呢？直接看下面的效果：
<iframe   width="100%" onload="this.height=window.getComputedStyle(this).width;" frameborder="0" src="/origin/test.html"></iframe>
<!-- more --> 
上面的这三个小黄人，是我自己内部的html文件，
采取的方案，就是上篇文章中所说的两种方案结合

# 1方案的好处在于，他和hexo主题相结合，保持了主题的一致性
# 2方案的好处在于，完全能自定义html文件，包括里面的css和js

    
因此我的思路，就是利用这两种方案的优点结合起来，定义这个markdown模板

只要每次，改一下iframe中的src路径地址，就可以了，剩下的可以随意展示定制自己的网站了。
