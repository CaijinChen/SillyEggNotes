# **ECMAScript 5.1知识点总结 <二>**
---
## 0.0进阶知识点，总结参考自[网道](https://wangdoc.com/)

## 1.1面向对象

###### JavaScript 语言的对象体系，不是基于“类”的，而是基于构造函数（constructor）和原型链（prototype）

###### 构造函数与普通函数相仿，特点有两个

- 函数体内部使用了this关键字，代表了所要生成的对象实例。
- 生成对象的时候，必须使用new命令。

###### new命令

> 作用：执行构造函数，返回一个实例对象。new命令本身就可以执行构造函数，所以构造函数的后面可以带括号，也可以不带括号

    var Vehicle = function (){
      this.price = 1000;
    };
    // 此时省略new，并不会生成实例对象，this此时代表全局对象
    var v = Vehicle();
    v // undefined
    price // 1000

	//为了保证构造函数必须与new命令一起使用，一个解决办法是，构造函数内部使用严格模式，即第一行加上use strict。这样的话，一旦忘了使用new命令，直接调用构造函数就会报错
    function Fubar(foo, bar){
      'use strict';
      this._foo = foo;
      this._bar = bar;
    }
    
    Fubar()
    // TypeError: Cannot set property '_foo' of undefined
	
	//另一个解决办法，构造函数内部判断是否使用new命令，如果发现没有使用，则直接返回一个实例对象
	function Fubar(foo, bar) {
	  if (!(this instanceof Fubar)) {
	    return new Fubar(foo, bar);
	  }
	
	  this._foo = foo;
	  this._bar = bar;
	}
	
	Fubar(1, 2)._foo // 1
	(new Fubar(1, 2))._foo // 1

#### new的底层原理

###### new的底层步骤

1. 创建一个空对象，作为将要返回的对象实例。
1. 将这个空对象的原型，指向构造函数的prototype属性。
1. 将这个空对象赋值给函数内部的this关键字。
1. 开始执行构造函数内部的代码。

###### 函数内部可以使用new.target属性。如果当前函数是new命令调用，new.target指向当前函数，否则为undefined。使用这个属性，可以判断函数调用的时候，是否使用new命令

- function f() {
-   if (!new.target) {
-     throw new Error('请使用 new 命令调用！');
-   }
-   // ...
- }
- 
- f() // Uncaught Error: 请使用 new 命令调用！
    
###### Object.create() 创建实例对象

    var person1 = {
      name: '张三',
      age: 38,
      greeting: function() {
    console.log('Hi! I\'m ' + this.name + '.');
      }
    };
    
    var person2 = Object.create(person1);
    
    person2.name // 张三
    person2.greeting() // Hi! I'm 张三.

###### [this关键字](https://wangdoc.com/javascript/oop/this.html)

###### 继承

> JavaScript 继承机制的设计思想就是，原型对象的所有属性和方法，都能被实例对象共享。也就是说，如果属性和方法定义在原型上，那么所有实例对象就能共享，不仅节省了内存，还体现了实例对象之间的联系。

**JavaScript 规定，所有对象都有自己的原型对象（prototype）[由于原型对象也是对象，所以它也有自己的原型]**

> 所有对象的原型最终都可以上溯到Object.prototype，即Object构造函数的prototype属性，另外Object.prototyppe 为 null
#
> prototype对象有一个constructor属性，默认指向prototype对象所在的构造函数,由于constructor属性定义在prototype对象上面，意味着可以被所有实例对象继承
#
> 修改原型对象时，一般要同时修改constructor属性的指向
#
> instanceof运算符返回一个布尔值，表示对象是否为某个构造函数的实例

	v instanceof Vehicle
	// 等同于
	Vehicle.prototype.isPrototypeOf(v)

> [构造函数的继承](https://wangdoc.com/javascript/oop/prototype.html#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E7%9A%84%E7%BB%A7%E6%89%BF)
#
> Sub.prototype = new Super();//上面这种写法也有继承的效果，但是子类会具有父类实例的方法。有时，这可能不是我们需要的，所以不推荐使用这种写法。
#
    function Shape() {
      this.x = 0;
      this.y = 0;
    }
    
    Shape.prototype.move = function (x, y) {
      this.x += x;
      this.y += y;
      console.info('Shape moved.');
    };
    //让Rectangle继承Shape
    // 第一步，子类继承父类的实例
    function Rectangle() {
      Shape.call(this); // 调用父类构造函数
    }
    // 另一种写法
    function Rectangle() {
      this.base = Shape;
      this.base();
    }
    
    // 第二步，子类继承父类的原型
    Rectangle.prototype = Object.create(Shape.prototype);
    Rectangle.prototype.constructor = Rectangle;
    
    var rect = new Rectangle();
    
    rect instanceof Rectangle  // true
    rect instanceof Shape  // true
	
	//上面代码中，子类是整体继承父类。有时只需要单个方法的继承，这时可以采用下面的写法
    ClassB.prototype.print = function() {
      ClassA.prototype.print.call(this);
      // some code
    }

> JavaScript不支持多重继承，但可以通过的变通的方法实现继承多个对象，这种模式又称为 Mixin（混入）
#
	function M1() {
	  this.hello = 'hello';
	}
	
	function M2() {
	  this.world = 'world';
	}
	
	function S() {
	  M1.call(this);
	  M2.call(this);
	}
	
	// 继承 M1
	S.prototype = Object.create(M1.prototype);
	// 继承链上加入 M2
	Object.assign(S.prototype, M2.prototype);
	
	// 指定构造函数
	S.prototype.constructor = S;
	
	var s = new S();
	s.hello // 'hello'
	s.world // 'world'

> 使用“立即执行函数”（Immediately-Invoked Function Expression，IIFE），将相关的属性和方法封装在一个函数作用域里面，可以达到不暴露私有成员的目的。即**封装**
#
> 放大模式与宽放大模式（应对浏览器环境下模块的加载顺序不确定的情况），两个模式都是为了用于扩大模块！

	//放大模式
	var module1 = (function (mod){
	　mod.m3 = function () {
	　　//...
	　};
	　return mod;
	})(module1);
	//宽放大模式
	var module1 = (function (mod) {
	　//...
	　return mod;
	})(window.module1 || {});

> 独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。为了在模块内部调用全局变量，必须显式地将其他变量输入模块。使用立即执行函数还可以起到命名空间的作用

###### Object对象的相关方法

- Object.getPrototypeOf方法返回参数对象的原型。这是获取原型对象的标准方法。
	
		// 空对象的原型是 Object.prototype
		Object.getPrototypeOf({}) === Object.prototype // true
		
		// Object.prototype 的原型是 null
		Object.getPrototypeOf(Object.prototype) === null // true
		
		// 函数的原型是 Function.prototype
		function f() {}
		Object.getPrototypeOf(f) === Function.prototype // true
- Object.setPrototypeOf方法为参数对象设置原型，返回该参数对象。它接受两个参数，第一个是现有对象，第二个是原型对象

		var a = {};
		var b = {x: 1};
		Object.setPrototypeOf(a, b);
		
		Object.getPrototypeOf(a) === b // true
		a.x // 1
		
		//new命令可以使用Object.setPrototypeOf方法模拟。
		var F = function () {
		  this.foo = 'bar';
		};
		
		var f = new F();
		// 等同于
		var f = Object.setPrototypeOf({}, F.prototype);
		F.call(f);
- Object.create方法接受一个对象作为参数，然后以它为原型，返回一个实例对象。该实例完全继承原型对象的属性

		// 原型对象
		var A = {
		  print: function () {
		    console.log('hello');
		  }
		};
		
		// 实例对象
		var B = Object.create(A);
		
		Object.getPrototypeOf(B) === A // true
		B.print() // hello
		B.print === A.print // true
		
		//一下三种方式等价
		var obj1 = Object.create({});
		var obj2 = Object.create(Object.prototype);
		var obj3 = new Object();
- Object.prototype.isPrototypeOf方法，用来判断该对象是否为参数对象的原型

		Object.prototype.isPrototypeOf({}) // true
		Object.prototype.isPrototypeOf([]) // true
		Object.prototype.isPrototypeOf(/xyz/) // true
		Object.prototype.isPrototypeOf(Object.create(null)) // false
- Object.getOwnPropertyNames方法返回一个数组，成员是参数对象本身的所有属性的键名，不包含继承的属性键名。Object.getOwnPropertyNames方法返回所有键名，不管是否可以遍历。只获取那些可以遍历的属性，使用Object.keys方法

- Object.prototype.hasOwnProperty方法返回一个布尔值，用于判断某个属性定义在对象自身，还是定义在原型链上【hasOwnProperty方法是 JavaScript 之中唯一一个处理对象属性时，不会遍历原型链的方法】

- 实例对象的__proto__属性（前后各两个下划线），返回该对象的原型。该属性可读写【**根据语言标准，__proto__属性只有浏览器才需要部署，其他环境可以没有这个属性。它前后的两根下划线，表明它本质是一个内部属性，不应该对使用者暴露。因此，应该尽量少用这个属性，而是用Object.getPrototypeof()和Object.setPrototypeOf()，进行原型对象的读写操作**】

> 获取原型对象的方法【下面三种方法之中，前两种都不是很可靠。__proto__属性只有浏览器才需要部署，其他环境可以不部署。而obj.constructor.prototype在手动改变原型对象时，可能会失效】：

1. obj.__proto__
1. obj.constructor.prototype
1. Object.getPrototypeOf(obj)

> in运算符返回一个布尔值，表示一个对象是否具有某个属性。它不区分该属性是对象自身的属性，还是继承的属性。
#
> 获得对象的所有可遍历属性（不管是自身的还是继承的），可以使用for...in循环

	var o1 = { p1: 123 };
	
	var o2 = Object.create(o1, {
	  p2: { value: "abc", enumerable: true }
	});
	
	for (p in o2) {
	  console.info(p);
	}
	// p2
	// p1

> 对象的拷贝：

1. 确保拷贝后的对象，与原对象具有同样的原型。
1. 确保拷贝后的对象，与原对象具有同样的实例属性。

		function copyObject(orig) {
		  var copy = Object.create(Object.getPrototypeOf(orig));
		  copyOwnPropertiesFrom(copy, orig);
		  return copy;
		}
	
		function copyOwnPropertiesFrom(target, source) {
		  Object
		    .getOwnPropertyNames(source)
		    .forEach(function (propKey) {
		      var desc = Object.getOwnPropertyDescriptor(source, propKey);
		      Object.defineProperty(target, propKey, desc);
		    });
		  return target;
		}
		//另一种更简单的写法，是利用 ES2017 才引入标准的Object.getOwnPropertyDescriptors方法。
		
		function copyObject(orig) {
		  return Object.create(
		    Object.getPrototypeOf(orig),
		    Object.getOwnPropertyDescriptors(orig)
		  );
		}

---

## 1.2 'use strict'; 严格模式【用以执行个严格的js语法】

###### 严格模式是从 ES5 进入标准的，主要目的有以下几个。

- 明确禁止一些不合理、不严谨的语法，减少 JavaScript 语言的一些怪异行为。
- 增加更多报错的场合，消除代码运行的一些不安全之处，保证代码运行的安全。
- 提高编译器效率，增加运行速度。
- 为未来新版本的 JavaScript 语法做好铺垫。

###### 严格模式可以用于整个脚本（需要放置在实际执行的代码之前，否则无效），也可以只用于单个函数。

###### 正常模式下，JavaScript 语言有两种变量作用域（scope）：全局作用域和函数作用域。严格模式创设了第三种作用域：eval作用域。

###### ES6 会引入块级作用域。为了与新版本接轨，ES5 的严格模式只允许在全局作用域或函数作用域声明函数。也就是说，不允许在非函数的代码块内声明函数。

---

## [1.3异步操作](https://wangdoc.com/javascript/async/general.html)

> 单线程模型指的是，JavaScript 只在一个线程上运行。也就是说，JavaScript 同时只能执行一个任务，其他任务都必须在后面排队等待。
#
> 注意，JavaScript 只在一个线程上运行，不代表 JavaScript 引擎只有一个线程。事实上，JavaScript 引擎有多个线程，单个脚本只能在一个线程上运行（称为主线程），其他线程都是在后台配合
#
> JavaScript 运行时，除了一个正在运行的主线程，引擎还提供一个任务队列（task queue），里面是各种需要当前程序处理的异步任务。（实际上，根据异步任务的类型，存在多个任务队列。为了方便理解，这里假设只存在一个队列。）
#
> 主线程会去执行所有的同步任务。等到同步任务全部执行完，就会去看任务队列里面的异步任务。如果满足条件，那么异步任务就重新进入主线程开始执行，这时它就变成同步任务了。等到执行完，下一个异步任务再进入主线程开始执行。一旦任务队列清空，程序就结束执行。
#
> JavaScript 引擎怎么知道异步任务有没有结果，能不能进入主线程呢？答案就是引擎在不停地检查，一遍又一遍，只要同步任务执行完了，引擎就会去检查那些挂起来的异步任务，是不是可以进入主线程了。这种循环检查的机制，就叫做事件循环（Event Loop）。维基百科的定义是：“事件循环是一个程序结构，用于等待和发送消息和事件（a programming construct that waits for and dispatches events or messages in a program）”。

###### 异步操作的几种实现方式

- 回调函数
- 事件监听
- 发布/订阅模式。事件完全可以理解成”信号“，如果存在一个”信号中心“，某个任务执行完成，就向信号中心”发布“（publish）一个信号，其他任务可以向信号中心”订阅“（subscribe）这个信号，从而知道什么时候自己可以开始执行
- 定时器
- [Promise](https://wangdoc.com/javascript/async/promise.html)

###### setTimeout和setInterval的运行机制，是将指定的代码移出本轮事件循环，等到下一轮事件循环，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就继续等待。这意味着，setTimeout和setInterval指定的回调函数，必须等到本轮事件循环的所有同步任务都执行完，才会开始执行。由于前面的任务到底需要多少时间执行完，是不确定的，所以没有办法保证，setTimeout和setInterval指定的任务，一定会按照预定时间执行

---

## 1.4DOM

###### 节点【DOM的最小组成单位】

###### 节点类型

- Document：整个文档树的顶层节点
- DocumentType：doctype标签（比如<!DOCTYPE html>）
- Element：网页的各种HTML标签（比如\<body>、\<a>等）
- Attribute：网页元素的属性（比如class="right"）
- Text：标签之间或标签包含的文本
- Comment：注释
- DocumentFragment：文档的片段

###### 节点之间的关系

- 父节点关系（parentNode）：直接的那个上级节点
- 子节点关系（childNodes）：直接的下级节点
- 同级节点关系（sibling）：拥有同一个父节点的节点

**DOM 提供操作接口，用来获取这三种关系的节点。比如，子节点接口包括firstChild（第一个子节点）和lastChild（最后一个子节点）等属性，同级节点接口包括nextSibling（紧邻在后的那个同级节点）和previousSibling（紧邻在前的那个同级节点）属性**

#### *所有 DOM 节点对象都继承了 Node 接口，拥有一些共同的属性和方法。这是 DOM 操作的基础*

###### 属性

- Node.prototype.nodeType 节点类型，可选值：
	- 文档节点（document）：9，对应常量Node.DOCUMENT_NODE
	- 元素节点（element）：1，对应常量Node.ELEMENT_NODE
	- 属性节点（attr）：2，对应常量Node.ATTRIBUTE_NODE
	- 文本节点（text）：3，对应常量Node.TEXT_NODE
	- 文档片断节点（DocumentFragment）：11，对应常量Node.DOCUMENT_FRAGMENT_NODE
	- 文档类型节点（DocumentType）：10，对应常量Node.DOCUMENT_TYPE_NODE
	- 注释节点（Comment）：8，对应常量Node.COMMENT_NODE
- Node.prototype.nodeName 节点名称，可选值：
	- 文档节点（document）：#document
	- 元素节点（element）：大写的标签名
	- 属性节点（attr）：属性的名称
	- 文本节点（text）：#text
	- 文档片断节点（DocumentFragment）：#document-fragment
	- 文档类型节点（DocumentType）：文档的类型
	- 注释节点（Comment）：#comment
- Node.prototype.nodeValue 节点文本值，可读写。只有文本节点（text）、注释节点（comment）和属性节点（attr）有文本值，因此这三类节点的nodeValue可以返回结果，其他类型的节点一律返回null。同样的，也只有这三类节点可以设置nodeValue属性的值，其他类型的节点设置无效
- Node.prototype.textContent 节点及其后代节点的文本内容（会忽略HTML标签，既标签不会被包含在返回的文本中），可读写，写入文本时会把HTML标签转为文本
- Node.prototype.baseURI 当前网页的绝对路径，只读
- Node.prototype.ownerDocument 返回顶层文档对象，即document对象，document对象本身的ownerDocument属性，返回null
- Node.prototype.nextSibling 下一个同级节点，若无返回null
- Node.prototype.previousSibling 前一个同级节点，若无返回null
- Node.prototype.parentNode 返回父节点，只能是三种类型：元素节点（element）、文档节点（document）和文档片段节点（documentfragment）
- Node.prototype.parentElement 返回父元素节点，如果当前节点没有父节点，或者父节点类型不是元素节点，则返回null
- Node.prototype.firstChild，Node.prototype.lastChild 返回当前节点的第一个/最后一个子节点
- Node.prototype.childNodes 返回当前节点的所有子节点（**文档节点（document）就有两个子节点：文档类型节点（docType）和 HTML 根元素节点**）
- Node.prototype.isConnected 返回一个布尔值，判断当前节点是否在文档之中

###### 方法

- Node.prototype.appendChild() 添加子节点，如果参数节点是 DOM 已经存在的节点，appendChild方法会将其从原来的位置，移动到新位置
- Node.prototype.hasChildNodes() 是否含有子节点
- Node.prototype.cloneNode() 用于克隆一个节点。它接受一个布尔值作为参数，表示是否同时克隆子节点。它的返回值是一个克隆出来的新节点。注意：
	- 克隆一个节点，会拷贝该节点的所有属性，但是会丧失addEventListener方法和on-属性（即node.onclick = fn），添加在这个节点上的事件回调函数。
	- 该方法返回的节点不在文档之中，即没有任何父节点，必须使用诸如Node.appendChild这样的方法添加到文档之中。
	- 克隆一个节点之后，DOM 有可能出现两个有相同id属性（即id="xxx"）的网页元素，这时应该修改其中一个元素的id属性。如果原节点有name属性，可能也需要修改。
- Node.prototype.insertBefore() 将某个节点插入父节点内部的指定位置
- Node.prototype.removeChild() 接受一个子节点作为参数，用于从当前节点移除该子节点。返回值是移除的子节点
- Node.prototype.replaceChild() 用于将一个新的节点，替换当前节点的某一个子节点
- Node.prototype.contains() 返回一个布尔值，表示参数节点是否满足以下三个条件之一：
	- 参数节点为当前节点。
	- 参数节点为当前节点的子节点。
	- 参数节点为当前节点的后代节点。
- Node.prototype.compareDocumentPosition() 方法的用法，与contains方法完全一致，返回一个七个比特位的二进制值，表示参数节点与当前节点的关系，可能值的含义：

	二进制值|十进制值|含义
	:-:|:-:|:-
	000000|0|两个节点相同
	000001|1|两个节点不在同一个文档（即有一个节点不在当前文档）
	000010|2|参数节点在当前节点的前面
	000100|4|参数节点在当前节点的后面
	001000|8|参数节点包含当前节点
	010000|16|当前节点包含参数节点
	100000|32|浏览器内部使用

- Node.prototype.isEqualNode()，Node.prototype.isSameNode() isEqualNode()返回一个布尔值，用于检查两个节点是否相等。所谓相等的节点，指的是两个节点的类型相同、属性相同、子节点相同; isSameNode()方法返回一个布尔值，表示两个节点是否为同一个节点
- Node.prototype.normalize() 用于清理当前节点内部的所有文本节点（text）。它会去除空的文本节点，并且将毗邻的文本节点合并成一个，也就是说不存在空的文本节点，以及毗邻的文本节点
- Node.prototype.getRootNode() 返回当前节点所在文档的根节点，与ownerDocument属性的作用相同

#### 节点集合:NodeList、HTMLCollection，两者区别: NodeList可以包含各种类型的节点，HTMLCollection只能包含 HTML 元素节点

###### NodeList

###### NodeList实例类似于一个数组(但是，它不是数组，不能使用pop或push之类数组特有的方法)，成员节点对象，以下方法可获取NodeList实例：
#
- Node.childNodes
- document.querySelectorAll()等节点搜索方法

###### 目前，只有Node.childNodes返回的是一个动态集合，其他的 NodeList 都是静态集合

###### 属性与方法

- NodeList.prototype.length 节点数量
- NodeList.prototype.forEach() 用于遍历 NodeList 的所有成员。它接受一个回调函数作为参数，每一轮遍历就执行一次这个回调函数，用法与数组实例的forEach方法完全一致
- NodeList.prototype.item() 接受一个整数值作为参数，表示成员的位置，返回该位置上的成员，其实直接使用方括号索引即可‘[]’
- NodeList.prototype.keys()，NodeList.prototype.values()，NodeList.prototype.entries() 这三个方法都返回一个 ES6 的遍历器对象，可以通过for...of循环遍历获取每一个成员的信息。区别在于，keys()返回键名的遍历器，values()返回键值的遍历器，entries()返回的遍历器同时包含键名和键值的信息。

###### HTMLCollection(实例均为动态集合)

###### 返回HTMLCollection实例的，主要是一些Document对象的集合属性，比如document.links、docuement.forms、document.images等

###### 属性与方法

- HTMLCollection.prototype.length 返回HTMLCollection实例包含的成员数量
- HTMLCollection.prototype.item() 接受一个整数值作为参数，表示成员的位置，返回该位置上的成员，其实直接使用方括号索引即可‘[]’
- HTMLCollection.prototype.namedItem() 参数是一个字符串，表示id属性或name属性的值，返回对应的元素节点。如果没有对应的节点，则返回null










 




