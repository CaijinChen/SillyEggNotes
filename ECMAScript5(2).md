# **ECMAScript 5.1知识点总结 <二>**
---
## 0.0进阶知识点，总结参考自[网道,创作者：阮一峰](https://wangdoc.com/)

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

### NodeList

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

### HTMLCollection(实例均为动态集合)

###### 返回HTMLCollection实例的，主要是一些Document对象的集合属性，比如document.links、docuement.forms、document.images等

###### 属性与方法

- HTMLCollection.prototype.length 返回HTMLCollection实例包含的成员数量
- HTMLCollection.prototype.item() 接受一个整数值作为参数，表示成员的位置，返回该位置上的成员，其实直接使用方括号索引即可‘[]’
- HTMLCollection.prototype.namedItem() 参数是一个字符串，表示id属性或name属性的值，返回对应的元素节点。如果没有对应的节点，则返回null

### ParentNode

##### 只有元素节点（element）、文档节点（document）和文档片段节点（documentFragment）拥有子节点，因此只有这三类节点会继承ParentNode接口

###### 属性和方法

- ParentNode.children 回一个HTMLCollection实例，成员是当前节点的所有元素子节点。该属性只读
- ParentNode.firstElementChild 返回当前节点的第一个元素子节点。如果没有任何元素子节点，则返回null
- ParentNode.lastElementChild 返回当前节点的最后一个元素子节点，如果不存在任何元素子节点，则返回null
- ParentNode.childElementCount 返回一个整数，表示当前节点的所有元素子节点的数目。如果不包含任何元素子节点，则返回0
- ParentNode.append 为当前节点追加一个或多个子节点，位置是最后一个元素子节点的后面。该方法不仅可以添加元素子节点，还可以添加文本子节点
- ParentNode.prepend 方法为当前节点追加一个或多个子节点，位置是第一个元素子节点的前面。它的用法与append方法完全一致，也是没有返回值

### ChildNode

##### 如果一个节点有父节点，那么该节点就继承了ChildNode接口

###### 属性与方法

- ChildNode.remove() 用于从父节点移除当前节点
- ChildNode.before() 在当前节点的前面，插入一个或多个同级节点。两者拥有相同的父节点;该方法不仅可以插入元素节点，还可以插入文本节点
- ChildNode.after() 用于在当前节点的后面，插入一个或多个同级节点，两者拥有相同的父节点。用法与before方法完全相同
- ChildNode.replaceWith() 使用参数节点，替换当前节点。参数可以是元素节点，也可以是文本节点

## 1.5Document

> document节点对象代表整个文档，每张网页都有自己的document对象。window.document属性就指向这个对象。只要浏览器开始载入 HTML 文档，该对象就存在了，可以直接使用

### 获取

- 正常的网页，直接使用document或window.document。
- iframe框架里面的网页，使用iframe节点的contentDocument属性。
- Ajax 操作返回的文档，使用XMLHttpRequest对象的responseXML属性。
- 内部节点的ownerDocument属性。

### 属性

#### 快捷方式属性

- document.defaultView 返回document对象所属的window对象。如果当前文档不属于window对象，该属性返回null
- document.doctype 对于 HTML 文档来说，document对象一般有两个子节点。第一个子节点是document.doctype，指向<DOCTYPE>节点，即文档类型（Document Type Declaration，简写DTD）节点。HTML 的文档类型节点，一般写成<!DOCTYPE html>。如果网页没有声明 DTD，该属性返回null。
- document.documentElement属性返回当前文档的根元素节点（root）。它通常是document节点的第二个子节点，紧跟在document.doctype节点后面。HTML网页的该属性，一般是<html>节点
- document.body属性指向<body>节点，属性是可写的，如果改写它们的值，相当于移除所有子节点
- document.head属性指向<head>节点，属性是可写的，如果改写它们的值，相当于移除所有子节点
- document.scrollingElement属性返回文档的滚动元素。也就是说，当文档整体滚动时，到底是哪个元素在滚动
- document.activeElement属性返回获得当前焦点（focus）的 DOM 元素
- document.fullscreenElement属性返回当前以全屏状态展示的 DOM 元素。如果不是全屏状态，该属性返回null

#### 节点集合属性

- document.links属性返回当前文档所有设定了href属性的\<a>及\<area>节点
- document.forms属性返回所有\<form>表单节点
- document.images属性返回页面所有\<img>图片节点
- document.scripts属性返回所有\<script>节点
- document.embeds属性和document.plugins属性，都返回所有\<embed>节点
- document.styleSheets属性返回文档内嵌或引入的样式表集合
- **除了document.styleSheets，以上的集合属性返回的都是HTMLCollection实例;HTMLCollection实例是类似数组的对象，所以这些属性都有length属性，都可以使用方括号运算符引用成员。如果成员有id或name属性，还可以用这两个属性的值，在HTMLCollection实例上引用到这个成员**

#### 文档静态信息属性
 
- document.documentURI属性和document.URL属性都返回一个字符串，表示当前文档的网址。不同之处是它们继承自不同的接口，documentURI继承自Document接口，可用于所有文档；URL继承自HTMLDocument接口，只能用于 HTML 文档
- document.domain属性返回当前文档的域名，不包含协议和接口;另外，设置document.domain会导致端口被改成null。因此，如果通过设置document.domain来进行通信，双方网页都必须设置这个值，才能保证端口相同
- Location对象是浏览器提供的原生对象，提供 URL 相关的信息和操作方法。通过window.location和document.location属性，可以拿到这个对象
- document.lastModified属性返回一个字符串，表示当前文档最后修改的时间。不同浏览器的返回值，日期格式是不一样的
- document.title属性返回当前文档的标题。默认情况下，返回<title>节点的值。但是该属性是可写的，一旦被修改，就返回修改后的值
- document.characterSet属性返回当前文档的编码，比如UTF-8、ISO-8859-1等等
- document.referrer属性返回一个字符串，表示当前文档的访问者来自哪里,如果无法获取来源，或者用户直接键入网址而不是从其他网页点击进入，document.referrer返回一个空字符串
- document.dir返回一个字符串，表示文字方向。它只有两个可能的值：rtl表示文字从右到左，阿拉伯文是这种方式；ltr表示文字从左到右，包括英语和汉语在内的大多数文字采用这种方式
- compatMode属性返回浏览器处理文档的模式，可能的值为BackCompat（向后兼容模式）和CSS1Compat（严格模式）

#### 文档状态属性

- document.hidden属性返回一个布尔值，表示当前页面是否可见。如果窗口最小化、浏览器切换了 Tab，都会导致导致页面不可见，使得document.hidden返回true
- document.visibilityState返回文档的可见状态
	- visible：页面可见。注意，页面可能是部分可见，即不是焦点窗口，前面被其他窗口部分挡住了。
	- hidden：页面不可见，有可能窗口最小化，或者浏览器切换到了另一个 Tab。
	- prerender：页面处于正在渲染状态，对于用户来说，该页面不可见。
	- unloaded：页面从内存里面卸载了
- document.readyState属性返回当前文档的状态，共有三种可能的值
	- loading：加载 HTML 代码阶段（尚未完成解析）
	- interactive：加载外部资源阶段
	- complete：加载完成
- document.cookie属性用来操作浏览器 Cookie
- document.designMode属性控制当前文档是否可编辑。该属性只有两个值on和off，默认值为off。一旦设为on，用户就可以编辑整个文档的内容
- document.implementation属性返回一个DOMImplementation对象。该对象有三个方法，主要用于创建独立于当前文档的新的 Document 对象
	- DOMImplementation.createDocument()：创建一个 XML 文档。
	- DOMImplementation.createHTMLDocument()：创建一个 HTML 文档。
	- DOMImplementation.createDocumentType()：创建一个 DocumentType 对象。
- document.open方法清除当前文档所有内容，使得文档处于可写状态，供document.write方法写入内容
- document.close方法用来关闭document.open()打开的文档
- document.write方法用于向当前文档写入内容。在网页的首次渲染阶段，只要页面没有关闭写入（即没有执行document.close()），document.write写入的内容就会追加在已有内容的后面；如果页面已经解析完成（DOMContentLoaded事件发生之后），再调用write方法，它会先调用open方法，擦除当前文档所有内容，然后再写入
- document.writeln方法与write方法完全一致，除了会在输出内容的尾部添加换行符，writeln方法添加的是 ASCII 码的换行符，渲染成 HTML 网页时不起作用，即在网页上显示不出换行。网页上的换行，必须显式写入<br>
- document.querySelector方法接受一个 CSS 选择器作为参数，返回匹配该选择器的元素节点。如果有多个节点满足匹配条件，则返回第一个匹配的节点。如果没有发现匹配的节点，则返回null
- document.querySelectorAll方法与querySelector用法类似，区别是返回一个NodeList对象
- document.getElementsByTagName方法搜索 HTML 标签名，返回符合条件的元素。它的返回值是一个类似数组对象（HTMLCollection实例），可以实时反映 HTML 文档的变化。如果没有任何匹配的元素，就返回一个空集；注意，元素节点本身也定义了getElementsByTagName方法，返回该元素的后代元素中符合条件的元素。也就是说，这个方法不仅可以在document对象上调用，也可以在任何元素节点上调用
- document.getElementsByClassName方法返回一个类似数组的对象（HTMLCollection实例），包括了所有class名字符合指定条件的元素，元素的变化实时反映在返回结果中
- document.getElementsByName方法用于选择拥有name属性的 HTML 元素（比如`<form>、<radio>、<img>、<frame>、<embed>和<object>`等），返回一个类似数组的的对象（NodeList实例），因为name属性相同的元素可能不止一个
- document.getElementById方法返回匹配指定id属性的元素节点。如果没有发现匹配的节点，则返回null
- document.elementFromPoint方法返回位于页面指定位置最上层的元素节点；方法的两个参数，依次是相对于当前视口左上角的横坐标和纵坐标，单位是像素。如果位于该位置的 HTML 元素不可返回（比如文本框的滚动条），则返回它的父元素（比如文本框）。如果坐标值无意义（比如负值或超过视口大小），则返回null
- document.elementsFromPoint()返回一个数组，成员是位于指定坐标（相对于视口）的所有元素
- document.caretPositionFromPoint()返回一个 CaretPosition 对象，包含了指定坐标点在节点对象内部的位置信息。CaretPosition 对象就是光标插入点的概念，用于确定光标点在文本对象内部的具体位置，它有两个属性：
	- CaretPosition.offsetNode：该位置的节点对象
	- CaretPosition.offset：该位置在offsetNode对象内部，与起始位置相距的字符数
- document.createElement方法用来生成元素节点，并返回该节点
- document.createTextNode方法用来生成文本节点（Text实例），并返回该节点。它的参数是文本节点的内容
- document.createAttribute方法生成一个新的属性节点（Attr实例），并返回它
- document.createComment方法生成一个新的注释节点，并返回该节点
- document.createDocumentFragment方法生成一个空的文档片段对象（DocumentFragment实例）；DocumentFragment是一个存在于内存的 DOM 片段，不属于当前文档，常常用来生成一段较复杂的 DOM 结构，然后再插入当前文档。这样做的好处在于，因为DocumentFragment不属于当前文档，对它的任何改动，都不会引发网页的重新渲染，比直接修改当前文档的 DOM 有更好的性能表现
- document.createEvent方法生成一个事件对象（Event实例），方法的参数是事件类型，比如UIEvents、MouseEvents、MutationEvents、HTMLEvents
- document.addEventListener()，document.removeEventListener()，document.dispatchEvent() 这三个方法用于处理document节点的事件。它们都继承自EventTarget接口
- document.hasFocus方法返回一个布尔值，表示当前文档之中是否有元素被激活或获得焦点，*注意，有焦点的文档必定被激活（active），反之不成立，激活的文档未必有焦点。比如，用户点击按钮，从当前窗口跳出一个新窗口，该新窗口就是激活的，但是不拥有焦点*
- document.adoptNode方法将某个节点及其子节点，从原来所在的文档或DocumentFragment里面移除，归属当前document对象，返回插入后的新节点。**注意，document.adoptNode方法只是改变了节点的归属，并没有将这个节点插入新的文档树。所以，还要再用appendChild方法或insertBefore方法，将新节点插入当前文档树**
- document.importNode方法则是从原来所在的文档或DocumentFragment里面，拷贝某个节点及其子节点，让它们归属当前document对象。拷贝的节点对象的ownerDocument属性，会变成当前的document对象，而parentNode属性是null
- document.createNodeIterator方法返回一个子节点遍历器。方法第一个参数为所要遍历的根节点，第二个参数为所要遍历的节点类型,可选值为：
	- 所有节点：NodeFilter.SHOW_ALL
	- 元素节点：NodeFilter.SHOW_ELEMENT
	- 文本节点：NodeFilter.SHOW_TEXT
	- 评论节点：NodeFilter.SHOW_COMMENT
- document.createTreeWalker方法返回一个 DOM 的子树遍历器。它与document.createNodeIterator方法基本是类似的，区别在于它返回的是TreeWalker实例，后者返回的是NodeIterator实例。另外，它的第一个节点不是根节点
- document.execCommand document.designMode属性设为on，那么整个文档用户可编辑；如果元素的contenteditable属性设为true，那么该元素可编辑。这两种情况下，可以使用document.execCommand()方法，改变内容的样式，比如document.execCommand('bold')会使得字体加粗，三个参数为：
	- command：字符串，表示所要实施的样式。
	- showDefaultUI：布尔值，表示是否要使用默认的用户界面，建议总是设为false。
	- input：字符串，表示该样式的辅助内容，比如生成超级链接时，这个参数就是所要链接的网址。如果第二个参数设为true，那么浏览器会弹出提示框，要求用户在提示框输入该参数。但是，不是所有浏览器都支持这样做，为了兼容性，还是需要自己部署获取这个参数的方式
- document.queryCommandSupported document.queryCommandEnabled()方法返回一个布尔值，表示浏览器是否允许使用这个方法
- document.queryCommandSupported()方法返回一个布尔值，表示当前是否可用某种样式改变。比如，加粗只有存在文本选中时才可用，如果没有选中文本，就不可用
- document.getSelection 指向window.getSelection()

---

### 1.6Element

> Element节点对象对应网页的 HTML 元素。每一个 HTML 元素，在 DOM 树上都会转化成一个Element节点对象（以下简称元素节点）
#
> Element对象继承了Node接口，因此Node的属性和方法在Element对象都存在。此外，不同的 HTML 元素对应的元素节点是不一样的，浏览器使用不同的构造函数，生成不同的元素节点，比如\<a>元素的节点对象由HTMLAnchorElement构造函数生成,\<button>元素的节点对象由HTMLButtonElement构造函数生成。因此，元素节点不是一种对象，而是一组对象，这些对象除了继承Element的属性和方法，还有各自构造函数的属性和方法

#### 元素特性的相关属性

- Element.id属性返回指定元素的id属性，该属性可读写
- Element.tagName属性返回指定元素的大写标签名，与nodeName属性的值相等
- Element.dir属性用于读写当前元素的文字方向，可能是从左到右（"ltr"），也可能是从右到左（"rtl"）
- Element.accessKey属性用于读写分配给当前元素的快捷键

		// HTML 代码如下
		// \<button accesskey="h" id="btn">点击\</button>
		var btn = document.getElementById('btn');
		btn.accessKey // "h"
- Element.draggable属性返回一个布尔值，表示当前元素是否可拖动。该属性可读写
- Element.lang属性返回当前元素的语言设置。该属性可读写
- Element.tabIndex属性返回一个整数，表示当前元素在 Tab 键遍历时的顺序。该属性可读写
- Element.title属性用来读写当前元素的 HTML 属性title。该属性通常用来指定，鼠标悬浮时弹出的文字提示框

#### 元素状态的相关属性

- Element.hidden属性返回一个布尔值，表示当前元素的hidden属性，用来控制当前元素是否可见。该属性可读写(注意，该属性与 CSS 设置是互相独立的。CSS 对这个元素可见性的设置，Element.hidden并不能反映出来，CSS 的设置高于Element.hidden。如果 CSS 指定了该元素不可见（display: none）或可见（display: hidden），那么Element.hidden并不能改变该元素实际的可见性)
- Element.contentEditable，HTML 元素可以设置contentEditable属性，使得元素的内容可以编辑，属性返回一个字符串，表示是否设置了contenteditable属性返回一个字串，有三种可能的值。该属性可写：
	- "true"：元素内容可编辑
	- "false"：元素内容不可编辑
	- "inherit"：元素是否可编辑，继承了父元素的设置
- Element.isContentEditable属性返回一个布尔值，同样表示是否设置了contenteditable属性。该属性只读
- Element.attributes属性返回一个类似数组的对象，成员是当前元素节点的所有属性节点
- Element.className属性用来读写当前元素节点的class属性。它的值是一个字符串，每个class之间用空格分割
- Element.classList属性返回一个类似数组的对象，当前元素节点的每个class就是这个对象的一个成员，该对象的length属性（只读）返回当前元素的class数量，可对classList操作：
	- add()：增加一个 class。
	- remove()：移除一个 class。
	- contains()：检查当前元素是否包含某个 class。
	- toggle()：将某个 class 移入或移出当前元素。
	- item()：返回指定索引位置的 class。
	- toString()：将 class 的列表转为字符串
- Element.dataset属性返回一个对象，可以从这个对象读写data-属性；**注意，dataset上面的各个属性返回都是字符串。**HTML 代码中，data-属性的属性名，只能包含英文字母、数字、连词线（-）、点（.）、冒号（:）和下划线（_）。它们转成 JavaScript 对应的dataset属性名，data-abc-def对应dataset.abcDef，data-abc-1对应dataset["abc-1"]。除了使用dataset读写data-属性，也可以使用Element.getAttribute()和Element.setAttribute()，通过完整的属性名读写这些属性。
- Element.innerHTML属性返回一个字符串，等同于该元素包含的所有 HTML 代码。该属性可读写，常用来设置某个节点的内容。它能改写所有元素节点的内容，包括<HTML>和<body>元素。**注意，读取属性值的时候，如果文本节点包含&、小于号（<）和大于号（>），innerHTML属性会将它们转为实体形式&amp;、&lt;、&gt;。如果想得到原文，建议使用element.textContent属性**
- Element.outerHTML属性返回一个字符串，表示当前元素节点的所有 HTML 代码，包括该元素本身和所有子元素，属性是可读写的，对它进行赋值，等于替换掉当前元素
- Element.clientHeight属性返回一个整数值，表示元素节点的 CSS 高度（单位像素），只对块级元素生效，对于行内元素返回0。如果块级元素没有设置 CSS 高度，则返回实际高度
- Element.
- 属性返回元素节点的 CSS 宽度，同样只对块级元素有效，也是只包括元素本身的宽度和padding，如果有垂直滚动条，还要减去垂直滚动条的宽度（**document.documentElement的clientHeight属性，返回当前视口的高度（即浏览器窗口的高度），等同于window.innerHeight属性减去水平滚动条的高度（如果有的话）。document.body的高度则是网页的实际高度。一般来说，document.body.clientHeight大于document.documentElement.clientHeight**）
- Element.clientTop属性等于网页元素顶部边框的宽度（单位像素）
- Element.clientLeft属性等于元素节点左边框（left border）的宽度
- Element.scrollHeight属性返回一个整数值（小数会四舍五入），表示当前元素的总高度（单位像素），包括溢出容器、当前不可见的部分。它包括padding，但是不包括border、margin以及水平滚动条的高度（如果有水平滚动条的话），还包括伪元素（::before或::after）的高度，属性只读
- Element.scrollWidth属性表示当前元素的总宽度（单位像素），其他地方都与scrollHeight属性类似。属性只读
- **注意，如果元素节点的内容出现溢出，即使溢出的内容是隐藏的，scrollHeight属性仍然返回元素的总高度。**
- Element.scrollLeft属性表示当前元素的水平滚动条向右侧滚动的像素数量，设置该属性的值，会导致浏览器将当前元素自动滚动到相应的位置
- Element.scrollTop属性表示当前元素的垂直滚动条向下滚动的像素数量，设置该属性的值，会导致浏览器将当前元素自动滚动到相应的位置
- Element.offsetParent属性返回最靠近当前元素的、并且 CSS 的position属性不等于static的上层元素（**如果某个元素的所有上层节点的position属性都是static，则Element.offsetParent属性指向<body>元素**）
- Element.offsetHeight属性返回一个整数，表示元素的 CSS 垂直高度（单位像素），只比Element.clientHeight多了边框的高度
- Element.offsetWidth属性表示元素的 CSS 水平宽度（单位像素）。只比Element.clientWidth多了边框的宽度
- Element.offsetLeft返回当前元素左上角相对于Element.offsetParent节点的水平位移
- Element.offsetTop返回当前元素左上角相对于Element.offsetParent节点的垂直位移
- Element.style 每个元素节点都有style用来读写该元素的行内样式信息
- Element.children属性返回一个类似数组的对象（HTMLCollection实例），包括当前元素节点的所有子元素。如果当前元素没有子元素，则返回的对象包含零个成员
- Element.childElementCount属性返回当前元素节点包含的子元素节点的个数，与Element.children.length的值相同
- Element.firstElementChild属性返回当前元素的第一个元素子节点
- Element.lastElementChild属性返回当前元素的最后一个元素子节点
- Element.nextElementSibling属性返回当前元素节点的后一个同级元素节点，如果没有则返回null
- Element.previousElementSibling属性返回当前元素节点的前一个同级元素节点，如果没有则返回null

### 方法

- 属性相关方法
	- getAttribute()：读取某个属性的值
	- getAttributeNames()：返回当前元素的所有属性名
	- setAttribute()：写入属性值
	- hasAttribute()：某个属性是否存在
	- hasAttributes()：当前元素是否有属性
	- removeAttribute()：删除属性
- 其他方法
	- Element.querySelector方法接受 CSS 选择器作为参数，返回父元素的第一个匹配的子元素。如果没有找到匹配的子元素，就返回null
	- Element.querySelectorAll方法接受 CSS 选择器作为参数，返回一个NodeList实例，包含所有匹配的子元素
	- Element.getElementsByClassName方法返回一个HTMLCollection实例，成员是当前元素节点的所有具有指定 class 的子元素节点。该方法与document.getElementsByClassName方法的用法类似，只是搜索范围不是整个文档，而是当前元素节点
	- Element.getElementsByTagName方法返回一个HTMLCollection实例，成员是当前节点的所有匹配指定标签名的子元素节点
	- Element.closest方法接受一个 CSS 选择器作为参数，返回匹配该选择器的、最接近当前节点的一个祖先节点（包括当前节点本身）。如果没有任何节点匹配 CSS 选择器，则返回null
	- Element.matches方法返回一个布尔值，表示当前元素是否匹配给定的 CSS 选择器
	- Element.addEventListener()：添加事件的回调函数
	- Element.removeEventListener()：移除事件监听函数
	- Element.dispatchEvent()：触发事件
	- Element.scrollIntoView方法滚动当前元素，进入浏览器的可见区域，类似于设置window.location.hash的效果
	- Element.getBoundingClientRect方法返回一个对象，提供当前元素节点的大小、位置等信息，基本上就是 CSS 盒状模型的所有信息，方法返回的rect对象，具有以下属性（全部为只读）：
		- x：元素左上角相对于视口的横坐标
		- y：元素左上角相对于视口的纵坐标
		- height：元素高度
		- width：元素宽度
		- left：元素左上角相对于视口的横坐标，与x属性相等
		- right：元素右边界相对于视口的横坐标（等于x + width）
		- top：元素顶部相对于视口的纵坐标，与y属性相等
		- bottom：元素底部相对于视口的纵坐标（等于y + height）
		- **注意：width和height包括了元素本身 + padding + border**
	- Element.getClientRects方法返回一个类似数组的对象，里面是当前元素在页面上形成的所有矩形（所以方法名中的Rect用的是复数）。每个矩形都有bottom、height、left、right、top和width六个属性，表示它们相对于视口的四个坐标，以及本身的高度和宽度。对于盒状元素（比如\<div>和\<p>），该方法返回的对象中只有该元素一个成员。对于行内元素（比如\<span>、\<a>、\<em>），该方法返回的对象有多少个成员，取决于该元素在页面上占据多少行。这是它和Element.getBoundingClientRect()方法的主要区别，后者对于行内元素总是返回一个矩形。
	- Element.insertAdjacentElement方法在相对于当前元素的指定位置，插入一个新的节点。该方法返回被插入的节点，如果插入失败，返回null,方法一共可以接受两个参数，第一个参数是一个字符串，表示插入的位置，第二个参数是将要插入的节点。第一个参数只可以取如下的值:
		- beforebegin：当前元素之前
		- afterbegin：当前元素内部的第一个子节点前面
		- beforeend：当前元素内部的最后一个子节点后面
		- afterend：当前元素之后
	- Element.insertAdjacentHTML方法用于将一个 HTML 字符串，解析生成 DOM 结构，插入相对于当前节点的指定位置
	- Element.insertAdjacentText方法在相对于当前节点的指定位置，插入一个文本节点，用法与
	- Element.insertAdjacentHTML方法完全一致
	- Element.remove方法继承自 ChildNode 接口，用于将当前元素节点从它的父节点移除
	- Element.focus方法用于将当前页面的焦点，转移到指定元素上
	- Element.blur方法用于将焦点从当前元素移除
	- Element.click方法用于在当前元素上模拟一次鼠标点击，相当于触发了click事件

## 1.7操作属性

> HTML 元素包括标签名和若干个键值对，这个键值对就称为“属性”（attribute）。属性本身是一个对象（Attr对象），但是实际上，这个对象极少使用。一般都是通过元素节点对象（HTMlElement对象）来操作属性。
#
> Element.attributes,元素对象有一个attributes属性，返回一个类似数组的动态对象，成员是该元素标签的所有属性节点对象，属性的实时变化都会反映在这个节点对象上。其他类型的节点对象，虽然也有attributes属性，但返回的都是null，因此可以把这个属性视为元素对象独有的，单个属性可以通过序号引用，也可以通过属性名引用

### 方法

> **适用性** 这六个方法对所有属性（包括用户自定义的属性）都适用。
#
> **返回值** getAttribute()只返回字符串，不会返回其他类型的值。
#
> **属性名** 这些方法只接受属性的标准名称，不用改写保留字，比如for和class都可以直接使用。另外，这些方法对于属性名是大小写不敏感的。

- getAttribute(), 方法返回当前元素节点的指定属性。如果指定属性不存在，则返回null
- getAttributeNames(), 返回一个数组，成员是当前元素的所有属性的名字。如果当前元素没有任何属性，则返回一个空数组。使用Element.attributes属性，也可以拿到同样的结果，唯一的区别是它返回的是类似数组的对象
- setAttribute() 方法用于为当前元素节点新增属性。如果同名属性已存在，则相当于编辑已存在的属性。该方法没有返回值，**注意：首先，属性值总是字符串，其他类型的值会自动转成字符串，比如布尔值true就会变成字符串true；其次，上例的disable属性是一个布尔属性，对于<button>元素来说，这个属性不需要属性值，只要设置了就总是会生效，因此setAttribute方法里面可以将disabled属性设成任意值**
- hasAttribute() 方法返回一个布尔值，表示当前元素节点是否包含指定属性
- hasAttributes() 方法返回一个布尔值，表示当前元素是否有属性，如果没有任何属性，就返回false，否则返回true
- removeAttribute() 方法移除指定属性。该方法没有返回值

### 自定义属性 data-*

> data-后面的属性名有限制，只能包含字母、数字、连词线（-）、点（.）、冒号（:）和下划线（_)。而且，属性名不应该使用A到Z的大写字母，比如不能有data-helloWorld这样的属性名，而要写成data-hello-world,转成dataset的键名时，连词线后面如果跟着一个小写字母，那么连词线会被移除，该小写字母转为大写字母，其他字符不变。反过来，dataset的键名转成属性名时，所有大写字母都会被转成连词线+该字母的小写形式，其他字符不变。比如，dataset.helloWorld会转成data-hello-world









 




