---
title: 关于requirejs的用法
---

 
# 今天要分享的内容是requirejs
<!-- more --> 

# AMD 简介
  前端开发在近一两年发展的非常快，JavaScript 作为主流的开发语言得到了前所未有的热捧。大量的前端框架出现了，这些框架都在尝试着解决一些前端开发中的共性问题，但是实现又不尽相同。在这个背景下，CommonJS 社区诞生了，为了让前端框架发展的更加成熟，CommonJS 鼓励开发人员一起在社区里为一些完成特定功能的框架制定规范。AMD（Asynchronous Module Definition）就是其中的一个规范。
  
# RequireJS 简介
  RequireJS 是一个非常小巧的 JavaScript 模块载入框架，是 AMD 规范最好的实现者之一。最新版本的 RequireJS 压缩后只有 14K，堪称非常轻量。它还同时可以和其他的框架协同工作，使用 RequireJS 必将使您的前端代码质量得以提升。 目前最新版本的 RequireJS 1.0.8 在 IE 6+、Firefox 2+、Safari 3.2+、Chrome 3+、Opera 10+ 上都工作的很好。

# 案例一： 加载 JavaScript 文件
     <script src="./js/require.js"></script> 
          <script> 
         require(["./js/a.js", "./js/b.js"], function() { 
                  myFunctionA(); 
                  myFunctionB(); 
             }); 
          </script>
          
          这种方式，用来加载以前的js文件，就像本地引用一样，但是没有解决全局变量的问题
# 案例二： 页面加载后执行 JavaScript
  
   <script src="./js/require.js"></script> 
       <script> 
      require(["domReady!", "./js/a.js", "./js/b.js"], function() { 
               myFunctionA(); 
               myFunctionB(); 
          });   
       </script>
       这个例子domReady!，解决了，有需要保证页面加载以后执行脚本时，RequireJS 提供了一个独立的 domReady 模块，需要去 RequireJS 官方网站下载这个模块
# AMD模块的写法
  require.js加载的模块，采用AMD规范。也就是说，模块必须按照AMD的规定来写。
  
  具体来说，就是模块必须采用特定的define()函数来定义。如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。
  
  假定现在有一个math.js文件，它定义了一个math模块。那么，math.js就要这样写：
  
    // math.js
    define(function (){
    　　　　var add = function (x,y){
    　　　　　　return x+y;
    　　　　};
    　　　　return {
    　　　　　　add: add
    　　　　};
    });
  加载方法如下：
  
    // main.js
    　　require(['math'], function (math){
    　　　　alert(math.add(1,1));
    　　});
# 加载非规范的模块
  理论上，require.js加载的模块，必须是按照AMD规范、用define()函数定义的模块。但是实际上，虽然已经有一部分流行的函数库（比如jQuery）符合AMD规范，更多的库并不符合。那么，require.js是否能够加载非规范的模块呢？
  
  回答是可以的。
  
  这样的模块在用require()加载之前，要先用require.config()方法，定义它们的一些特征。
  
  举例来说，underscore和backbone这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征。
  
    require.config({
    　　　　shim: {
    
    　　　　　　'underscore':{
    　　　　　　　　exports: '_'
    　　　　　　},
    　　　　　　'backbone': {
    　　　　　　　　deps: ['underscore', 'jquery'],
    　　　　　　　　exports: 'Backbone'
    　　　　　　}
    　　　　}
    });
  require.config()接受一个配置对象，这个对象除了有前面说过的paths属性之外，还有一个shim属性，专门用来配置不兼容的模块。具体来说，每个模块要定义（1）exports值（输出的变量名），表明这个模块外部调用时的名称；（2）deps数组，表明该模块的依赖性。
  
  比如，jQuery的插件可以这样定义：
  
    shim: {
    　　　　'jquery.scroll': {
    　　　　　　deps: ['jquery'],
    　　　　　　exports: 'jQuery.fn.scroll'
    　　　　}
    }
# require.js插件
  require.js还提供一系列插件，实现一些特定的功能。
  
  domready插件，可以让回调函数在页面DOM结构加载完成后再运行。
  
  require(['domready!'], function (doc){
  　　　　// called once the DOM is ready
  　　});
  text和image插件，则是允许require.js加载文本和图片文件。
  
  define([
  　　　　'text!review.txt',
  　　　　'image!cat.jpg'
  　　　　],
  
  　　　　function(review,cat){
  　　　　　　console.log(review);
  　　　　　　document.body.appendChild(cat);
  　　　　}
  　);
  类似的插件还有json和mdown，用于加载json文件和markdown文件。
  
# 在html中引入requirejs
  在HTML中，添加这样的 < script> 标签：
  
    <script src="/path/to/require.js" data-main="/path/to/app/config.js"></script>
  通常使用requirejs的话，我们只需要导入requirejs即可，不需要显式导入其它的js库，因为这个工作会交给requirejs来做。
  
  属性 data-main 是告诉requirejs：你下载完以后，马上去载入真正的入口文件。它一般用来对requirejs进行配置，并且载入真正的程序模块。

# 依赖一个不使用requirejs方式的库
  前面的代码是理想的情况，即依赖的js文件，里面用了 define(...) 这样的方式来组织代码的。如果没用这种方式，会出现什么情况？
  
  比如这个 hello.js :
  
    function hello() {
    alert("hello, world~");
    }
  它就按最普通的方式定义了一个函数，我们能在requirejs里使用它吗？
  
  先看下面不能正确工作的代码：
  
    requirejs.config({
    baseUrl: '/public/js',
    paths: {
      hello: 'hello'
    }
    });
    
    requirejs(['hello'], function(hello) {
    hello();
    });
  这段代码会报错，提示：
  
  Uncaught TypeError: undefined is not a function 
  原因是最后调用 hello() 的时候，这个 hello 是个 undefined . 这说明，虽然我们依赖了一个js库（它会被载入），但requirejs无法从中拿到代表它的对象注入进来供我们使用。
  
  在这种情况下，我们要使用 shim ，将某个依赖中的某个全局变量暴露给requirejs，当作这个模块本身的引用。
  
    requirejs.config({
    baseUrl: '/public/js',
    paths: {
      hello: 'hello'
    },
    shim: {
      hello: { exports: 'hello' }
    }
    });
    
    requirejs(['hello'], function(hello) {
    hello();
    });
  再运行就正常了。
  
  上面代码 exports: 'hello' 中的 hello ，是我们在 hello.js 中定义的 hello 函数。当我们使用 function hello() {} 的方式定义一个函数的时候，它就是全局可用的。如果我们选择了把它 export 给requirejs，那当我们的代码依赖于 hello 模块的时候，就可以拿到这个 hello 函数的引用了。
  
  所以： exports 可以把某个非requirejs方式的代码中的某一个**全局变量**暴露出去，当作该模块以引用。
  
  
# 无主的与有主的模块
  我遇到了一个折腾我不少时间的问题：为什么我只能使用 jquery 来依赖jquery, 而不能用其它的名字？
  
  比如下面这段代码：
    
    requirejs.config({
    baseUrl: '/public/js',
    paths: {
      myjquery: 'lib/jquery/jquery'
    }
    });
    
    requirejs(['myjquery'], function(jq) {
    alert(jq);
    });
  它会提示我：
  
  jq is undefined
  但我仅仅改个名字：
  
    requirejs.config({
    baseUrl: '/public/js',
    paths: {
      jquery: 'lib/jquery/jquery'
    }
    });
    
    requirejs(['jquery'], function(jq) {
    alert(jq);
    });
  就一切正常了，能打印出 jq 相应的对象了。
  
  为什么？我始终没搞清楚问题在哪儿。
  
# 有主的模块
  经常研究，发现原来在jquery中已经定义了：
  
    define('jquery', [], function() { ... });
  它这里的 define 跟我们前面看到的 app.js 不同，在于它多了第一个参数 'jquery' ，表示给当前这个模块起了名字 jquery ，它已经是有主的了，只能属于 jquery .
  
  所以当我们使用另一个名字：
  
  myjquery: 'lib/jquery/jquery'
  去引用这个库的时候，它会发现，在 jquery.js 里声明的模块名 jquery 与我自己使用的模块名 myjquery 冲突，便不会把它赋给 myjquery ，所以 myjquery 的值是 undefined 。
  
  所以我们在使用一个第三方的时候，一定要注意它是否声明了一个确定的模块名。
  
# 无主的模块
  如果我们不指明模块名，就像这样：
  
    define([...], function() {
    ...
    });
  那么它就是无主的模块。我们可以在 requirejs.config 里，使用任意一个模块名来引用它。这样的话，就让我们的命名非常自由，大部分的模块就是无主的。
  
# 为什么有的有主，有的无主
  可以看到，无主的模块使用起来非常自由，为什么某些库（jquery, underscore）要把自己声明为有主的呢？
  
  按某些说法，这么做是出于性能的考虑。因为像 jquery , underscore 这样的基础库，经常被其它的库依赖。如果声明为无主的，那么其它的库很可能起不同的模块名，这样当我们使用它们时，就可能会多次载入jquery/underscore。
  
  而把它们声明为有主的，那么所有的模块只能使用同一个名字引用它们，这样系统就只会载入它们一次。
  
# 强行该名称的方法
  对于有主的模块，我们还有一种方式可以挖墙角：不把它们当作满足requirejs规范的模块，而当作普通js库，然后在 shim 中导出它们定义的全局变量。
  
    requirejs.config({
    baseUrl: '/public/js',
    paths: {
      myjquery: 'lib/jquery/jquery'
    },
    shim: {
      myjquery: { exports: 'jQuery' }
    }
    });
    
    requirejs(['myjquery'], function(jq) {
    alert(jq);
    });
  这样通过暴露 jQuery 这个全局变量给 myjquery ，我们就能正常的使用它了。
  
  不过我们完全没有必要这么挖墙角，因为对于我们来说，似乎没有任何好处。
  
  
  
 # 总结
 
 关于这块部分，我是看项目里有用到这个前端的组件，而自己没有实际开发过，因此
 对这个部分，理解不深刻，也就是现在能看懂的阶段了，主要是参考了好几篇关于
 requirejs的文章总结的
 
 还有就是，这个部分其实一直就有了解，因此也不是完全不懂的去学
 
 反正是先入手学一下是很有必要的，
 
 哎，最近发现那个requirejs的中文网，不知道怎么滴就访问不了，好像只能去看英文文档了