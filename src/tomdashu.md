---
title: 汤姆大叔的深入理解JavaScript读后感一（1——16节）
---

 
# 今天要分享的内容是tom大叔的JavaScript系列的读书观后感

# 第一部分，编写高质量JavaScript代码的基本要点
内容不多，但是作为从头规范开始，值得遵循，现在看来，才更能明白里面的道理。
总结一下：

    1. 避免全局变量，为啥？自己去看，以及隐式全局变量的副作用，那怎么办？命名空间
    2. 推荐单一的单var形式，如  var a = 1,b = 2,myobject = {};形式
    3. for循环，预存缓存变量，for-in遍历对象用hasOwnProperty，去掉原型链的属性
    4. 避免使用eval，记住该咒语“eval()是魔鬼”，给setInterval(), setTimeout()和Function()构造函数传递字符串，大部分情况下，与使用eval()是类似的，因此要避免                     
    5. 编码规范，缩进，空格，花括号，分号
    6. 命名函数，啥时候大写，啥时候小写，推荐驼峰命名
    7. 关于注解，通常，当你深入研究一个问题，你会很清楚的知道这个代码是干嘛用的，但是，当你一周之后再回来看的时候，想必也要耗掉不少脑细胞去搞明白到底怎么工作的。就是保持注释的及时更新，因为过时的注释比没有注释更加的误导人。
# 第二部分，揭秘命名函数表达式
总结如下：

    1. 什么是申明函数？
    2. 什么是表达式函数？
    3. 什么是函数语句？
    4. 什么是命名函数？
    5. 主要是区别的地方，很细微，然后又很多浏览器的怪异行为，坑比较多
    6. 还有一种函数表达式不太常见，就是被括号括住的(function foo(){})，他是表达式的原因是因为括号 ()是一个分组操作符，它的内部只能包含表达式
    7. 后面的部分，如果第一次看，肯定看不懂，是啥意思，建议先忽略掉
    
# 第三部分，全面解析Module模式
总结如下：

    1. 正常的module模式，缺点就是，每次都必须new，每个实例都是单独的
   
   ```
   var Calculator = function (eq) {
          //这里可以声明私有成员
      
          var eqCtl = document.getElementById(eq);
      
          return {
              // 暴露公开的成员
              add: function (x, y) {
                  var val = x + y;
                  eqCtl.innerHTML = val;
              }
          };
      };
   ```
    2. 改进过后的，利用函数自执行，可以直接使用
       
   ```
var blogModule = (function () {
    var my = {}, privateName = "博客园";

    function privateAddTopic(data) {
        // 这里是内部处理代码
    }

    my.Name = privateName;
    my.AddTopic = function (data) {
        privateAddTopic(data);
    };

    return my;
} ());
   ```
    3. 改進松耦合
       
   ```
var blogModule = (function (my) {

    // 添加一些功能   
    
    return my;
} (blogModule || {}));  
   ```
   
       4. 克隆
          
 ```
     var blogModule = (function (old) {
          var my = {},
              key;
       for (key in old) {
           if (old.hasOwnProperty(key)) {
               my[key] = old[key];
           }
       }
   
       var oldAddPhotoMethod = old.AddPhoto;
       my.AddPhoto = function () {
           // 克隆以后，进行了重写，当然也可以继续调用oldAddPhotoMethod
       };
   
       return my;
   } (blogModule));
      
   ```
      
          5. 子模块
             
 ```
blogModule.CommentSubModule = (function () {
    var my = {};
    // ...

    return my;
} ());
 ```

# 第四部分，立即调用的函数表达式

    1. 自動執行
    (function () { /* code */ } ()); // 推荐使用这个
    2. (function () { /* code */ })(); // 但是这个也是可以用的，括號
    3.在一个表达式后面加上括号()，该表达式会立即执行，但是在一个语句后面加上括号()，是完全不一样的意思，他的只是分组操作符。
    4. 闭包的经典例子
    
   ```
   错误的：
   var elems = document.getElementsByTagName('a');
   
   for (var i = 0; i < elems.length; i++) {
   
       elems[i].addEventListener('click', function (e) {
           e.preventDefault();
           alert('I am link #' + i);
       }, 'false');
   
   }
正确的：会锁住对应的index的值，虽然i最后为10，但是之前的值，已经记录好了
var elems = document.getElementsByTagName('a');

for (var i = 0; i < elems.length; i++) {

    (function (lockedInIndex) {

        elems[i].addEventListener('click', function (e) {
            e.preventDefault();
            alert('I am link #' + lockedInIndex);
        }, 'false');

    })(i);

}

   ``` 
       5. 不管什么是自执行，还是立即调用，不用分那么清楚，只需知道立即执行一次匿名函数就够了
       
# 第五部分，强大的原型和原型链

    1. 原型关键词，prototype
    2. 说白了，原型就是一个对象
    3. 通过hasOwnProperty，区分属性从哪里来的
    4. 默认还有一个原型的链条，一直到Object.prototype为止
    5. 所有的对象都有"[[prototype]]"属性（通过__proto__访问），该属性对应对象的原型
    6. 所有的函数对象都有"prototype"属性，该属性的值会被赋值给该函数创建的对象的"__proto__"属性
    7. 所有的原型对象都有"constructor"属性，该属性对应创建所有指向该原型的实例的构造函数
    8. 函数对象和原型对象通过"prototype"和"constructor"属性进行相互关联
    9. 最简单易懂的看下边
   [原型链介绍](https://www.talkingcoder.com/article/6360227501704156372)

# 第六七八部分，S.O.L.I.D五大原则之单一职责SRP（忽略）

    The Single Responsibility Principle（单一职责SRP）
    The Open/Closed Principle（开闭原则OCP）
    The Liskov Substitution Principle（里氏替换原则LSP）
    The Interface Segregation Principle（接口分离原则ISP）
    The Dependency Inversion Principle（依赖反转原则DIP）
# 第九部分，根本没有“JSON对象”这回事！

    简单的说
    1. // 这是JSON字符串
       var foo = '{ "prop": "val" }';
    2. // 这是对象字面量
       var bar = { "prop": "val" };
    3. // 将字符串反序列化成json对象
       var my_obj = JSON.parse( foo );
    4. json对象，和字面量对象，明显的区别，前者必须有引号，后者去掉引号可以
           var bar = { prop: "val" };合法的
    
# 第十部分，JavaScript核心（晋级高手必读篇）
    1. 执行上下文栈(Execution Context Stack)，有三种类型：global, function和eval。
    2. 执行上下文(Execution Context)，变量对象(variable object)，this指针(this value)，作用域链(scope chain)
    函数表达式[function expression]（而不是函数声明[function declarations，区别请参考本系列第2章]）是不包含在VO[variable object]里面的
    3. 变量对象(variable object) 是与执行上下文相关的 数据作用域(scope of data) 。
       它是与上下文关联的特殊对象，用于存储被定义在上下文中的 变量(variables) 和 函数声明(function declarations) 。
    4. 当函数被调用者激活，这个特殊的活动对象(activation object) 就被创建了
    5. 作用域链的原理和原型链很类似，如果这个变量在自己的作用域中没有，那么它会寻找父级的，直到最顶层。
    6. 这表示，在我们去搜寻__parent__之前，首先会去__proto__的链接中。
    7. 闭包是一系列代码块（在ECMAScript中是函数），并且静态保存所有父级的作用域。通过这些保存的作用域来搜寻到函数中的自由变量。
    8. 在ECMAScript中，是不可以给this赋值的，因为，还是那句话，this不是变量。
    9. this是执行上下文环境的一个属性，而不是某个变量对象的属性
# 第十一部分，执行上下文（Execution Contexts）
可以把调用上下文作为第二个参数传递给eval。那么，如果这个上下文存在，就有可能影响“私有”(有人喜欢这样叫它)变量。
# 第十二部分，变量对象（Variable Object）
```angularjs
    if (true) {
        var a = 1;
        } else {
        var b = 2;
        }
    alert(a); // 1
    alert(b); // undefined,不是b没有声明，而是b的值是undefined
```
变量相对于简单属性来说，变量有一个特性(attribute)：{DontDelete},这个特性的含义就是不能用delete操作符直接删除变量属性。
```angularjs
a = 10;
alert(window.a); // 10
 
alert(delete a); // true
 
alert(window.a); // undefined
 
var b = 20;
alert(window.b); // 20
 
alert(delete b); // false
 
alert(window.b); // still 20

```
但是这个规则在有个上下文里不起走样，那就是eval上下文，变量没有{DontDelete}特性。
# 第十三部分，This? Yes,this!
    1，一个函数上下文中确定this值的通用规则如下：
    2. 在一个函数上下文中，this由调用者提供，由调用函数的方式来决定。如果调用括号()的左边是引用类型的值，this将设为引用类型值的base对象（base object），在其他情况下（与引用类型不同的任何其它属性），这个值为null。不过，实际不存在this的值为null的情况，因为当this的值为null的时候，其值会被隐式转换为全局对象。
    3. 我们可以很明确的告诉你，为什么用表达式的不同形式激活同一个函数会不同的this值，答案在于引用类型（type Reference）不同的中间值。
    4. 标识符是变量名，函数名，函数参数名和全局对象中未识别的属性名
    5. (function () {
         alert(this); // null => global
       })();
    6. 函数调用中手动设置this，apply,call
    7. 作为构造器调用的函数中的this,都将this的值设置为新创建的对象。
    8. 引用类型和this为null,默认为golbal
# 第十四部分，作用域链(Scope Chain)
    1. 函数上下文的作用域链在函数调用时创建的，包含活动对象和这个函数内部的[[scope]]属性。
    2. 注意这重要的一点－－[[scope]]在函数创建时被存储－－静态（不变的），永远永远，直至函数销毁 
    3. 闭包理解
    var x = 10;
     
    function foo() {
      alert(x);
    }
     
    (function () {
      var x = 20;
      foo(); // 10, but not 20
    })();
    [[Scope]]包括在函数内创建的词法作用域（父变量对象）。当函数进一步激活时，在变量对象的这个词法链（静态的存储于创建时）中，来自较高作用域的变量将被搜寻。
    此外，这个例子也清晰的表明，一个函数（这个例子中为从函数“foo”返回的匿名函数）的[[scope]]持续存在，即使是在函数创建的作用域已经完成之后。
    4. 通过函构造函数创建的函数的[[scope]]属性总是唯一的全局对象
    5. 源于ECMAScript 的原型特性。如果一个属性在对象中没有直接找到，查询将在原型链中继续。即常说的二维链查找。（1）作用域链环节；（2）每个作用域链－－深入到原型链环节
    6. 全局和eval上下文中的作用域链,全局上下文的作用域链仅包含全局对象
    7. 在代码执行阶段有两个声明能修改作用域链。这就是with声明和catch语句。它们添加到作用域链的最前端，对象须在这些声明中出现的标识符中查找。
# 第十五部分，函数（Functions）
    1。 只有这2个位置可以声明函数，也就是说:不可能在表达式位置或一个代码块中定义它。
    2. // 函数可以在如下地方声明：
       // 1) 直接在全局上下文中
       function globalFD() {
         // 2) 或者在一个函数的函数体内
         function innerFD() {}
       }
    3. 相当一部分问题出现了，我们为什么需要函数表达式？答案很明显——在表达式中使用它们，”不会污染”变量对象。最简单的例子是将一个函数作为参数传递给其它函数。
    4. 这种模式中，初始化的FE的名称通常被忽略：
    (function () {
       // 初始化作用域 
    })();
    5. ”为何在函数创建后的立即调用中必须用圆括号来包围它？”，答案就是：表达式句子的限制就是这样的。
    6. function () {
         ...
       }();
        
       // 即便有名称
        
       function foo() {
         ...
       }();
    如果在全局代码里定义（也就是程序级别），解释器会将它看做是函数声明，因为他是以function关键字开头，第一个例子，我们会得到SyntaxError错误，是因为函数声明没有名字（我们前面提到了函数声明必须有名字）。
    
    第二个例子，我们有一个名称为foo的一个函数声明正常创建，但是我们依然得到了一个语法错误——没有任何表达式的分组操作符错误。在函数声明后面他确实是一个分组操作符，而不是一个函数调用所使用的圆括号。
# 第十六部分，闭包（Closures）
    var z = 10;
    
    function foo() {
      alert(z);
    }
    
    foo(); // 10 – 使用静态和动态作用域的时候
    
    (function () {
    
      var z = 20;
      foo(); // 10 – 使用静态作用域, 20 – 使用动态作用域
    
    })();
    
    // 将foo作为参数的时候是一样的
    (function (funArg) {
    
      var z = 30;
      funArg(); // 10 – 静态作用域, 30 – 动态作用域
    
    })(foo);
    上述描述的就是两类funarg问题 —— 取决于是否将函数以返回值返回（第一类问题）以及是否将函数当函数参数使用（第二类问题）。
    
    为了解决上述问题，就引入了 闭包的概念。
    
    这里说明一下，开发人员经常错误将闭包简化理解成从父上下文中返回内部函数，甚至理解成只有匿名函数才能是闭包。
    
    再说一下，因为作用域链，使得所有的函数都是闭包（与函数类型无关： 匿名函数，FE，NFE，FD都是闭包）。
    ECMAScript中，闭包指的是：
    
    从理论角度：所有的函数。因为它们都在创建的时候就将上层上下文的数据保存起来了。哪怕是简单的全局变量也是如此，因为函数中访问全局变量就相当于是在访问自由变量，这个时候使用最外层的作用域。
    从实践角度：以下函数才算是闭包：
    即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）
    在代码中引用了自由变量
    
    顺便提下，函数对象的 apply 和 call方法，在函数式编程中也可以用作应用函数。 apply和call已经在讨论“this”的时候介绍过了；这里，我们将它们看作是应用函数 —— 应用到参数中的函数（在apply中是参数列表，在call中是独立的参数）：
    
    闭包还有另外一个非常重要的应用 —— 延迟调用：
    
    var a = 10;
    setTimeout(function () {
      alert(a); // 10, after one second
    }, 1000);
    
    还可以创建封装的作用域来隐藏辅助对象：
    
    var foo = {};
    
    // 初始化
    (function (object) {
    
      var x = 10;
    
      object.getX = function _getX() {
        return x;
      };
    
    })(foo);
    
    alert(foo.getX()); // 获得闭包 "x" – 10