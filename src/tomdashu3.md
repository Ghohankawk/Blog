---
title: 汤姆大叔的深入理解JavaScript读后感三（设计模式篇）
---

 
# 今天要分享的内容是tom大叔的JavaScript系列的读书观后感3
挑选大叔里面，个人自己喜欢的设计模式实现

# 第二十五部分，设计模式之单例模式
在传统开发工程师眼里，单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。在JavaScript里，单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。

```angularjs
function Universe() {

    // 缓存的实例
    var instance = this;

    // 其它内容
    this.start_time = 0;
    this.bang = "Big";

    // 重写构造函数
    Universe = function () {
        return instance;
    };
}

// 测试
var uni = new Universe();
var uni2 = new Universe();
uni.bang = "123";
console.log(uni === uni2); // true
console.log(uni2.bang); // 123

```
这个设计模式，简单明了，通过this进行缓存，剩下了去判断不存在的麻烦

# 第二十六部分，设计模式之构造函数模式

构造函数用于创建特定类型的对象——不仅声明了使用的对象，构造函数还可以接受参数以便第一次创建对象的时候设置对象的成员值。你可以自定义自己的构造函数，然后在里面声明自定义类型对象的属性或方法。
```angularjs
function Car(model, year, miles) {
    this.model = model;
    this.year = year;
    this.miles = miles;
}

/*
注意：这里我们使用了Object.prototype.方法名，而不是Object.prototype
主要是用来避免重写定义原型prototype对象
*/
Car.prototype.output= function () {
    return this.model + "走了" + this.miles + "公里";
};

var tom = new Car("大叔", 2009, 20000);
var dudu = new Car("Dudu", 2010, 5000);

console.log(tom.output());
console.log(dudu.output());

```

# 第二十七部分，设计模式之建造者模式
建造者模式可以将一个复杂对象的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。也就是说如果我们用了建造者模式，那么用户就需要指定需要建造的类型就可以得到它们，而具体建造的过程和细节就不需要知道了。
```angularjs

function getBeerById(id, callback) {
    // 使用ID来请求数据，然后返回数据.
    asyncRequest('GET', 'beer.uri?id=' + id, function (resp) {
        // callback调用 response
        callback(resp.responseText);
    });
}

var el = document.querySelector('#test');
el.addEventListener('click', getBeerByIdBridge, false);

function getBeerByIdBridge(e) {
    getBeerById(this.id, function (beer) {
        console.log('Requested Beer: ' + beer);
    });
}

```
建造者模式主要用于“分步骤构建一个复杂的对象”，在这其中“分步骤”是一个稳定的算法，而复杂对象的各个部分则经常变化，其优点是：建造者模式的“加工工艺”是暴露的，这样使得建造者模式更加灵活，并且建造者模式解耦了组装过程和创建具体部件，使得我们不用去关心每个部件是如何组装的。
也就是说，上面的函数中，getBeerById函数是稳定的，写了一个构建过程，而具体的构建细节由另外一个回调函数决定

其实，我觉得，这个部分，不属于java里面的建造者模式，更是一种模板模式，可能个人理解不同吧，反正大叔的意思，就是这种样子
# 第二十八部分，设计模式之工厂模式
    什么时候使用工厂模式
    
    以下几种情景下工厂模式特别有用：
    
    对象的构建十分复杂
    需要依赖具体环境创建不同实例
    处理大量具有相同属性的小对象
```angularjs
var productManager = {};

productManager.createProductA = function () {
    console.log('ProductA');
}

productManager.createProductB = function () {
    console.log('ProductB');
}
        
productManager.factory = function (typeType) {
    return new productManager[typeType];
}

productManager.factory("createProductA");

```
一句话，就是根据不同的类型，调用不同的子类，进行实例化一个对象

# 第二十九部分，设计模式之装饰者模式

装饰者用于通过重载方法的形式添加新功能，该模式可以在被装饰者前面或者后面加上自己的行为以达到特定的目的。

```angularjs
var tree = {};
tree.decorate = function () {
    console.log('Make sure the tree won\'t fall');
};

tree.getDecorator = function (deco) {
    tree[deco].prototype = this;
    return new tree[deco];
};

tree.RedBalls = function () {
    this.decorate = function () {
        this.RedBalls.prototype.decorate(); // 第7步：先执行原型（这时候是Angel了）的decorate方法
        console.log('Put on some red balls'); // 第8步 再输出 red
        // 将这2步作为RedBalls的decorate方法
    }
};

tree.BlueBalls = function () {
    this.decorate = function () {
        this.BlueBalls.prototype.decorate(); // 第1步：先执行原型的decorate方法，也就是tree.decorate()
        console.log('Add blue balls'); // 第2步 再输出blue
        // 将这2步作为BlueBalls的decorate方法
    }
};

tree.Angel = function () {
    this.decorate = function () {
        this.Angel.prototype.decorate(); // 第4步：先执行原型（这时候是BlueBalls了）的decorate方法
        console.log('An angel on the top'); // 第5步 再输出angel
        // 将这2步作为Angel的decorate方法
    }
};

tree = tree.getDecorator('BlueBalls'); // 第3步：将BlueBalls对象赋给tree，这时候父原型里的getDecorator依然可用
tree = tree.getDecorator('Angel'); // 第6步：将Angel对象赋给tree，这时候父原型的父原型里的getDecorator依然可用
tree = tree.getDecorator('RedBalls'); // 第9步：将RedBalls对象赋给tree

tree.decorate(); // 第10步：执行RedBalls对象的decorate方法

```
这段代码，主要是在于，
        tree.getDecorator 这段函数设计精妙
        他让tree接受一个新的函数，而每次都用这个新函数去替代之前的函数，并且同时让原型指向前一个，实现了一个
        继承关系，比如A-》B-》C，最开始tree是A，然后，放入一个，就变成B，之后为C
        
        
# 第三十部分，设计模式之外观模式
外观模式不仅简化类中的接口，而且对接口与调用者也进行了解耦。外观模式经常被认为开发者必备，它可以将一些复杂操作封装起来，并创建一个简单的接口用于调用。

外观模式经常被用于JavaScript类库里，通过它封装一些接口用于兼容多浏览器，外观模式可以让我们间接调用子系统，从而避免因直接访问子系统而产生不必要的错误。

外观模式的优势是易于使用，而且本身也比较轻量级。但也有缺点 外观模式被开发者连续使用时会产生一定的性能问题，因为在每次调用时都要检测功能的可用性。

```angularjs
var addMyEvent = function (el, ev, fn) {
    if (el.addEventListener) {
        el.addEventListener(ev, fn, false);
    } else if (el.attachEvent) {
        el.attachEvent('on' + ev, fn);
    } else {
        el['on' + ev] = fn;
    }
}; 
再来一个简单的例子，说白了就是用一个接口封装其它的接口：
var mobileEvent = {
    // ...
    stop: function (e) {
        e.preventDefault();
        e.stopPropagation();
    }
    // ...
};

```
# 第三十一部分，设计模式之代理模式
代理，顾名思义就是帮助别人做事
```angularjs
// 先声明美女对象
var girl = function (name) {
    this.name = name;
};

// 这是dudu
var dudu = function (girl) {
    this.girl = girl;
    this.sendGift = function (gift) {
        alert("Hi " + girl.name + ", dudu送你一个礼物：" + gift);
    }
};

// 大叔是代理
var proxyTom = function (girl) {
    this.girl = girl;
    this.sendGift = function (gift) {
        (new dudu(girl)).sendGift(gift); // 替dudu送花咯
    }
};

说的简单一点，就是代理新类，必须包括被代理的对象，也就是可以主动new

```

# 第三十二部分，设计模式之观察者模式
观察者模式又叫发布订阅模式（Publish/Subscribe），它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得它们能够自动更新自己。

```angularjs
jQuery版本

根据jQuery1.7版新增的on/off功能，我们也可以定义jQuery版的观察者：

(function ($) {

    var o = $({});

    $.subscribe = function () {
        o.on.apply(o, arguments);
    };

    $.unsubscribe = function () {
        o.off.apply(o, arguments);
    };

    $.publish = function () {
        o.trigger.apply(o, arguments);
    };

} (jQuery));

```

# 第三十三部分，设计模式之策略模式
策略模式定义了算法家族，分别封装起来，让他们之间可以互相替换，此模式让算法的变化不会影响到使用算法的客户。

```angularjs
正常的模式，缺点就是，下次继续增加case进行判断
        validator = {
            validate: function (value, type) {
                switch (type) {
                    case 'isNonEmpty ':
                        {
                            return true; // NonEmpty 验证结果
                        }
                    case 'isNumber ':
                        {
                            return true; // Number 验证结果
                            break;
                        }
                    case 'isAlphaNum ':
                        {
                            return true; // AlphaNum 验证结果
                        }
                    default:
                        {
                            return true;
                        }
                }
            }
        };
        //  测试
        alert(validator.validate("123", "isNonEmpty"));

策略模式

var validator = {

    // 所有可以的验证规则处理类存放的地方，后面会单独定义
    types: {},

    // 验证类型所对应的错误消息
    messages: [],

    // 当然需要使用的验证类型
    config: {},

    // 暴露的公开验证方法
    // 传入的参数是 key => value对
    validate: function (data) {

        var i, msg, type, checker, result_ok;

        // 清空所有的错误信息
        this.messages = [];

        for (i in data) {
            if (data.hasOwnProperty(i)) {

                type = this.config[i];  // 根据key查询是否有存在的验证规则
                checker = this.types[type]; // 获取验证规则的验证类

                if (!type) {
                    continue; // 如果验证规则不存在，则不处理
                }
                if (!checker) { // 如果验证规则类不存在，抛出异常
                    throw {
                        name: "ValidationError",
                        message: "No handler to validate type " + type
                    };
                }

                result_ok = checker.validate(data[i]); // 使用查到到的单个验证类进行验证
                if (!result_ok) {
                    msg = "Invalid value for *" + i + "*, " + checker.instructions;
                    this.messages.push(msg);
                }
            }
        }
        return this.hasErrors();
    },

    // helper
    hasErrors: function () {
        return this.messages.length !== 0;
    }
};

然后剩下的工作，就是定义types里存放的各种验证类了，我们这里只举几个例子：

// 验证给定的值是否不为空
validator.types.isNonEmpty = {
    validate: function (value) {
        return value !== "";
    },
    instructions: "传入的值不能为空"
};

// 验证给定的值是否是数字
validator.types.isNumber = {
    validate: function (value) {
        return !isNaN(value);
    },
    instructions: "传入的值只能是合法的数字，例如：1, 3.14 or 2010"
};

// 验证给定的值是否只是字母或数字
validator.types.isAlphaNum = {
    validate: function (value) {
        return !/[^a-z0-9]/i.test(value);
    },
    instructions: "传入的值只能保护字母和数字，不能包含特殊字符"
};

以配置替代之前的写死的那种方式，可以理解为开闭原则，不修改之前的代码

```

# 第三十四部分，设计模式之命令模式
命令模式(Command)的定义是：用于将一个请求封装成一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及执行可撤销的操作。也就是说改模式旨在将函数的调用、请求和操作封装成一个单一的对象，然后对这个对象进行一系列的处理。此外，可以通过调用实现具体函数的对象来解耦命令对象与接收对象。

```angularjs
$(function () {

    var CarManager = {

        // 请求信息
        requestInfo: function (model, id) {
            return 'The information for ' + model +
        ' with ID ' + id + ' is foobar';
        },

        // 购买汽车
        buyVehicle: function (model, id) {
            return 'You have successfully purchased Item '
        + id + ', a ' + model;
        },

        // 组织view
        arrangeViewing: function (model, id) {
            return 'You have successfully booked a viewing of '
        + model + ' ( ' + id + ' ) ';
        }
    };
})();

CarManager.execute = function (command) {
    return CarManager[command.request](command.model, command.carID);
};

CarManager.execute({ request: "arrangeViewing", model: 'Ferrari', carID: '145523' });

之前可能没明白命令模式，现在看这个例子，就很简单了，
就是，我不具体的调用函数，而是改成一个命令，通过一个弄命令函数，传入不同的指令，实现方法的调用
大叔的意思是说，不推荐使用，可以直接调用，就不用这么麻烦，前天是系统不复杂的时候
```
# 第三十五部分，设计模式之迭代器模式
迭代器模式(Iterator)：提供一种方法顺序一个聚合对象中各个元素，而又不暴露该对象内部表示。

迭代器的几个特点是：

访问一个聚合对象的内容而无需暴露它的内部表示。
为遍历不同的集合结构提供一个统一的接口，从而支持同样的算法在不同的集合结构上进行操作。
遍历的同时更改迭代器所在的集合结构可能会导致问题（比如C#的foreach里不允许修改item）。

```angularjs
jQuery里一个非常有名的迭代器就是$.each方法，通过each我们可以传入额外的function，然后来对所有的item项进行迭代操作，例如：

$.each(['dudu', 'dudu', '酸奶小妹', '那个MM'], function (index, value) {
    console.log(index + ': ' + value);
});
//或者
$('li').each(function (index) {
    console.log(index + ': ' + $(this).text());
});

```

# 第三十六部分，设计模式之中介者模式
软件开发中，中介者是一个行为设计模式，通过提供一个统一的接口让系统的不同部分进行通信。一般，如果系统有很多子模块需要直接沟通，都要创建一个中央控制点让其各模块通过该中央控制点进行交互。中介者模式可以让这些子模块不需要直接沟通，而达到进行解耦的目的。

打个比方，平时常见的机场交通控制系统，塔台就是中介者，它控制着飞机（子模块）的起飞和降落，因为所有的沟通都是从飞机向塔台汇报来完成的，而不是飞机之前相互沟通。中央控制系统就是该系统的关键，也就是软件设计中扮演的中介者角色。

```angularjs
// 如下代码是伪代码，请不要过分在意代码
// 这里app命名空间就相当于扮演中介者的角色
var app = app || {};
 
// 通过app中介者来进行Ajax请求
app.sendRequest = function ( options ) {
    return $.ajax($.extend({}, options);
}
 
// 请求URL以后，展示View
app.populateView = function( url, view ){
  $.when(app.sendRequest({url: url, method: 'GET'})
     .then(function(){
         //显示内容
     });
}
 
// 清空内容
app.resetView = function( view ){
   view.html('');
}
之前没有分清，中介和代理，其实区别是明显，中介，里面参与的是多个人，他共同维护这些人，而且还都彼此认识switch 
而代理，只是简单的处理一个人而已，参与者一

```

另外，由于中介者模式把交互复杂性变成了中介者本身的复杂性，所以说中介者对象会比其它任何对象都复杂。

# 第三十七部分，设计模式之享元模式
Flyweight中有两个重要概念--内部状态intrinsic和外部状态extrinsic之分，内部状态就是在对象里通过内部方法管理，而外部信息可以在通过外部删除或者保存。

说白点,就是先捏一个的原始模型，然后随着不同场合和环境,再产生各具特征的具体模型，很显然,在这里需要产生不同的新对象，所以Flyweight模式中常出现Factory模式，

```angularjs
var Book = function( id, title, author, genre, pageCount,publisherID, ISBN, checkoutDate, checkoutMember, dueReturnDate,availability ){
   this.id = id;
   this.title = title;
   this.author = author;
   this.genre = genre;
   this.pageCount = pageCount;
   this.publisherID = publisherID;
   this.ISBN = ISBN;
   this.checkoutDate = checkoutDate;
   this.checkoutMember = checkoutMember;
   this.dueReturnDate = dueReturnDate;
   this.availability = availability;
};
Book.prototype = {
   getTitle:function(){
       return this.title;
   },
   getAuthor: function(){
       return this.author;
   },
   getISBN: function(){
       return this.ISBN;
   },
/*其它get方法在这里就不显示了*/


上面这种函数，最大的问题，就是后面的那4个参数，就算是同一本书，时间不一样，都算成是不一样的
其实，你是想知道，书这个东西，而不是书*变量个，这样内存中有很多没用的例子


$('div').bind('click', function(){
 console.log('You clicked: ' + $(this).attr('id'));
});
// 上面的代码，要避免使用，避免再次对DOM元素进行生成jQuery对象，因为这里可以直接使用DOM元素自身了。
$('div').bind('click', function(){
 console.log('You clicked: ' + this.id);
});

其实，很简单，就是一个排列组合的问题
比如，创建文件夹来举例吧，你是创建book1到book10,10个文件夹，还是，创建book文件夹，里面放1-10个子文件夹，一个11个文件夹

第一种方案字符串是，5*9+6=51
第二种方案是4+9+2=15
这节省空间是明显的，别问啥意思

```

# 第三十八部分，设计模式之职责链模式
职责链模式（Chain of responsibility）是使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系。将这个对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理他为止。

也就是说，请求以后，从第一个对象开始，链中收到请求的对象要么亲自处理它，要么转发给链中的下一个候选者。提交请求的对象并不明确知道哪一个对象将会处理它——也就是该请求有一个隐式的接受者（implicit receiver）。根据运行时刻，任一候选者都可以响应相应的请求，候选者的数目是任意的，你可以在运行时刻决定哪些候选者参与到链中。

```angularjs
var NO_TOPIC = -1;
var Topic;

function Handler(s, t) {
    this.successor = s || null;
    this.topic = t || 0;
}

Handler.prototype = {
    handle: function () {
        if (this.successor) {
            this.successor.handle()
        }
    },
    has: function () {
        return this.topic != NO_TOPIC;
    }
};


var app = new Handler({
    handle: function () {
        console.log('app handle');
    }
}, 3);

var dialog = new Handler(app, 1);
dialog.handle = function () {
    console.log('dialog before ...')
    // 这里做具体的处理操作
    Handler.prototype.handle.call(this); //继续往上走
    console.log('dialog after ...')
};

var button = new Handler(dialog, 2);
button.handle = function () {
    console.log('button before ...')
    // 这里做具体的处理操作
    Handler.prototype.handle.call(this);
    console.log('button after ...')
};

button.handle();

通过代码的运行结果我们可以看出，如果想先自身处理，然后再调用继任者处理的话，就在末尾执行Handler.prototype.handle.call(this);代码，如果想先处理继任者的代码，就在开头执行Handler.prototype.handle.call(this);代码。

prototype用的好，就得理解this和prototype

```

# 第三十九部分，设计模式之适配器模式

适配器模式（Adapter）是将一个类（对象）的接口（方法或属性）转化成客户希望的另外一个接口（方法或属性），适配器模式使得原本由于接口不兼容而不能一起工作的那些类（对象）可以一些工作。速成包装器（wrapper）。

```angularjs
//鸭子
var Duck = function(){

};
Duck.prototype.fly = function(){
throw new Error("该方法必须被重写!");
};
Duck.prototype.quack = function(){
throw new Error("该方法必须被重写!");
}

//火鸡
var Turkey = function(){

};
Turkey.prototype.fly = function(){
    throw new Error(" 该方法必须被重写 !");
};
Turkey.prototype.gobble = function(){
    throw new Error(" 该方法必须被重写 !");
};



//鸭子
var MallardDuck = function () {
    Duck.apply(this);
};
MallardDuck.prototype = new Duck(); //原型是Duck
MallardDuck.prototype.fly = function () {
    console.log("可以飞翔很长的距离!");
};
MallardDuck.prototype.quack = function () {
    console.log("嘎嘎！嘎嘎！");
};

//火鸡
var WildTurkey = function () {
    Turkey.apply(this);
};
WildTurkey.prototype = new Turkey(); //原型是Turkey
WildTurkey.prototype.fly = function () {
    console.log("飞翔的距离貌似有点短!");
};
WildTurkey.prototype.gobble = function () {
    console.log("咯咯！咯咯！");
};

```
    那合适使用适配器模式好呢？如果有以下情况出现时，建议使用：
    
    使用一个已经存在的对象，但其方法或属性接口不符合你的要求；
    你想创建一个可复用的对象，该对象可以与其它不相关的对象或不可见对象（即接口方法或属性不兼容的对象）协同工作；
    想使用已经存在的对象，但是不能对每一个都进行原型继承以匹配它的接口。对象适配器可以适配它的父对象接口方法或属性。
    另外，适配器模式和其它几个模式可能容易让人迷惑，这里说一下大概的区别：
    
    适配器和桥接模式虽然类似，但桥接的出发点不同，桥接的目的是将接口部分和实现部分分离，从而对他们可以更为容易也相对独立的加以改变。而适配器则意味着改变一个已有对象的接口。
    装饰者模式增强了其它对象的功能而同时又不改变它的接口，因此它对应程序的透明性比适配器要好，其结果是装饰者支持递归组合，而纯粹使用适配器则是不可能的。
    代理模式在不改变它的接口的条件下，为另外一个对象定义了一个代理。

# 第四十部分，设计模式之组合模式

组合模式（Composite）将对象组合成树形结构以表示“部分-整体”的层次结构，组合模式使得用户对单个对象和组合对象的使用具有一致性。

常见的场景有asp.net里的控件机制（即control里可以包含子control，可以递归操作、添加、删除子control），类似的还有DOM的机制，一个DOM节点可以包含子节点，不管是父节点还是子节点都有添加、删除、遍历子节点的通用功能。所以说组合模式的关键是要有一个抽象类，它既可以表示子元素，又可以表示父元素。
```angularjs
var MenuComponent = function () {
};
MenuComponent.prototype.getName = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.getDescription = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.getPrice = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.isVegetarian = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.print = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.add = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.remove = function () {
    throw new Error("该方法必须重写!");
};
MenuComponent.prototype.getChild = function () {
    throw new Error("该方法必须重写!");
};

var MenuItem = function (sName, sDescription, bVegetarian, nPrice) {
    MenuComponent.apply(this);
    this.sName = sName;
    this.sDescription = sDescription;
    this.bVegetarian = bVegetarian;
    this.nPrice = nPrice;
};
MenuItem.prototype = new MenuComponent();
MenuItem.prototype.getName = function () {
    return this.sName;
};
MenuItem.prototype.getDescription = function () {
    return this.sDescription;
};
MenuItem.prototype.getPrice = function () {
    return this.nPrice;
};
MenuItem.prototype.isVegetarian = function () {
    return this.bVegetarian;
};
MenuItem.prototype.print = function () {
    console.log(this.getName() + ": " + this.getDescription() + ", " + this.getPrice() + "euros");
};



var Menu = function (sName, sDescription) {
    MenuComponent.apply(this);
    this.aMenuComponents = [];
    this.sName = sName;
    this.sDescription = sDescription;
    this.createIterator = function () {
        throw new Error("This method must be overwritten!");
    };
};
Menu.prototype = new MenuComponent();
Menu.prototype.add = function (oMenuComponent) {
    // 添加子菜品
    this.aMenuComponents.push(oMenuComponent);
};
Menu.prototype.remove = function (oMenuComponent) {
    // 删除子菜品
    var aMenuItems = [];
    var nMenuItem = 0;
    var nLenMenuItems = this.aMenuComponents.length;
    var oItem = null;

    for (; nMenuItem < nLenMenuItems; ) {
        oItem = this.aMenuComponents[nMenuItem];
        if (oItem !== oMenuComponent) {
            aMenuItems.push(oItem);
        }
        nMenuItem = nMenuItem + 1;
    }
    this.aMenuComponents = aMenuItems;
};
Menu.prototype.getChild = function (nIndex) {
    //获取指定的子菜品
    return this.aMenuComponents[nIndex];
};
Menu.prototype.getName = function () {
    return this.sName;
};
Menu.prototype.getDescription = function () {
    return this.sDescription;
};
Menu.prototype.print = function () {
    // 打印当前菜品以及所有的子菜品
    console.log(this.getName() + ": " + this.getDescription());
    console.log("--------------------------------------------");

    var nMenuComponent = 0;
    var nLenMenuComponents = this.aMenuComponents.length;
    var oMenuComponent = null;

    for (; nMenuComponent < nLenMenuComponents; ) {
        oMenuComponent = this.aMenuComponents[nMenuComponent];
        oMenuComponent.print();
        nMenuComponent = nMenuComponent + 1;
    }
};



var DinnerMenu = function () {
    Menu.apply(this);
};
DinnerMenu.prototype = new Menu();

var CafeMenu = function () {
    Menu.apply(this);
};
CafeMenu.prototype = new Menu();

var PancakeHouseMenu = function () {
    Menu.apply(this);
};
PancakeHouseMenu.prototype = new Menu();


var Mattress = function (aMenus) {
    this.aMenus = aMenus;
};
Mattress.prototype.printMenu = function () {
    this.aMenus.print();
};

组合模式，特点就是抽象一个父子公用的类，比如，add，remove这些getChild之类的，必须提供


```

# 第四十一部分，设计模式之模板方法

模板方法（TemplateMethod）定义了一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

模板方法是一种代码复用的基本技术，在类库中尤为重要，因为他们提取了类库中的公共行为。模板方法导致一种反向的控制结构，这种结构就是传说中的“好莱坞法则”，即“别找找我们，我们找你


```angularjs
var CaffeineBeverage = function () {

};
CaffeineBeverage.prototype.prepareRecipe = function () {
    this.boilWater();
    this.brew();
    this.pourOnCup();
    if (this.customerWantsCondiments()) {
        // 如果可以想加小料，就加上
 this.addCondiments();
    }
};
CaffeineBeverage.prototype.boilWater = function () {
    console.log("将水烧开!");
};
CaffeineBeverage.prototype.pourOnCup = function () {
    console.log("将饮料到再杯子里!");
};
CaffeineBeverage.prototype.brew = function () {
    throw new Error("该方法必须重写!");
};
CaffeineBeverage.prototype.addCondiments = function () {
    throw new Error("该方法必须重写!");
};
// 默认加上小料
CaffeineBeverage.prototype.customerWantsCondiments = function () {
    return true;
};


// 冲咖啡
var Coffee = function () {
    CaffeineBeverage.apply(this);
};
Coffee.prototype = new CaffeineBeverage();
Coffee.prototype.brew = function () {
    console.log("从咖啡机想咖啡倒进去!");
};
Coffee.prototype.addCondiments = function () {
    console.log("添加糖和牛奶");
};
Coffee.prototype.customerWantsCondiments = function () {
    return confirm("你想添加糖和牛奶吗？");
};

//冲茶叶
var Tea = function () {
    CaffeineBeverage.apply(this);
};
Tea.prototype = new CaffeineBeverage();
Tea.prototype.brew = function () {
    console.log("泡茶叶!");
};
Tea.prototype.addCondiments = function () {
    console.log("添加柠檬!");
};
Tea.prototype.customerWantsCondiments = function () {
    return confirm("你想添加柠檬嘛？");
};

```

模板方法应用于下列情况：

一次性实现一个算法的不变的部分，并将可变的行为留给子类来实现
各子类中公共的行为应被提取出来并集中到一个公共父类中的避免代码重复，不同之处分离为新的操作，最后，用一个钓鱼这些新操作的模板方法来替换这些不同的代码
控制子类扩展，模板方法只在特定点调用“hook”操作，这样就允许在这些点进行扩展

# 第四十二部分，设计模式之原型模式
原型模式（prototype）是指用原型实例指向创建对象的种类，并且通过拷贝这些原型创建新的对象。

```angularjs

var vehicle = {
    getModel: function () {
        console.log('车辆的模具是：' + this.model);
    }
};

var car = Object.create(vehicle, {
    'id': {
        value: MY_GLOBAL.nextId(),
        enumerable: true // 默认writable:false, configurable:false
 },
    'model': {
        value: '福特',
        enumerable: true
    }
});

说的简单一点，其实就是特指，JavaScript里面的原型模式

```

# 第四十三部分，设计模式之状态模式
状态模式（State）允许一个对象在其内部状态改变的时候改变它的行为，对象看起来似乎修改了它的类。
这个地方内容代码太长了
我自己理解，总结一下

1. 比如有A，B，C三种状态
2. 那么每种状态必须对应了一组动作，比如开和关，那么3*2=6种状态
3. 三种状态，有种转换关系，状态的改变，同样的开，代表的意思不一样
4. 比如顺序，A-》B-》C-》A，那么同样的，动作，开随着状态的改变，意义发生变化
# 第四十四部分，设计模式之桥接模式


桥接模式（Bridge）将抽象部分与它的实现部分分离，使它们都可以独立地变化。

```angularjs
function getBeerById(id, callback) {
// 通过ID发送请求，然后返回数据
asyncRequest('GET', 'beer.uri?id=' + id, function(resp) {
// callback response
callback(resp.responseText);
});
}
桥接，看大叔的例子，还真是没懂，回去看了我之前写过的设计模式，才慢慢理解switch 
桥接=代理+策略
为什么这么说？
1. jdbc是桥接，怎么用，是不是每次主动输入一个mysql.jdbc这么个字符串？
2. 代理模式很明白的只代理一个类，内部写死了
3. 而策略模式，是有一组策略，客户端不需要具体使用了什么策略
4. 那么桥接，就是你要明确的告诉这个代理，要用哪种策略
5. 因此，你需要清楚，你是要给谁桥？
6. 因此上面这个例子，callback，就是你自己传递尽量的，按个策略

```
# 第四十五部分，代码复用模式（避免篇）
写了6篇，应该让我们避免的模式，那么不要去话时间记忆不要用的，重点在下篇
# 第四十六部分，代码复用模式（推荐篇）
只需记住，大叔推荐，让我们记住的模式，就可以了

    模式1：原型继承
    
    原型继承是让父对象作为子对象的原型，从而达到继承的目的：
    function object(o) {
        function F() {
        }
    
        F.prototype = o;
        return new F();
    }
    
    // 要继承的父对象
    var parent = {
        name: "Papa"
    };
    
    // 新对象
    var child = object(parent);
    
    // 测试
    console.log(child.name); // "Papa"
    
    
    // 父构造函数
    function Person() {
        // an "own" property
        this.name = "Adam";
    }
    // 给原型添加新属性
    Person.prototype.getName = function () {
        return this.name;
    };
    // 创建新person
    var papa = new Person();
    // 继承
    var kid = object(papa);
    console.log(kid.getName()); // "Adam"
    
    
    // 父构造函数
    function Person() {
        // an "own" property
        this.name = "Adam";
    }
    // 给原型添加新属性
    Person.prototype.getName = function () {
        return this.name;
    };
    // 继承
    var kid = object(Person.prototype);
    console.log(typeof kid.getName); // "function",因为是在原型里定义的
    console.log(typeof kid.name); // "undefined", 因为只继承了原型
    记住一句话，prototype是一个对象
    
    function object(o) {
        function F() {
        }
    
        F.prototype = o;
        return new F();
    }
    
    // 要继承的父对象
    var parent = {
        name: "Papa"
    };
    
    // 新对象
    var child = object(parent);
    
    // 测试
    console.log(child.name); // "Papa"
    
    
    // 父构造函数
    function Person() {
        // an "own" property
        this.name = "Adam";
    }
    // 给原型添加新属性
    Person.prototype.getName = function () {
        return this.name;
    };
    // 创建新person
    var papa = new Person();
    // 继承
    var kid = object(papa);
    console.log(kid.getName()); // "Adam"
    
    
    // 父构造函数
    function Person() {
        // an "own" property
        this.name = "Adam";
    }
    // 给原型添加新属性
    Person.prototype.getName = function () {
        return this.name;
    };
    // 继承
    var kid = object(Person.prototype);
    console.log(typeof kid.getName); // "function",因为是在原型里定义的
    console.log(typeof kid.name); // "undefined", 因为只继承了原型
    
    模式2：复制所有属性进行继承
    
    这种方式的继承就是将父对象里所有的属性都复制到子对象上，一般子对象可以使用父对象的数据。
    
    先来看一个浅拷贝的例子：
    
    /* 浅拷贝 */
    function extend(parent, child) {
        var i;
        child = child || {};
        for (i in parent) {
            if (parent.hasOwnProperty(i)) {
                child[i] = parent[i];
            }
        }
        return child;
    }
    
    var dad = { name: "Adam" };
    var kid = extend(dad);
    console.log(kid.name); // "Adam"
    
    var dad = {
        counts: [1, 2, 3],
        reads: { paper: true }
    };
    var kid = extend(dad);
    kid.counts.push(4);
    console.log(dad.counts.toString()); // "1,2,3,4"
    console.log(dad.reads === kid.reads); // true
    代码的最后一行，你可以发现dad和kid的reads是一样的，也就是他们使用的是同一个引用，这也就是浅拷贝带来的问题。
    
    
    /* 深拷贝 */
    function extendDeep(parent, child) {
        var i,
            toStr = Object.prototype.toString,
            astr = "[object Array]";
    
        child = child || {};
    
        for (i in parent) {
            if (parent.hasOwnProperty(i)) {
                if (typeof parent[i] === 'object') {
                    child[i] = (toStr.call(parent[i]) === astr) ? [] : {};
                    extendDeep(parent[i], child[i]);
                } else {
                    child[i] = parent[i];
                }
            }
        }
        return child;
    }
    
    var dad = {
        counts: [1, 2, 3],
        reads: { paper: true }
    };
    var kid = extendDeep(dad);
    
    kid.counts.push(4);
    console.log(kid.counts.toString()); // "1,2,3,4"
    console.log(dad.counts.toString()); // "1,2,3"
    
    console.log(dad.reads === kid.reads); // false
    kid.reads.paper = false;
    
    这个地方就是厉害在，上面的递归那部分，当包含复合部分就会重新递归调用一次 
    
    
    模式3：混合（mix-in）
    
    混入就是将一个对象的一个或多个（或全部）属性（或方法）复制到另外一个对象，我们举一个例子：
    
    function mix() {
        var arg, prop, child = {};
        for (arg = 0; arg < arguments.length; arg += 1) {
            for (prop in arguments[arg]) {
                if (arguments[arg].hasOwnProperty(prop)) {
                    child[prop] = arguments[arg][prop];
                }
            }
        }
        return child;
    }
    
    var cake = mix(
                    { eggs: 2, large: true },
                    { butter: 1, salted: true },
                    { flour: '3 cups' },
                    { sugar: 'sure!' }
                    );
    
    console.dir(cake);
    
    
    模式4：借用方法
    
    一个对象借用另外一个对象的一个或两个方法，而这两个对象之间不会有什么直接联系。不用多解释，直接用代码解释吧：
    
    var one = {
        name: 'object',
        say: function (greet) {
            return greet + ', ' + this.name;
        }
    };
    
    // 测试
    console.log(one.say('hi')); // "hi, object"
    
    var two = {
        name: 'another object'
    };
    
    console.log(one.say.apply(two, ['hello'])); // "hello, another object"
    
    // 将say赋值给一个变量，this将指向到全局变量
    var say = one.say;
    console.log(say('hoho')); // "hoho, undefined"
    
    // 传入一个回调函数callback
    var yetanother = {
        name: 'Yet another object',
        method: function (callback) {
            return callback('Hola');
        }
    };
    console.log(yetanother.method(one.say)); // "Holla, undefined"
    
    function bind(o, m) {
        return function () {
            return m.apply(o, [].slice.call(arguments));
        };
    }
    
    var twosay = bind(two, one.say);
    console.log(twosay('yo')); // "yo, another object"
    
    
    // ECMAScript 5给Function.prototype添加了一个bind()方法，以便很容易使用apply()和call()。
    
    if (typeof Function.prototype.bind === 'undefined') {
        Function.prototype.bind = function (thisArg) {
            var fn = this,
    slice = Array.prototype.slice,
    args = slice.call(arguments, 1);
            return function () {
                return fn.apply(thisArg, args.concat(slice.call(arguments)));
            };
        };
    }
    
    var twosay2 = one.say.bind(two);
    console.log(twosay2('Bonjour')); // "Bonjour, another object"
    
    var twosay3 = one.say.bind(two, 'Enchanté');
    console.log(twosay3()); // "Enchanté, another object"
    
    
# 第四十七部分，对象创建模式（上篇）
    模式1：命名空间（namespace）
    
    命名空间可以减少全局命名所需的数量，避免命名冲突或过度。一般我们在进行对象层级定义的时候，经常是这样的：
    
    var app = app || {};
    app.moduleA = app.moduleA || {};
    
    // 更简洁的方式
    var MYAPP = MYAPP || {};


    模式2：定义依赖
    
    有时候你的一个模块或者函数可能要引用第三方的一些模块或者工具，这时候最好将这些依赖模块在刚开始的时候就定义好，以便以后可以很方便地替换掉。
    
    var myFunction = function () {
        // 依赖模块
        var event = YAHOO.util.Event,
            dom = YAHOO.util.dom;
    
        // 其它函数后面的代码里使用局部变量event和dom
    };
    
    };
    模式3：私有属性和私有方法
    
    JavaScript本书不提供特定的语法来支持私有属性和私有方法，但是我们可以通过闭包来实现，代码如下：
    
    
    function Gadget() {
        // 私有对象
        var name = 'iPod';
        // 公有函数
        this.getName = function () {
            return name;
        };
    }
    
    模式4：Revelation模式
    
    也是关于隐藏私有方法的模式，和《深入理解JavaScript系列（3）：全面解析Module模式》里的Module模式有点类似，但是不是return的方式，而是在外部先声明一个变量，然后在内部给变量赋值公有方法。代码如下：
    
    var myarray;
    
    (function () {
        var astr = "[object Array]",
            toString = Object.prototype.toString;
    
        function isArray(a) {
            return toString.call(a) === astr;
        }
    
        function indexOf(haystack, needle) {
            var i = 0,
                max = haystack.length;
            for (; i < max; i += 1) {
                if (haystack[i] === needle) {
                    return i;
                }
            }
            return -1;
        }
    
        //通过赋值的方式，将上面所有的细节都隐藏了
        myarray = {
            isArray: isArray,
            indexOf: indexOf,
            inArray: indexOf
        };
    } ());
    
    模式5：链模式
    
    链模式可以你连续可以调用一个对象的方法，比如obj.add(1).remove(2).delete(4).add(2)这样的形式，其实现思路非常简单，就是将this原样返回。代码如下：
    
    var obj = {
        value: 1,
        increment: function () {
            this.value += 1;
            return this;
        },
        add: function (v) {
            this.value += v;
            return this;
        },
        shout: function () {
            console.log(this.value);
        }
    };

# 第四十八部分，对象创建模式（下篇）

    模式6：函数语法糖
    
    函数语法糖是为一个对象快速添加方法（函数）的扩展，这个主要是利用prototype的特性，代码比较简单，我们先来看一下实现代码：
    
    if (typeof Function.prototype.method !== "function") {
        Function.prototype.method = function (name, implementation) {
            this.prototype[name] = implementation;
            return this;
        };
    }
    模式7：对象常量
    
    对象常量是在一个对象提供set,get,ifDefined各种方法的体现，而且对于set的方法只会保留最先设置的对象，后期再设置都是无效的，已达到别人无法重载的目的。实现代码如下：
    
    var constant = (function () {
        var constants = {},
            ownProp = Object.prototype.hasOwnProperty,
        // 只允许设置这三种类型的值
            allowed = {
                string: 1,
                number: 1,
                boolean: 1
            },
            
            
    模式8：沙盒模式
    
    沙盒（Sandbox）模式即时为一个或多个模块提供单独的上下文环境，而不会影响其他模块的上下文环境，比如有个Sandbox里有3个方法event,dom,ajax，在调用其中2个组成一个环境的话，和调用三个组成的环境完全没有干扰。
    
    模式9：静态成员
    
    静态成员（Static Members）只是一个函数或对象提供的静态属性，可分为私有的和公有的，就像C#或Java里的public static和private static一样。
    
    我们先来看一下公有成员，公有成员非常简单，我们平时声明的方法，函数都是公有的，比如：
    
    // 构造函数
    var Gadget = function () {
    };
    
    // 公有静态方法
    Gadget.isShiny = function () {
        return "you bet";
    };
    
    // 原型上添加的正常方法
    Gadget.prototype.setPrice = function (price) {
        this.price = price;
    };
    
    // 调用静态方法
    console.log(Gadget.isShiny()); // "you bet"
    
    // 创建实例，然后调用方法
    var iphone = new Gadget();
    iphone.setPrice(500);
    
    
    而私有静态成员，我们可以利用其闭包特性去实现，以下是两种实现方式。
    
    第一种实现方式：
    
    var Gadget = (function () {
        // 静态变量/属性
        var counter = 0;
    
        // 闭包返回构造函数的新实现
        return function () {
            console.log(counter += 1);
        };
    } ()); // 立即执行
    
    var g1 = new Gadget(); // logs 1
    var g2 = new Gadget(); // logs 2
    var g3 = new Gadget(); // logs 3
# 第四十九部分，Function模式（上篇）
    回调函数
    
    在JavaScript中，当一个函数A作为另外一个函数B的其中一个参数时，则函数A称为回调函数，即A可以在函数B的周期内执行（开始、中间、结束时均可）。
    
    
    配置对象
    
    如果一个函数（或方法）的参数只有一个参数，并且参数为对象字面量，我们则称这种模式为配置对象模式。例如，如下代码：
    
    var conf = {
        username:"shichuan",
        first:"Chuan",
        last:"Shi"
    };
    
    
    返回函数
    
    返回函数，则是指在一个函数的返回值为另外一个函数，或者根据特定的条件灵活创建的新函数，示例代码如下：
    
    var setup = function () {
        console.log(1);
        return function () {
            console.log(2);
        };
    };
    // 调用setup 函数
    var my = setup(); // 输出 1
    my(); // 输出 2
    // 或者直接调用也可
    setup()();
    
    强调一句，这种形式的this，认为是ao，激活对象，也可以认为是null，因此是global
    
    
    偏应用
    
    忽略
    
    Currying
    
    Currying是函数式编程的一个特性，将多个参数的处理转化成单个参数的处理，类似链式调用。
    
    忽略

    

# 第五十部分，Function模式（下篇）
    立即执行的对象初始化
    
    该模式的意思是指在声明一个对象（而非函数）的时候，立即执行对象里的某一个方法来进行初始化工作，通常该模式可以用在一次性执行的代码上。
    
    ({
        // 这里你可以定义常量，设置其它值
        maxwidth: 600,
        maxheight: 400,
    
        //  当然也可以定义utility方法
        gimmeMax: function () {
            return this.maxwidth + "x" + this.maxheight;
        },
    
        // 初始化
        init: function () {
            console.log(this.gimmeMax());
            // 更多代码...
        }
    }).init();  // 这样就开始初始化咯
    
    
    分支初始化
    
    分支初始化是指在初始化的时候，根据不同的条件（场景）初始化不同的代码，也就是所谓的条件语句赋值。之前我们在做事件处理的时候，通常使用类似下面的代码：
    
    var utils = {
        addListener: function (el, type, fn) {
            if (typeof window.addEventListener === 'function') {
                el.addEventListener(type, fn, false);
            } else if (typeof document.attachEvent !== 'undefined') {
                el.attachEvent('on' + type, fn);
            } else {
                el['on' + type] = fn;
            }
        },
        removeListener: function (el, type, fn) {
        }
    };
    
    内存优化
    
    该模式主要是利用函数的属性特性来避免大量的重复计算。通常代码形式如下：
    
    var myFunc = function (param) {
        if (!myFunc.cache[param]) {
            var result = {};
            // ... 复杂操作 ...
            myFunc.cache[param] = result;
        }
        return myFunc.cache[param];
    };
    
    // cache 存储
    myFunc.cache = {};