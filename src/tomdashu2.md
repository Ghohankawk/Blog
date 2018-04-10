---
title: 汤姆大叔的深入理解JavaScript读后感二（17——24节）
---

 
# 今天要分享的内容是tom大叔的JavaScript系列的读书观后感2
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=527602&auto=1&height=66"></iframe>
# 第十七部分，面向对象编程之概论
我们有必要掌握一些OOP基本的特征，并澄清概论中的主要概念。主要讨论封装，继承，多态，接口，那些传统的面向对象概念，可以忽略不看，不影响
# 第十八部分，面向对象编程之ECMAScript实现（推荐）
总结如下：

    1. 原始值类型
       回头来看6中用于ECMAScript程序的数据类型，前5种是原始值类型，包括Undefined、Null、Boolean、String、Number、Object。
    2. 有必要需要注意的是规范还区分了这内置对象、元素对象和宿主对象。
    所有ECMAScript实现的对象都是原生对象（其中一些是内置对象、一些在程序执行的时候创建，例如用户自定义对象）。内置对象是原生对象的一个子集、是在程序开始之前内置到ECMAScript里的（例如，parseInt, Match等）。所有的宿主对象是由宿主环境提供的，通常是浏览器，并可能包括如window、alert等。
    也有对象是由特殊的内置构造函数创建： Function（函数对象构造器）、Array（数组构造器） RegExp（正则表达式构造器）、Math（数学模块）、 Date（日期的构造器）等等，这些对象也是Object对象类型的值
    3. 字面量Literal
       
       对于三个对象的值：对象（object）,数组（array）和正则表达式（regular expression）
    4. 然而，如果我们彻底改变函数的prototype属性（通过分配一个新的对象），那原始构造函数的引用就是丢失，这是因为我们创建的对象不包括constructor属性：
    function A() {}
    A.prototype = {
      x: 10
    };
     
    var a = new A();
    alert(a.x); // 10
    alert(a.constructor === A); // false!
    因此，对函数的原型引用需要手工恢复：
    
    function A() {}
    A.prototype = {
      constructor: A,
      x: 10
    };
     
    var a = new A();
    alert(a.x); // 10
    alert(a.constructor === A); // true
    5. a.[[Prototype]] ----> Prototype <---- A.prototype
       
       此外， 实例的[[Prototype]]值确实是在构造函数的prototype属性上获取的。
       
       然而，提交prototype属性不会影响已经创建对象的原型（只有在构造函数的prototype属性改变的时候才会影响到)，就是说新创建的对象才有有新的原型，而已创建对象还是引用到原来的旧原型（这个原型已经不能被再被修改了）。
      对象的原型是对象的创建的时候创建的，并且在此之后不能修改为新的对象，如果依然引用到同一个对象，可以通过构造函数的显式prototype引用，对象创建以后，只能对原型的属性进行添加或修改。
      
      这个地方，原型链，特别难理解，详细的看第一篇，观后感的，原型链的，链接，那个是非常详细的说明 
    6. 有误解：
       
       if (foo instanceof Foo) {
         ...
       }
       这不是用来检测对象foo是否是用Foo构造函数创建的，
    所有instanceof运算符只需要一个对象属性——foo.[[Prototype]]，在原型链中从Foo.prototype开始检查其是否存在。
    
    7. alert(1..toString()); // "1"，不是语法错误
       大部分程序里使用原型是用来存储对象的方法、默认状态和共享对象的属性。
# 第十九部分，求值策略(Evaluation strategy)
很多程序员都确信在JavaScript中（甚至其它一些语言)，对象是按引用传参，而原始值类型按值传参

    1. 按值传递
       按值传递，很多开发人员都很了解了，参数的值是调用者传递的对象值的拷贝(copy of value），函数内部改变参数的值不会影响到外面的对象（该参数在外面的值），一般来说，是重新分配了新内存(我们不关注分配内存是怎么实现的——也是是栈也许是动态内存分配），该新内存块的值是外部对象的拷贝，并且它的值是用到函数内部的。
    2. 按引用传递
       另外一个众所周知的按引用传递接收的不是值拷贝，而是对象的隐式引用，如该对象在外部的直接引用地址。函数内部对参数的任何改变都是影响该对象在函数外部的值，因为两者引用的是同一个对象，也就是说：这时候参数就相当于外部对象的一个别名。
    3. 按共享传递（Call by sharing）
    最重要的区别就是：函数内部给参数重新赋新值不会影响到外部的对象（和上例按引用传递的case），但是因为该参数是一个地址拷贝，所以在外面访问和里面访问的都是同一个对象（例如外部的该对象不是想按值传递一样完全的拷贝),改变该参数对象的属性值将会影响到外部的对象。
    4. 现在我们知道了ECMAScript中将对象作为参数传递的策略了——按共享传递：修改参数的属性将会影响到外部，而重新赋值将不会影响到外部对象
    传递的是引用的拷贝（地址副本）
    
    再强调一下，这里所说对象的值是地址（address），而不是对象结构本身，将变量赋值给另外一个变量——是赋值值的引用。因此两个变量引用的是同一个内存地址。下一个赋值却是新地址，是解析与旧对象的地址绑定，然后绑定到新对象的地址上，这就是和按引用传递的最重要区别。
    
# 第二十部分，《你真懂JavaScript吗？》答案详解
总结如下：

    1.  题目1
        
        if (!("a" in window)) {
            var a = 1;
        }
        alert(a);
       答案是undefined
    2. var a = 1,
           b = function a(x) {
               x && a(--x);
           };
       alert(a);
       答案1
    3. function a(x) {
           return x * 2;
       }
       var a;
       alert(a);
       答案 a函数
    4. function b(x, y, a) {
           arguments[2] = 10;
           alert(a);
       }
       b(1, 2, 3);
       答案是10
    5. function a() {
           alert(this);
       }
       a.call(null);
        答案是window
# 第二十一部分，S.O.L.I.D五大原则之接口隔离原则ISP
没看懂，基本不用看

# 第二十二部分，S.O.L.I.D五大原则之依赖倒置原则DIP]
稍微看懂了，估计是因为java看多了，ioc，基本不用看

# 第二十三部分，JavaScript与DOM（上）——也适用于新手
    最重要的是Element, Text, Document。
    
     Element节点在页面里展示的是一个元素，所以如果你有段落元素(<p>)，你可以通过这个DOM节点来访问。
     Text节点在页面里展示的所有文本相关的元素，所以如果你的段落有文本在里面的话，你可以直接通过DOM的Text节点来访问这个文本
     Document节点代表是整个文档，它是DOM的根节点。
     
    nodeType类型，1是元素，2是属性，3是text节点，详细的type类型可以通过此地址：
    
        Node.ELEMENT_NODE == 1
        Node.ATTRIBUTE_NODE == 2
        Node.TEXT_NODE == 3
        Node.CDATA_SECTION_NODE == 4
        Node.ENTITY_REFERENCE_NODE == 5
        Node.ENTITY_NODE == 6
        Node.PROCESSING_INSTRUCTION_NODE == 7
        Node.COMMENT_NODE == 8
        Node.DOCUMENT_NODE == 9
        Node.DOCUMENT_TYPE_NODE == 10
        Node.DOCUMENT_FRAGMENT_NODE == 11
        Node.NOTATION_NODE == 12
# 第二十四部分，JavaScript与DOM（下）
    node节点的2种类型，一种是元素节点，一种是text节点，上一章节已经列出了所有的节点类型，这两种需要我们现在特别注意。创建元素可以通过createElement方法，而创建text节点可以使用createTextNode，相应代码如下：
    正如我们上章所说的，DOM和JavaScript语言是2个单独的东西，浏览器事件是DOM API的一部分，而不是JavaScript的一部分。
    
    ‘mouseover’ – 鼠标移动到某元素上的时候触发mouseover事件。
     ‘mouseout’ – 鼠标从某元素离开的时候触发mouseout事件。
     ‘mousemove’ – 鼠标在某元素上移动但未离开的时候触发mousemove事件。
     ‘change’ – 控件失去input焦点的时候触发该事件（或者值被改变的时候）。
     
     ‘load’ – 页面加载完毕（包括内容、图片、frame、object）的时候触发该事件。
     ‘resize’ – 页面大小改变的时候触发该事件（例如浏览器缩放）。
     ‘scroll’ – 页面滚动的时候触发该事件。
     ‘unload’ – 从页面或frame删除所有内容的时候触发该事件（例如离开一个页面）。
     
     严格来说，有2中不同的模型：W3C模型和微软模型，除IE之外W3C模型支持所有的现代浏览器，而微软模型只支持IE
     
     使用W3C模型的代码如下：
     
     // 格式：target.addEventListener( type, function, useCapture );  
     // 例子:  
     var myIntro = document.getElementById('intro');
     myIntro.addEventListener('click', introClick, false);
     使用IE模型的代码如下：
     
     // 格式: target.attachEvent ( 'on' + type, function );  
     // 例子:  
     var myIntro = document.getElementById('intro');
     myIntro.attachEvent('onclick', introClick);
     
     一个非常重要的内容是Event对象，当事件发生的时候出发某个函数，该Event对象将自动在函数内可用，该对象包含了很多事件触发时候的信息，但IE却没有这么实现，而是自己实现的，IE浏览器是通过全局对象window下的event属性来包含这些信息
     
     例如当你想取消默认的行为的时候你可以使用Event对象里的preventDefault()方法，但IE里不得不使用对象的returnValue属性值来控制
     
     
     事件冒泡，就是事件触发的时候通过DOM向上冒泡，首先要知道不是所有的事件都有冒泡。事件在一个目标元素上触发的时候，该事件将触发一一触发祖先节点元素，直到最顶层的元素：
     如图所示，如果a连接被点击，触发触发连接的click事件，然后触发p的click事件，以此再触发div和body的click事件。顺序不变，而且不一定是在同时触发的。
     举例来说，如果你有一个很多行的大表格，在每个<tr>上绑定点击事件是个非常危险的想法，因为性能是个大问题。流行的做法是使用事件委托。事件委托描述的是将事件绑定在容器元素上，然后通过判断点击的target子元素的类型来触发相应的事件。
     事件委托依赖于事件冒泡，如果事件冒泡到table之前被禁用的话，那上面的代码就无法工作了。
     
     经典的事件处理模型，微软的冒泡，以及网景的捕获模型，w3c的两者兼容，网上例子很多，去看解释