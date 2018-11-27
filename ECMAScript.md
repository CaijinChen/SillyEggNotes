# **ECMAScript 5.1知识点总结**
---
## 1.1变量提升

###### JavaScript 引擎的工作方式是，先解析代码，获取所有被声明的变量，然后再一行一行地运行。这造成的结果，就是所有的变量的声明语句，都会被提升到代码的头部，这就叫做变量提升

## 1.2区块

###### avaScript 使用大括号，将多个相关的语句组合在一起，称为“区块”（block）
	
	{  
	  var a = 1;  
	}
	a // 1
	
*区块对于var命令不构成单独的作用域，与不使用区块的情况没有任何区别*

## 1.3标签

###### JavaScript 语言允许，语句的前面有标签（label），相当于定位符，用于跳转到程序的任意位置，标签的格式如下

	label:
		  语句

	//example
	//标签通常与break语句和continue语句配合使用，跳出特定的循环或代码块，下面可以直接跳出两层循环
	top:
	  for (var i = 0; i < 3; i++){
	    for (var j = 0; j < 3; j++){
	      if (i === 1 && j === 1) break top;
	      console.log('i=' + i + ', j=' + j);
	    }
	  }

## 2.1.1数据类型

###### JavaScript 语言的每一个值，都属于某一种数据类型。JavaScript 的数据类型，共有六种。（ES6 又新增了第七种 Symbol 类型的值）

* 数值（number）：整数和小数（比如1和3.14）
* 字符串（string）：文本（比如Hello World）。
* 布尔值（boolean）：表示真伪的两个特殊值，即true（真）和false（假）
* undefined：表示“未定义”或不存在，即由于目前没有定义，所以此处暂时没有任何值
* null：表示空值，即此处的值为空。
* 对象（object）：各种值组成的集合。

###### 数值、字符串、布尔值这三种类型，合称为原始类型（primitive type）的值，即它们是最基本的数据类型，不能再细分了。对象则称为合成类型（complex type）的值，因为一个对象往往是多个原始类型的值的合成，可以看作是一个存放各种值的容器。至于undefined和null，一般将它们看成两个特殊值

###### 对象是最复杂的数据类型，又可以分成三个子类型。

* 狭义的对象（object）
* 数组（array）
* 函数（function）

###### 狭义的对象和数组是两种不同的数据组合方式，除非特别声明，本教程的”对象“都特指狭义的对象。函数其实是处理数据的方法，JavaScript 把它当成一种数据类型，可以赋值给变量，这为编程带来了很大的灵活性，也为 JavaScript 的“函数式编程”奠定了基础

## 2.1.2typeof

###### 数值、字符串、布尔值、未声明或未定义、函数、对象、null数组分别返回number、string、boolean、undefined、function、object、object、object

*均以字符串形式返回，如："string"，"function"*

## 2.2null、undefined

###### 对于null和undefined，大致可以像下面这样理解：

> null表示空值，即该处的值现在为空。调用函数时，某个参数未设置任何值，这时就可以传入null，表示该参数为空。比如，某个函数接受引擎抛出的错误作为参数，如果运行过程中未出错，那么这个参数就会传入null，表示未发生错误。

> undefined表示“未定义”

	if (!undefined) {
	  console.log('undefined is false');
	}
	// undefined is false
	
	if (!null) {
	  console.log('null is false');
	}
	// null is false
	
	undefined == null
	// true
	
	Number(null) // 0
	Number(undefined) // NaN

###### undefined的典型应用场景：

	// 变量声明了，但没有赋值
	var i;
	i // undefined
	
	// 调用函数时，应该提供的参数没有提供，该参数等于 undefined
	function f(x) {
	  return x;
	}
	f() // undefined
	
	// 对象没有赋值的属性
	var  o = new Object();
	o.p // undefined
	
	// 函数没有返回值时，默认返回 undefined
	function f() {}
	f() // undefined

## 2.3布尔值

###### JavaScript 预期某个位置应该是布尔值，会将该位置上现有的值自动转为布尔值。转换规则是除了下面六个值被转为false，其他值都视为true。

* undefined
* null
* false
* 0
* NaN
* ""或''（空字符串）

###### 空数组与空对象对应的布尔值为true

	if ([]) {
	  console.log('true');
	}
	// true
	
	if ({}) {
	  console.log('true');
	}
	// true

## 2.4.1数值

###### JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。所以，1与1.0是相同的，是同一个数。浮点数不是精确的值，所以容易出现意想不到的情况

	0.1 + 0.2 === 0.3
	// false
	
	0.3 / 0.1
	// 2.9999999999999996
	
	(0.3 - 0.2) === (0.2 - 0.1)
	// false

###### 浮点数采用IEEE 754标准，包含1个符号位，11位指数位，52位小数位，又由于省略存储第一位数字（总是1），故小数位可为53位，这意味着，绝对值小于2的53次方的整数，即-2^53到2^53，都可以精确表示，大于2的53次方的数值，都无法保持精度。由于2的53次方是一个16位的十进制数值，所以简单的法则就是，JavaScript 对15位的十进制数都可以精确处理（此处注意是精度会受到影响，不考虑精度影响，数值可表示的范围远大于绝对值2^53之内，详情看数值范围）

	var c = Math.pow(2, 200)
	//undefined
	c
	//1.6069380442589903e+60

## 2.4.2数值范围

###### 根据标准，64位浮点数的指数部分的长度是11个二进制位，意味着指数部分的最大值是2047（2的11次方减1）。也就是说，64位浮点数的指数部分的值最大为2047，分出一半表示负数，则 JavaScript 能够表示的数值范围为2^1024到2^-1023（开区间），超出这个范围的数无法表示。

###### 如果一个数大于等于2的1024次方，那么就会发生“正向溢出”，即 JavaScript 无法表示这么大的数，这时就会返回Infinity。如果一个数小于2的-1075次方（指数部分最小值-1023，再加上小数部分的52位），那么就会发生为“负向溢出”，即 JavaScript 无法表示这么小的数，这时会直接返回0

	Math.pow(2, 1024)
	//Infinity
	Math.pow(2, -1023)
	//1.1125369292536007e-308
	Math.pow(2, -1075)
	//5e-324
	Math.pow(2, -1076)
	//0
	Math.pow(2, 1023)
	//8.98846567431158e+307

###### 以下两种情况，JavaScript 会自动将数值转为科学计数法表示，其他情况都采用字面形式直接表示

* 小数点前的数字多于21位
* 小数点后的零多于5个

####关于数值的其他

###### 存在正零和负零

###### NaN是 JavaScript 的特殊值，表示“非数字”（Not a Number），主要出现在将字符串解析成数字出错的场合，另外，一些数学函数的运算结果会出现NaN。NaN不等于任何值，包括它本身

*typeof NaN //“number”*

###### Infinity表示“无穷”，用来表示两种场景。一种是一个正的数值太大，或一个负的数值太小，无法表示；另一种是非0数值除以0，得到Infinity。Infinity有正负之分，Infinity表示正的无穷，-Infinity表示负的无穷

	Infinity > NaN // false
	-Infinity > NaN // false
	
	Infinity < NaN // false
	-Infinity < NaN // false
###### 前导0表示八进制，处理时很容易造成混乱。ES5 的严格模式和 ES6，已经废除了这种表示法，但是浏览器为了兼容以前的代码，目前还继续支持这种表示法

#### 与数值相关的全局方法

* parseInt() 用于将字符串转为整数
* parseFloat() 用于将一个字符串转为浮点数
* isNaN() 判断一个值是否为NaN
* isFinite() 返回一个布尔值，表示某个值是否为正常的数值

## 2.5字串

###### 字串分行书写可以使用反斜杠 '\'

###### 转义：

* \0 ：null（\u0000）
* \b ：后退键（\u0008）
* \f ：换页符（\u000C）
* \n ：换行符（\u000A）
* \r ：回车键（\u000D）
* \t ：制表符（\u0009）
* \v ：垂直制表符（\u000B）
* \' ：单引号（\u0027）
* \" ：双引号（\u0022）
* \\ ：反斜杠（\u005C）

###### 字符串可以被视为字符数组，因此可以使用数组的方括号运算符，用来返回某个位置的字符

###### 字符串内部的单个字符无法改变和增删，字符串length属性返回字符串的长度，该属性也是无法改变的

###### JavaScript 使用 Unicode 字符集。JavaScript 引擎内部，所有字符都用 Unicode 表示。JavaScript 不仅以 Unicode 储存字符，还允许直接在程序中使用 Unicode 码点表示字符，即将字符写成\uxxxx的形式，其中xxxx代表该字符的 Unicode 码点。解析代码的时候，JavaScript 会自动识别一个字符是字面形式表示，还是 Unicode 形式表示。输出给用户的时候，所有字符都会转成字面形式

###### 每个字符在 JavaScript 内部都是以16位（即2个字节）的 UTF-16 格式储存。也就是说，JavaScript 的单位字符长度固定为16位长度，即2个字节。但是，UTF-16 有两种长度：对于码点在U+0000到U+FFFF之间的字符，长度为16位（即2个字节）；对于码点在U+10000到U+10FFFF之间的字符，长度为32位（即4个字节），而且前两个字节在0xD800到0xDBFF之间，后两个字节在0xDC00到0xDFFF之间。举例来说，码点U+1D306对应的字符为𝌆，它写成 UTF-16 就是0xD834 0xDF06。所以，JavaScript 对 UTF-16 的支持是不完整的，由于历史原因，只支持两字节的字符，不支持四字节的字符。

###### Base64编码（不适合非 ASCII 码的字符，不过可经过转码后使用）：

* btoa()：任意值转为 Base64 编码
* atob()：Base64 编码转为原来的值

#
	function b64Encode(str) {
	  return btoa(encodeURIComponent(str));
	}
	
	function b64Decode(str) {
	  return decodeURIComponent(atob(str));
	}
	
	b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
	b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"

## 2.6对象

###### 简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。对象的所有键名都是字符串（ES6 又引入了 Symbol 值也可以作为键名），所以加不加引号都可以。如果键名是数值，会被自动转为字符串。如果键名不符合标识名的条件（比如第一个字符为数字，或者含有空格或运算符），且也不是数字，则必须加上引号，否则会报错

###### 如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。但是，这种引用只局限于对象，如果两个变量指向同一个原始类型的值。那么，变量这时都是值的拷贝

###### 查看一个对象本身的所有属性，可以使用**Object.keys**方法

###### delete命令用于删除对象的属性，删除成功后返回true（若属性本来就不存在也返回true!）

###### in运算符用于检查对象是否包含某个属性（注意，检查的是键名，不是键值），如果包含就返回true，否则返回false。它的左边是一个字符串，表示属性名，右边是一个对象.in运算符的一个问题是，它不能识别哪些属性是对象自身的，哪些属性是继承的。就像上面代码中，对象obj本身并没有toString属性，但是in运算符会返回true，因为这个属性是继承的。这时，可以使用对象的hasOwnProperty方法判断一下，是否为对象自身的属性

###### for...in循环用来遍历一个对象的全部属性，它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。它不仅遍历对象自身的属性，还遍历继承的属性。

###### with语句

	with (对象) {
	  语句;
	}
	//它的作用是操作同一个对象的多个属性时，提供一些书写的方便
	// 例一
	var obj = {
	  p1: 1,
	  p2: 2,
	};
	with (obj) {
	  p1 = 4;
	  p2 = 5;
	}
	// 等同于
	obj.p1 = 4;
	obj.p2 = 5;
	
	// 例二
	with (document.links[0]){
	  console.log(href);
	  console.log(title);
	  console.log(style);
	}
	// 等同于
	console.log(document.links[0].href);
	console.log(document.links[0].title);
	console.log(document.links[0].style);

	//注意，如果with区块内部有变量的赋值操作，必须是当前对象已经存在的属性，否则会创造一个当前作用域的全局变量。这是因为with区块没有改变作用域，它的内部依然是当前作用域
	
	var obj = {};
	with (obj) {
	  p1 = 4;
	  p2 = 5;
	}
	
	obj.p1 // undefined
	p1 // 4

## 2.7函数

###### 声明函数的3种方式：

* function命令

		function print(s) {
		  console.log(s);
		}
* 函数表达式

		var print = function(s) {
		console.log(s);
		};
* Function 构造函数

		var add = new Function(
		  'x',
		  'y',
		  'return x + y'
		);
		
		// 等同于
		function add(x, y) {
		  return x + y;
		}

###### JavaScript 语言将函数看作一种值，与其它值（数值、字符串、布尔值等等）地位相同。凡是可以使用值的地方，就能使用函数。比如，可以把函数赋值给变量和对象的属性，也可以当作参数传入其他函数，或者作为函数的结果返回。函数只是一个可以执行的值，此外并无特殊之处。由于函数与其他数据类型地位平等，所以在 JavaScript 语言中又称函数为第一等公民

###### JavaScript 引擎将函数名视同变量名，所以采用function命令声明函数时，整个函数会像变量声明一样，被提升到代码头部

###### 如果同时采用function命令和赋值语句声明同一个函数，最后总是采用赋值语句的定义

#### 函数的属性和方法

* name属性返回函数的名字
* length属性返回函数预期传入的参数个数，即函数定义之中的参数个数
* toString方法返回一个字符串，内容是函数的源码（函数内部的注释也可以返回）
* 在 ES5 的规范中，Javascript 只有两种作用域：一种是全局作用域，变量在整个程序中一直存在，所有地方都可以读取；另一种是函数作用域，变量只在函数内部存在。（ES6 又新增了块级作用域）
* 与全局作用域一样，函数作用域内部也会产生“变量提升”现象。var命令声明的变量，不管在什么位置，变量声明都会被提升到函数体的头部
* 函数本身也是一个值，也有自己的作用域。它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在的作用域无关（总之，函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域）
* 函数参数不是必需的，Javascript 允许省略参数
* 函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是传值传递（passes by value）。这意味着，在函数体内修改参数值，不会影响到函数外部；但是，如果函数参数是复合类型的值（数组、对象、其他函数），传递方式是传址传递（pass by reference）。也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值
* 由于 JavaScript 允许函数有不定数目的参数，所以需要一种机制，可以在函数体内部读取所有参数。这就是arguments对象的由来（正常模式下，arguments对象可以在运行时修改；严格模式下，arguments对象是一个只读对象，修改它是无效的，但不会报错）。arguments对象带有一个callee属性，返回它所对应的原函数
* arguments转换为数组

		var args = Array.prototype.slice.call(arguments);
		
		// 或者
		var args = [];
		for (var i = 0; i < arguments.length; i++) {
		  args.push(arguments[i]);
		}
* 函数闭包的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在，闭包的另一个用处，是封装对象的私有属性和私有方法
* 不能滥用闭包，否则会造成网页的性能问题
* 立即调用的函数表达式（IIFE），通常情况下，只对匿名函数使用这种“立即执行的函数表达式”。它的目的有两个：一是不必为函数命名，避免了污染全局变量；二是 IIFE 内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量。
		
		//两种写法最后的分号都是必须的。如果省略分号，遇到连着两个 IIFE，可能就会报错。
		(function(){ /* code */ }());
		// 或者
		(function(){ /* code */ })();
* eval命令接受一个字符串作为参数，并将这个字符串当作语句执行；eval没有自己的作用域，都在当前作用域内执行，因此可能会修改当前作用域的变量的值，造成安全问题，为了防止这种风险，JavaScript 规定，如果使用严格模式，eval内部声明的变量，不会影响到外部作用域，不过，即使在严格模式下，eval依然可以读写当前作用域的变量。
* eval的别名调用：为了保证eval的别名不影响代码优化，JavaScript 的标准规定，凡是使用别名执行eval，eval内部一律是全局作用域

		//eval的别名调用，作用域都是全局作用域
		eval.call(null, '...')
		window.eval('...')
		(1, eval)('...')
		(eval, eval)('...')

## 3.1运算符

###### 指数运算符 x**y => x^y

###### 数字与字串相加会自动转化为相应的字串再相加

	1 + 'a' // "1a"
	false + 'a' // "falsea"
	true + true //2
	false + false //0
	3 + 4 + 'a' //'7a'

###### 除了加法运算符，其他算术运算符（比如减法、除法和乘法）都不会发生重载。它们的规则是：所有运算子一律转为数值，再进行相应的数学运算。

	1 - '2' // -1
	1 * '2' // 2
	1 / '2' // 0.5

###### JavaScript 引擎内部首先比较首字符的 Unicode 码点。如果相等，再比较第二个字符的 Unicode 码点，以此类推

###### 两个复合类型（对象、数组、函数）的数据比较是否相等时，不是比较它们的值是否相等，而是比较它们是否指向同一个地址

***注意，对于两个对象的比较，相等运算符比较的是地址，而大于或小于运算符比较的是值***

	undefined === null
	//false
	undefined == null
	//true

###### 以下取反为true：

- undefined
- null
- false
- 0
- NaN
- 空字符串（''）

###### 且运算符（&&）往往用于多个表达式的求值。它的运算规则是：如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值

###### 或运算符（||）也用于多个表达式的求值。它的运算规则是：如果第一个运算子的布尔值为true，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为false，则返回第二个运算子的值

###### 虽然在 JavaScript 内部，数值都是以64位浮点数的形式储存，但是做位运算的时候，是以32位带符号的整数进行运算的，并且返回值也是一个32位带符号的整数

###### 位运算：

- 二进制或运算符（or）：符号为|，表示若两个二进制位都为0，则结果为0，否则为1。
- 二进制与运算符（and）：符号为&，表示若两个二进制位都为1，则结果为1，否则为0。
- 二进制否运算符（not）：符号为~，表示对一个二进制位取反。
- 异或运算符（xor）：符号为^，表示若两个二进制位不相同，则结果为1，否则为0。
- 左移运算符（left shift）：符号为<<，详见下文解释。
- 右移运算符（right shift）：符号为>>，详见下文解释。
- 带符号位的右移运算符（zero filled right shift）：符号为>>>
- **位运算符只对整数起作用，可以借此达到取整的效果**

###### 使用二进制否运算取整，是所有取整方法中最快的一种

###### 对字符串进行二进制否运算，JavaScript 引擎会先调用Number函数，将字符串转为数值

###### 带符号位的右移运算符（>>>）表示将一个数的二进制形式向右移动，包括符号位也参与移动，头部补0。所以，该运算总是得到正值。对于正数，该运算的结果与右移运算符（>>）完全一致，区别主要在于负数

###### 查看一个负整数在计算机内部的储存形式，最快的方法就是使用这个运算符‘>>>’,这个运算实际上将一个值转为32位无符号整数!

	-1 >>> 0 // 4294967295

***编程中熟练使用位运算将大大提高运算性能***

###### void

	void 0 // undefined
	void(0) // undefined

	//使用void避免表单跳转
	<a href="http://example.com" onclick="f(); return false;">点击</a>
	<a href="javascript: void(f())">文字</a>
	<a href="javascript: void(document.form.submit())">
	  提交
	</a>

###### 逗号运算符用于对两个表达式求值，并返回后一个表达式的值

## 4.1数据类型转换

###### 强制类型转换：Number(), String(), Boolean()

###### String()可以将任意类型的值转化成字符串

- 数值：转为相应的字符串。
- 字符串：转换后还是原来的值。
- 布尔值：true转为字符串"true"，false转为字符串"false"。
- undefined：转为字符串"undefined"。
- null：转为字符串"null"。
- 对象：返回一个类型字符串
- 数组：返回该数组的字符串形式

###### Boolean()函数可以将任意类型的值转为布尔值

###### 自动转换的时机：

- 不同类型的数据互相运算
- 对非布尔值类型的数据求布尔值
- 对非数值类型的值使用一元运算符（即+和-）

## 5.1错误处理机制

###### JavaScript 原生提供Error构造函数，所有抛出的错误都是这个构造函数的实例

	var err = new Error('出错了');
	err.message // "出错了"

- message：错误提示信息（标准属性）
- name：错误名称（非标准属性）
- stack：错误的堆栈（非标准属性）

## 5.2原生错误类型

- SyntaxError对象是解析代码时发生的语法错误
- ReferenceError对象是引用一个不存在的变量时发生的错误，另一种触发场景是，将一个值分配给无法分配的对象，比如对函数的运行结果或者this赋值
- RangeError对象是一个值超出有效范围时发生的错误，一是数组长度为负数，二是Number对象的方法参数超出范围，以及函数堆栈超过最大值
- TypeError对象是变量或参数不是预期类型时发生的错误
- URIError对象是 URI 相关函数的参数不正确时抛出的错误，主要涉及encodeURI()、decodeURI()、encodeURIComponent()、decodeURIComponent()、escape()和unescape()这六个函数
- EvalError 对象，eval函数没有被正确执行时，会抛出EvalError错误。该错误类型已经不再使用了，只是为了保证与以前代码兼容，才继续保留
#
	var err1 = new Error('出错了！');
	var err2 = new RangeError('出错了，变量超出有效范围！');
	var err3 = new TypeError('出错了，变量类型无效！');
	
	err1.message // "出错了！"
	err2.message // "出错了，变量超出有效范围！"
	err3.message // "出错了，变量类型无效！"

## 5.3自定义错误

	function UserError(message) {
	  this.message = message || '默认信息';
	  this.name = 'UserError';
	}
	
	UserError.prototype = new Error();
	UserError.prototype.constructor = UserError;
	
	new UserError('这是自定义的错误！');

## 5.4try，catch，throw，finally

## 5.5编程风格

- JavaScript 会自动添加句末的分号
- JavaScript 允许省略行尾的分号
- 下面这种写法就可以避免与其他脚本合并时，排在前面的脚本最后一行语句没有分号，导致运行出错的问题
  
		;var a = 1;
		// ...

## 6.1console对象

###### console对象是ECMAScript中的原生对象

###### console.log()占位符

	%s 字符串
	%d 整数
	%i 整数
	%f 浮点数
	%o 对象的链接
	%c CSS 格式字符串

###### console.info()是console.log方法的别名，用法完全一样。只不过console.info方法会在输出信息的前面，加上一个蓝色图标

###### console.debug方法与console.log方法类似，会在控制台输出调试信息。但是，默认情况下，console.debug输出的信息不会显示，只有在打开显示级别在verbose的情况下，才会显示

###### console.warn()，console.error()warn方法和error方法也是在控制台输出信息，它们与log方法的不同之处在于，warn方法输出信息时，在最前面加一个黄色三角，表示警告；error方法输出信息时，在最前面加一个红色的叉，表示出错。同时，还会高亮显示输出文字和错误发生的堆栈。其他方面都一样

###### 对于某些复合类型的数据，console.table方法可以将其转为表格显示

###### console.count(),count方法用于计数，输出它被调用了多少次,该方法可以接受一个字符串作为参数，作为标签，对执行次数进行分类

###### console.dir(),dir方法用来对一个对象进行检查（inspect），并以易于阅读和打印的格式显示

###### console.dirxml(),dirxml方法主要用于以目录树的形式，显示 DOM 节点

###### console.assert方法主要用于程序运行过程中，进行条件判断，如果不满足条件，就显示一个错误，但不会中断程序执行。这样就相当于提示用户，内部状态不正确

###### console.time()，console.timeEnd()这两个方法用于计时，可以算出一个操作所花费的准确时间

###### console.group和console.groupEnd这两个方法用于将显示的信息分组。它只在输出大量信息时有用，分在一组的信息，可以用鼠标折叠/展开;console.groupCollapsed方法与console.group方法很类似，唯一的区别是该组的内容，在第一次显示时是收起的（collapsed），而不是展开的

###### console.trace方法显示当前执行的代码在堆栈中的调用路径

###### console.clear方法用于清除当前控制台的所有输出，将光标回置到第一行。如果用户选中了控制台的“Preserve log”选项，console.clear方法将不起作用

## 6.2[控制台API](https://wangdoc.com/javascript/features/console.html#%E6%8E%A7%E5%88%B6%E5%8F%B0%E5%91%BD%E4%BB%A4%E8%A1%8C-api)

## 7.1Object对象

###### JavaScript 的所有其他对象都继承自Object对象，即那些对象都是Object的实例

###### Object对象的原生方法分成两类：Object本身的方法与Object的实例方法

	//本身的方法
	Object.print = function (o) { console.log(o) };
	//实例方法
	Object.prototype.print = function () {
	  console.log(this);
	};

###### Object本身是一个函数，可以当作工具方法使用，将`任意值`转为对象。这个方法常用于保证某个值一定是对象。如果参数为空（或者为undefined和null），Object()返回一个空对象

###### instanceof运算符用来验证，一个对象是否为指定的构造函数的实例

###### Object还是一个构造函数，Object构造函数的用法与工具方法很相似，几乎一模一样。使用时，可以接受一个参数，如果该参数是一个对象，则直接返回这个对象；如果是一个原始类型的值，则返回该值对应的包装对象。虽然用法相似，但是Object(value)与new Object(value)两者的语义是不同的，Object(value)表示将value转成一个对象，new Object(value)则表示新生成一个对象，它的值是value

###### Object的静态方法

- Object.keys方法的参数是一个对象，返回一个数组。该数组的成员都是该对象自身的（而不是继承的）所有属性名
- Object.getOwnPropertyNames方法与Object.keys类似，也是接受一个对象作为参数，返回一个数组，包含了该对象自身的所有属性名
- **对于一般的对象来说，Object.keys()和Object.getOwnPropertyNames()返回的结果是一样的。只有涉及不可枚举属性时，才会有不一样的结果。Object.keys方法只返回可枚举的属性，Object.getOwnPropertyNames方法还返回不可枚举的属性名**
#

- 属性相关
	- Object.getOwnPropertyDescriptor()：获取某个属性的描述对象。
	- Object.defineProperty()：通过描述对象，定义某个属性。
	- Object.defineProperties()：通过描述对象，定义多个属性。

- 状态相关
	- Object.preventExtensions()：防止对象扩展。
	- Object.isExtensible()：判断对象是否可扩展。
	- Object.seal()：禁止对象配置。
	- Object.isSealed()：判断一个对象是否可配置。
	- Object.freeze()：冻结一个对象。
	- Object.isFrozen()：判断一个对象是否被冻结。
- 原型链相关
	- Object.create()：该方法可以指定原型对象和属性，返回一个新的对象。
	- Object.getPrototypeOf()：获取对象的Prototype对象。

###### Object实例方法

- Object.prototype.valueOf()：返回当前对象对应的值。
- Object.prototype.toString()：返回当前对象对应的字符串形式。
- Object.prototype.toLocaleString()：返回当前对象对应的本地字符串形式。
- Object.prototype.hasOwnProperty()：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
- Object.prototype.isPrototypeOf()：判断当前对象是否为另一个对象的原型。
- Object.prototype.propertyIsEnumerable()：判断某个属性是否可枚举。

###### 使用同toString()实例方法判断数据类型

    Object.prototype.toString.call(value)
	//第二个表示该值的构造函数
	/*
	数值：返回[object Number]。
    字符串：返回[object String]。
    布尔值：返回[object Boolean]。
    undefined：返回[object Undefined]。
    null：返回[object Null]。
    数组：返回[object Array]。
    arguments 对象：返回[object Arguments]。
    函数：返回[object Function]。
    Error 对象：返回[object Error]。
    Date 对象：返回[object Date]。
    RegExp 对象：返回[object RegExp]。
    其他对象：返回[object Object]。
	*/

###### Object.prototype.toLocaleString方法与toString的返回结果相同，也是返回一个值的字符串形式,这个方法的主要作用是留出一个接口，让各种不同的对象实现自己版本的toLocaleString，用来返回针对某些地域的特定的值。
	var person = {
	  toString(): function () {
	return 'Henry Norman Bethune';
	  },
	  toLocaleString(): function () {
	return '白求恩';
	  }
	};
	
	person.toString() // Henry Norman Bethune
	person.toLocaleString() // 白求恩

###### Object.prototype.hasOwnProperty方法接受一个字符串作为参数，返回一个布尔值，表示该实例对象自身是否具有该属性,不包含继承的属性

#### 属性描述对象(包含6个元属性)

- value，value是该属性的属性值，默认为undefined。
- writable，writable是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为true。
- enumerable，enumerable是一个布尔值，表示该属性是否可遍历，默认为true。如果设为false，会使得某些操作（比如for...in循环、Object.keys()）跳过该属性。
- configurable，configurable是一个布尔值，表示可配置性，默认为true。如果设为false，将阻止某些操作改写该属性，比如无法删除该属性，也不得改变该属性的属性描述对象（value属性除外）。也就是说，configurable属性控制了属性描述对象的可写性。
- get，get是一个函数，表示该属性的取值函数（getter），默认为undefined。
- set，set是一个函数，表示该属性的存值函数（setter），默认为undefined。

###### Object.getOwnPropertyDescriptor()方法可以获取属性描述对象。它的第一个参数是目标对象，第二个参数是一个字符串，对应目标对象的某个属性名（只能用于对象自身的属性，继承无效）

###### Object.getOwnPropertyNames方法返回一个数组，成员是参数对象自身的全部属性的属性名，不管该属性是否可遍历（Object.prototype也是一个对象，所有实例对象都会继承它，它自身的属性都是不可遍历的）

###### Object.defineProperty()方法允许通过属性描述对象，定义或修改一个属性，然后返回修改后的对象，如果属性已经存在，Object.defineProperty()方法相当于更新该属性的属性描述对象；Object.defineProperties()可以同时修改一个对象的多个属性

    var obj = Object.defineProperties({}, {
      p1: { value: 123, enumerable: true },
      p2: { value: 'abc', enumerable: true },
      p3: { get: function () { return this.p1 + this.p2 },
    enumerable:true,
    configurable:true
      }
    });
    
    obj.p1 // 123
    obj.p2 // "abc"
    obj.p3 // "123abc"

**注意，一旦定义了取值函数get（或存值函数set），就不能将writable属性设为true，或者同时定义value属性**

**Object.defineProperty()和Object.defineProperties()参数里面的属性描述对象，writable、configurable、enumerable这三个属性的默认值都为false**

###### 实例对象的propertyIsEnumerable()方法返回一个布尔值，用来判断某个属性是否可遍历。注意，这个方法只能用于判断对象自身的属性，对于继承的属性一律返回false

###### 可遍历与for..in的使用有关，只有可遍历的属性，才会被for...in循环遍历，同时像toString这一类实例对象继承的原生属性，都是不可遍历的，这样就保证了for...in循环的可用性

###### JSON.stringify方法会排除enumerable为false的属性

###### configurable(可配置性）返回一个布尔值，决定了是否可以修改属性描述对象。也就是说，configurable为false时，writable、enumerable和configurable都不能被修改了（writable只有在false改为true会报错，true改为false是允许的；另外，value在writable为true时仍可以被修改，准确点说，只要writable和configurable有一个为true，就允许改动）

###### 访问属性get 和 set

#### 对象的拷贝

	var extend = function (to, from) {
	for (var property in from) {
	if (!from.hasOwnProperty(property)) continue;
	Object.defineProperty(
	  to,
	  property,
	  Object.getOwnPropertyDescriptor(from, property)
	);
	}
	
	return to;
	}
	
	extend({}, { get a(){ return 1 } })

#### 有时需要冻结对象的读写状态，防止对象被改变。JavaScript 提供了三种冻结方法，最弱的一种是Object.preventExtensions，其次是Object.seal，最强的是Object.freeze【控制对象状态】

- Object.preventExtensions方法可以使得一个对象无法再添加新的属性
- Object.isExtensible方法用于检查一个对象是否使用了Object.preventExtensions方法
- Object.seal方法使得一个对象既无法添加新属性，也无法删除旧属性（Object.seal实质是把属性描述对象的configurable属性设为false，因此属性描述对象不再能改变了）
- Object.isSealed方法用于检查一个对象是否使用了Object.seal方法
- Object.freeze方法可以使得一个对象无法添加新属性、无法删除旧属性、也无法改变属性的值，使得这个对象实际上变成了常量
- Object.isFrozen方法用于检查一个对象是否使用了Object.freeze方法

**注意：可以通过改变原型对象，来为对象增加属性**

	var obj = new Object();
	Object.preventExtensions(obj);
	
	var proto = Object.getPrototypeOf(obj);
	proto.t = 'hello';
	obj.t
	// hello

	//一种解决方案是，把obj的原型也冻结住
	var obj = new Object();
	Object.preventExtensions(obj);
	
	var proto = Object.getPrototypeOf(obj);
	Object.preventExtensions(proto);
	
	proto.t = 'hello';
	obj.t // undefined

**另外，如果冻结的属性是一个对象，那么实际上冻结的时属性的指向，仍然可以修改属性对应的对象的属性**

## 7.2Array对象