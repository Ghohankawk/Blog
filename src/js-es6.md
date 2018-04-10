title: ES6新特性概览总结
---
什么是ES6？
=============
 ES6（ECMAScript 6 == ECMAScript2015）是即将到来的新版本JavaScript语言的标准，代号harmony,上一次标准的制订还是2009年出台的ES5。
大部分标准已经就绪，且各浏览器对ES6的支持也正在实现中。
目前想要运行ES6代码的话，可以用[google/traceur-compiler](http://google.github.io/traceur-compiler/demo/repl.html#)将代码转译。
当然，并不是所有ES6新特性都被实现了，有一些还是无法测试的。
如果你还不知道什么是ES6的话，它是JavaScript一个新的实现，几个新特性。

首先，一个简单的JavaScript时间线，不了解历史的人也无法创造历史。

- 1995年：JavaScript以LiveScript之名诞生
- 1997年：ECMAScript标准确立
- 1999年：ES3发布，IE5非常生气
- 2000年-2005年：XMLHttpRequest，熟知为AJAX，在如Outlook Web Access(2002)、Oddpost(2002)、Gmail(2004)、Google Maps(2005)中得到了广泛的应用
- 2009年：ES5发布（这是我们目前用的最多的版本），带来了forEach / Object.keys / Object.create（特地为Douglas Crockford所造，JSON标准创建者） ，还有JSON标准。

历史课上完了，我们回来讲编程。
<!-- more --> 
箭头操作符是什么？
=============
其实就是类比java中的lambda表达式，ES6中新增的箭头操作符=>便有异曲同工之妙。它简化了函数的书写。操作符左边为输入的参数，而右边则是进行的操作以及返回的值Inputs=>outputs。
使用箭头函数需要注意：
- 函数体内的this对象，绑定定义时所在的对象，而不是使用时所在的对象。
- 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
- 不可以使用arguments对象，该对象在函数体内不存在。


    var f = () => 5;
    // 等同于
    var f = function (){ return 5 };
    
    var sum = (num1, num2) => num1 + num2;
    // 等同于
    var sum = function(num1, num2) {
        return num1 + num2;
    };
        
##为什么有？
JS中回调是经常的事，而一般回调又以匿名函数的形式出现，每次都需要写一个function，甚是繁琐。当引入箭头操作符后可以方便地写回调了，去掉繁琐的function   
     
     var array = [1, 2, 3];
     //传统写法
     array.forEach(function(v, i, a) {
         console.log(v);
     });
     //ES6
     array.forEach(v => console.log(v));
class是什么？
=============     
ES6中添加了对类的支持，引入了class关键字（其实class在JavaScript中一直是保留字，目的就是考虑到可能在以后的新版本中会用到，现在终于派上用场了）。JS本身就是面向对象的，ES6中提供的类实际上只是JS原型模式的包装。现在提供原生的class支持后，对象的创建，继承更加直观了，并且父类方法的调用，实例化，静态方法和构造函数等概念都更加形象化。

    //类的定义
    class Animal {
    	//ES6中新型构造器
        constructor(name) {
            this.name = name;
        }
        //实例方法
        sayName() {
            console.log('My name is '+this.name);
        }
    }
    //类的继承
    class Programmer extends Animal {
        constructor(name) {
        	//直接调用父类构造器进行初始化
            super(name);
        }
        program() {
            console.log("I'm coding...");
        }
    }
    //测试我们的类
    var animal=new Animal('dummy'),
    wayou=new Programmer('wayou');
    animal.sayName();//输出 ‘My name is dummy’
    wayou.sayName();//输出 ‘My name is wayou’
    wayou.program();//输出 ‘I'm coding...’
    
增强的对象字面量是什么？
=============  
对象字面量被增强了，写法更加简洁与灵活，同时在定义对象的时候能够做的事情更多了。具体表现在：
- 可以在对象字面量里面定义原型
- 定义方法可以不用function关键字
- 直接调用父类方法  


    var Person = {
      name: '张三',
    
      //等同于birth: birth
      birth,
    
      // 等同于hello: function ()...
      hello() { console.log('我的名字是', this.name); }
    };
    
    var lastWord = "last word";
    
    var a = {
        "first word": "hello",
        [lastWord]: "world"
    };
    
    //表达式属性
    a["first word"] // "hello"
    a[lastWord] // "world"
    a["last word"] // "world"


    //通过对象字面量创建对象
    var human = {
        breathe() {
            console.log('breathing...');
        }
    };
    var worker = {
        __proto__: human, //设置此对象的原型为human,相当于继承human
        company: 'freelancer',
        work() {
            console.log('working...');
        }
    };
    human.breathe();//输出 ‘breathing...’
    //调用继承来的breathe方法
    worker.breathe();//输出 ‘breathing...’
    
字符串模板是什么？
=============  
ES6中允许使用反引号 ` 来创建字符串，此种方法创建的字符串里面可以包含由美元符号加花括号包裹的变量${vraible}。
    
    //产生一个随机数
    var num=Math.random();
    //将这个数字输出到console
    console.log(`your num is ${num}`);
    
    `In JavaScript'\n' is a line-feed.`
    
    // 多行字符串
    console.log(`string text line 1
    string text line 2`);
    
    // 字符串中嵌入变量
    var name = "Bob", time = "today";
    `Hello ${name}, how are you ${time}?`
    
    //反斜杠转义
    var greeting = `\`Yo\` World!`;
    
    //对象属性
    var obj = {x: 1, y: 2};
    console.log(`${obj.x + obj.y}`)
    
    //函数调用
    function fn() {
      return "Hello World";
    }
    console.log(`foo ${fn()} bar`);
    
自动解析是什么？
=============  
自动解析数组或对象中的值。比如若一个函数要返回多个值，常规的做法是返回一个对象，将每个值做为这个对象的属性返回。但在ES6中，利用解构这一特性，可以直接返回一个数组，然后数组中的值会自动被解析到对应接收该值的变量中。
    
    var [x,y]=getVal(),//函数返回值的解构
        [name,,age]=['wayou','male','secrect'];//数组解构
    
    function getVal() {
        return [ 1, 2 ];
    }
    
    console.log('x:'+x+', y:'+y);//输出：x:1, y:2 
    console.log('name:'+name+', age:'+age);//输出： name:wayou, age:secrect 
    
    let [foo, [[bar], baz]] = [1, [[2], 3]];
    let [x, y='b'] = ['a', undefined]; // x='a', y='b'
    let [a, b, c] = new Set(["a", "b", "c"]);
    
    var { foo, bar } = { foo: "aaa", bar: "bbb" };
    
    var {x, y = 5} = {x: 1};
    console.log(x, y) // 1, 5
    
    let { log, sin, cos } = Math;
    
    function move({x=0, y=0} = {}) {
      return [x, y];
    }
    
    move({x: 3}); // [3, 0]
    move({}); // [0, 0]
    move(); // [0, 0]
    
参数默认值，不定参数，拓展参数是什么？
=============  
- 默认参数值（默认赋值）

现在可以在定义函数的时候指定参数的默认值了，而不用像以前那样通过逻辑或操作符来达到目的了。

    function sayHello(name){
    	//传统的指定默认参数的方式
    	var name=name||'dude';
    	console.log('Hello '+name);
    }
    //运用ES6的默认参数
    function sayHello2(name='dude'){
    	console.log(`Hello ${name}`);
    }
    sayHello();//输出：Hello dude
    sayHello('Wayou');//输出：Hello Wayou
    sayHello2();//输出：Hello dude
    sayHello2('Wayou');//输出：Hello Wayou
    
- 不定参数（三个点参数）

不定参数是在函数中使用命名参数同时接收不定数量的未命名参数。这只是一种语法糖，在以前的JavaScript代码中我们可以通过arguments变量来达到这一目的。不定参数的格式是三个句点后跟代表所有不定参数的变量名。比如下面这个例子中，…x代表了所有传入add函数的参数。
    
    //将所有参数相加的函数
    function add(...x){
    	return x.reduce((m,n)=>m+n);
    }
    //传递任意个数的参数
    console.log(add(1,2,3));//输出：6
    console.log(add(1,2,3,4,5));//输出：15
    
- 拓展参数（三个点数组参数）
   
拓展参数则是另一种形式的语法糖，它允许传递数组或者类数组直接做为函数的参数而不用通过apply。   

    var people=['Wayou','John','Sherlock'];
    //sayHello函数本来接收三个单独的参数人一，人二和人三
    function sayHello(people1,people2,people3){
    	console.log(`Hello ${people1},${people2},${people3}`);
    }
    //但是我们将一个数组以拓展参数的形式传递，它能很好地映射到每个单独的参数
    sayHello(...people);//输出：Hello Wayou,John,Sherlock 
    
    //而在以前，如果需要传递数组当参数，我们需要使用函数的apply方法
    sayHello.apply(null,people);//输出：Hello Wayou,John,Sherlock 
    
let与const 关键字是什么？
=============  
可以把let看成var，只是它定义的变量被限定在了特定范围内才能使用，而离开这个范围则无效。const则很直观，用来定义常量，即无法被更改值的变量。
    
    for (let i=0;i<2;i++)console.log(i);//输出: 0,1
    console.log(i);//输出：undefined,严格模式下会报错
    对比之前的var
    for (var i=0;i<2;i++)console.log(i);//输出: 0,1
    console.log(i);//输出：2
 
for of 值遍历是什么？
=============  
我们都知道for in 循环用于遍历数组，类数组或对象，ES6中新引入的for of循环功能相似，不同的是每次循环它提供的不是序号而是值。

    var someArray = [ "a", "b", "c" ];
     
    for (v of someArray) {
        console.log(v);//输出 a,b,c
    }
    for of 和 for in 的对比区别
    for (v in someArray) {
            console.log(v);//输出 0,1,2
    }
    
    var a1 = [1, 2, 3, 4];
    var a2 = [for (i of a1) i * 2];
    a2 // [2, 4, 6, 8]
    
    var years = [ 1954, 1974, 1990, 2006, 2010, 2014 ];
    [for (year of years) if (year > 2000 && year < 2010) year];
    
模块是什么？（关键字module）
=============  
在ES6标准中，JavaScript原生支持module了。这种将JS代码分割成不同功能的小块进行化的概念是在一些三方规范中流行起来的，比如CommonJS和AMD模式。

将不同功能的代码分别写在不同文件中，各模块只需导出公共接口部分，然后通过模块的导入的方式可以在其他地方使用    

    // point.js
    module "point" {
        export class Point {
            constructor (x, y) {
                public x = x;
                public y = y;
            }
        }
    }
     
    // myapp.js
    //声明引用的模块
    module point from "/point.js";
    //这里可以看出，尽管声明了引用的模块，还是可以通过指定需要的部分进行导入
    import Point from "point";
     
    var origin = new Point(0, 0);
    console.log(origin);
    
    module命令可以取代import语句，达到整体输入模块的作用。
    // circle.js
    export function area(radius) {
      return Math.PI * radius * radius;
    }
    
    export function circumference(radius) {
      return 2 * Math.PI * radius;
    }
    
    // main.js
    module circle from 'circle';
    
    console.log("圆面积：" + circle.area(4));
    console.log("圆周长：" + circle.circumference(14));
    
    
    export default命令定义模块的默认方法。
    // export-default.js
    export default function () {
        console.log('foo');
    }
    
    // import-default.js
    import customName from './export-default';
    customName(); // 'foo'
    
Map，Set 和 WeakMap，WeakSet是什么？（新增数据类型）
=============  
这些是新加的集合类型，提供了更加方便的获取属性值的方法，不用像以前一样用hasOwnProperty来检查某个属性是属于原型链上的呢还是当前对象的。同时，在进行属性值添加与获取时有专门的get，set 方法。
类比和java中的类型
    
    // Sets
    var s = new Set();
    s.add("hello").add("goodbye").add("hello");
    s.size === 2;
    s.has("hello") === true;
    
    // Maps
    var m = new Map();
    m.set("hello", 42);
    m.set(s, 34);
    m.get(s) == 34;
    
    // Weak Maps
    var wm = new WeakMap();
    wm.set(s, { extra: 42 });
    wm.size === undefined
    
    // Weak Sets
    var ws = new WeakSet();
    ws.add({ data: 42 });//因为添加到ws的这个临时对象没有其他变量引用它，所以ws不会保存它的值，也就是说这次添加其实没有意思
    
有时候我们会把对象作为一个对象的键用来存放属性值，普通集合类型比如简单对象会阻止垃圾回收器对这些作为属性键存在的对象的回收，有造成内存泄漏的危险。而WeakMap,WeakSet则更加安全些，这些作为属性键的对象如果没有别的变量在引用它们，则会被回收释放掉
WeakMap结构与Map结构基本类似，唯一的区别是它只接受对象作为键名（null除外），不接受原始类型的值作为键名，而且键名所指向的对象，不计入垃圾回收机制，有助于防止内存泄漏。

Map结构类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
注意Map的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞（clash）的问题，我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。只有对同一个对象的引用，Map结构才将其视为同一个键

    var map = new Map();
    
    map.set(['a'], 555); 
    map.get(['a']) // undefined

Promises是什么？（异步处理，ajax）
=============  
Promises是处理异步操作的一种模式，之前在很多三方库中有实现，比如jQuery的deferred 对象。当你发起一个异步请求，并绑定了.when(), .done()等事件处理程序时，其实就是在应用promise模式。

    //创建promise
    var promise = new Promise(function(resolve, reject) {
        // 进行一些异步或耗时操作
        if ( /*如果成功 */ ) {
            resolve("Stuff worked!");
        } else {
            reject(Error("It broke"));
        }
    });
    //绑定处理程序
    promise.then(function(result) {
    	//promise成功的话会执行这里
        console.log(result); // "Stuff worked!"
    }, function(err) {
    	//promise失败会执行这里
        console.log(err); // Error: "It broke"
    });

Proxies是什么？（设计模式）
=============  
Proxy可以监听对象身上发生了什么事情，并在这些事情发生后执行一些相应的操作。一下子让我们对一个对象有了很强的追踪能力，同时在数据绑定方面也很有用处。

Proxy用于修改某些操作的默认行为，等于在目标对象之前，架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。而Proxy.revocable()方法则返回一个可取消的Proxy实例。

    //定义被侦听的目标对象
    var engineer = { name: 'Joe Sixpack', salary: 50 };
    //定义处理程序
    var interceptor = {
      set: function (receiver, property, value) {
        console.log(property, 'is changed to', value);
        receiver[property] = value;
      }
    };
    //创建代理以进行侦听
    engineer = Proxy(engineer, interceptor);
    //做一些改动来触发代理
    engineer.salary = 60;//控制台输出：salary is changed to 60
    
    var person = {
      name: "张三"
    };
    
    var proxy = new Proxy(person, {
        get: function(target, property) {
            return property in target ? target[property] : "米有";
        }
    });
    
    proxy.name // "张三"
    proxy.title // "米有"
    
    

对于处理程序，是在被侦听的对象身上发生了相应事件之后，处理程序里面的方法就会被调用，上面例子中我们设置了set的处理函数，表明，如果我们侦听的对象的属性被更改，也就是被set了，那这个处理程序就会被调用，同时通过参数能够得知是哪个属性被更改，更改为了什么值。

Math，Number，String，Object 的新API是什么？
=============  
    
    Number.EPSILON
    Number.isInteger(Infinity) // false
    Number.isNaN("NaN") // false
    
    Math.acosh(3) // 1.762747174039086
    Math.hypot(3, 4) // 5
    Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2
    
    "abcde".contains("cd") // true
    "abc".repeat(3) // "abcabcabc"
    
    Array.from()：用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象
    Array.from(document.querySelectorAll('*')) // Returns a real Array
    
    Array.of()方法用于将一组值，转换为数组。弥补数组构造函数Array()的不足。
    Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
    [0, 0, 0].fill(7, 1) // [0,7,7]
    [1,2,3].findIndex(x => x == 2) // 1
    ["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
    ["a", "b", "c"].keys() // iterator 0, 1, 2
    ["a", "b", "c"].values() // iterator "a", "b", "c"
    
    Object.assign(Point, { origin: new Point(0,0) })

    二进制和八进制数值的新的写法，分别用前缀0b和0o表示
    0b111110111 === 503 // true
    0o767 === 503 // true
    


Symbols是什么？
============= 
我们知道对象其实是键值对的集合，而键通常来说是字符串。而现在除了字符串外，我们还可以用symbol这种值来做为对象的键。Symbol是一种基本类型，像数字，字符串还有布尔一样，它不是一个对象。Symbol 通过调用symbol函数产生，它接收一个可选的名字参数，该函数返回的symbol是唯一的。之后就可以用这个返回值做为对象的键了。Symbol还可以用来创建私有属性，外部无法直接访问由symbol做为键的属性值。

Symbol是一种新的原始数据类型，表示独一无二的ID，它通过Symbol函数生成。凡是属性名属于Symbol类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。Symbol函数可以接受一个字符串作为参数，表示Symbol实例的名称。

    (function() {
    
      // 创建symbol
      var key = Symbol("key");
    
      function MyClass(privateData) {
        this[key] = privateData;
      }
    
      MyClass.prototype = {
        doStuff: function() {
          ... this[key] ...
        }
      };
    
    })();
    
    var c = new MyClass("hello")
    c["key"] === undefined//无法访问该属性，因为是私有的
    
    var mySymbol = Symbol('Test');
    mySymbol.name
    // Test
    
    // 有参数的情况
    var s1 = Symbol("foo");
    var s2 = Symbol("foo");
    
    s1 === s2 // false
    
    //不能与其他类型的值进行运算
    var sym = Symbol('My symbol');
    '' + sym
    // TypeError: Cannot convert a Symbol value to a string
    
    //可以转换为字符串
    String(sym)
    // 'Symbol(My symbol)'
    
Generator 函数是什么？(个人认为最不好理解的)
============= 
Generator是一个普通函数，有两个特征：一是function命令与函数名之间有一个星号；二是函数体内部使用yield语句，定义遍历器的每个成员。

Generator可以理解成一个函数的内部状态的遍历器，每调用一次，函数的内部状态发生一次改变。换句话说，它就是一个改装了的 Iterator 遍历器，通过 yield 来增加一个 next() 节点。
    
先看一下Generator函数的完整作用： 用Generator函数定义helloWorldGenerator，它的遍历器有两个成员“hello”和“world”。当调用Generator函数的时候，该函数并不执行，而是返回一个遍历器（可以理解成暂停执行）。以后，每次调用这个遍历器的next方法，就从函数体的头部或者上一次停下来的地方开始执行（可以理解成恢复执行），直到遇到下一个yield语句为止。

for...of循环可以自动遍历Generator函数，且此时不再需要调用next方法。

    function *foo() {
      yield 1;
      yield 2;
      yield 3;
      yield 4;
      yield 5;
      return 6;
    }
    
    for (let v of foo()) {
      console.log(v);
    }
    // 1 2 3 4 5
    
## next方法的参数

yield语句本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数会被当作上一个yield语句的返回值。这个功能有很重要的语法意义，Generator函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过next方法的参数，就有办法在Generator函数开始运行之后，继续向函数体内部注入值。

    function* foo(x) {
      var y = 2 * (yield (x + 1));
      var z = yield (y / 3);
      return (x + y + z);
    }
    
    var it = foo(5);
    
    it.next()
    // { value:6, done:false }
    it.next(12)
    // { value:8, done:false }
    it.next(13)
    // { value:42, done:true }
    先把这个例子理解了，再说
    
