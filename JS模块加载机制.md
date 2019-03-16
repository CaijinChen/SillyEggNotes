## CommonJS
### 总结自[CommonJS参考教程 · by · 阮一峰](http://javascript.ruanyifeng.com/nodejs/module.html#toc0)
### 1.概述
Node应用采用Commonjs规范组织自己的模块。每个文件就是一个模块，有自己的作用域。在一个文件中定义的变量、函数、类，都是私有的，对其他文件不可见。

Node环境下，全局对象global可以帮助在多个文件间共享变量，将该变量设置为global的属性即可。

module变量代表当前模块，它是一个对象，它的exports属性是对外的接口。加载某个模块的时候实质上就是加载module.exports属性。

require用于加载模块

特点：

- 所有代码都运行在模块作用域下，不会污染全局
- 模块可加载多次，但是只会在第一次加载时运行一次，而后运行结果被缓存，以后再次加载就会直接读取缓存结果。要想让模块再次运行，需要清除缓存
- 模块加载的顺序遵循在代码中出现的次序
- 同步加载，只有加载完才会继续执行后面的语句

### 2.module对象

每个模块内部都有一个module对象。它的属性有：

- module.id 模块的标识符， 
- module.exports 模块对外的输出值，
- module.parent 返回一个对象，表示调用该模块的模块
- module.filename 模块的文件名，带有绝对路径
- module.loaded 表示模块是否加载完成
- module.children 返回一个数组，表示该模块要用到的模块
- module.paths 加载模块时访问的路径集合

### 3.module.exports属性

表示当前模块对外输出的接口，其他文件加载该模块就是在读取module.exports变量

exports变量，Node为每个模块提供指向mudule.exports的exports变量，这等同于在每个模块都执行了这么一行命令：

	var exports = module.exports;

可以像exports对象添加方法属性，但不可直接赋值，因为这样会改变exports变量的指向：

	//这种写法会使exports变量指向另一个对象
	exports = {name: "tom"};

如果在模块中同时使用exports、module.exports,需要注意直接对module.exports赋一个单一值会覆盖掉exports对象

	//下面的属性property1不会被输出
	exports.property1 = "this is not work";
	module.exports = 'hello world';

### 4.require

require用于加载模块文件。它会读入并执行一个js文件，然后返回该模块的exports对象。

文件加载规则：

默认后缀名为`.js`,所以js文件后缀可以省略

1. 如果参数字串以‘/’开头，则表示加载的是一个位于绝对路径的模块文件比如： require('/xx/xx/xx.js')
2. 如果以‘./’开头会加自相对路径上的文件比如：require('./xxx.js')
3. 如果不以‘./’也不以‘/’开头则表示加载默认提供的核心模块（位于Node的系统安装目录），或者一个位于各级node_modules目录的已安装模块（全局安装或局部安装）比如：require('xxx.js'),会依次寻找：
	#
		/usr/local/lib/node/xxx.js
		/home/user/projects/node_modules/xxx.js
		/home/user/node_modules/xxx.js
		/home/node_modules/xxx.js
		/node_modules/xxx.js
4. 如果参数字符串不以“./”或“/”开头，而是一个路径，比如require('example-module/path/to/file')，则将先找到example-module的位置，然后再以它为参数，找到后续路径。
5. 如果指定的模块文件没有发现，Node会尝试为文件名添加.js、.json、.node后，再去搜索。.js件会以文本格式的JavaScript脚本文件解析，.json文件会以JSON格式的文本文件解析，.node文件会以编译后的二进制文件解析。
6. 如果想得到require命令加载的确切文件名，使用require.resolve()方法。

目录加载规则：

通常，文件数比较多时，可以把文件放在目录中，并为该目录设置一个入口文件以加载整个目录。

在目录中设置package.json文件，并且将入口文件写入main字段中。当require发现参数指向一个目录后会自动查看目录中的package.json文件，然后加载main字段指定的入口文件，如果没有package.json文件，则会加载该目录下的index.js或index.node文件

环境变量NODE_PATH

Node执行脚本时会先查看环境变量NODE_PATH。在其他位置找不到指定模块时Node会去NODE_PATH中指定的路径中寻找

NODE_PATH是历史遗留下来的路径解决方案，通常不应该使用，而因利用好node_modules目录机制

模块的循环加载

A加载B，B加载A，则B将加载A的不完整版本

require方法有一个main属性，可以用来判断模块是直接执行，还是被调用执行。直接执行时（也即运行:node xxx.js），require.main属性指向模块本省

	require.main === module//true

### 5.模块加载机制

CommonJS模块加载输出的是被输出值的拷贝，所以一旦输出一个值，模块内部的变化就影响不到这个值。

require不是一个全局变量，实际上exports类似，它指向当前模块的module.require命令，后者又调用Node的内部命令Module.load

	Module._load = function(request, parent, isMain) {
	  // 1. 检查 Module._cache，是否缓存之中有指定模块
	  // 2. 如果缓存之中没有，就创建一个新的Module实例
	  // 3. 将它保存到缓存
	  // 4. 使用 module.load() 加载指定的模块文件，
	  //    读取文件内容之后，使用 module.compile() 执行文件代码
	  // 5. 如果加载/解析过程报错，就从缓存删除该模块
	  // 6. 返回该模块的 module.exports
	};

	//上面的第4步，采用module.compile()执行指定模块的脚本，逻辑如下。
	Module.prototype._compile = function(content, filename) {
	  // 1. 生成一个require函数，指向module.require
	  // 2. 加载其他辅助方法到require
	  // 3. 将文件内容放到一个函数之中，该函数可调用 require
	  // 4. 执行该函数
	};

	//上面的第1步和第2步，require函数及其辅助方法主要如下。
	require(): 加载外部模块
	require.resolve()：将模块名解析到一个绝对路径
	require.main：指向主模块
	require.cache：指向所有缓存的模块
	require.extensions：根据文件的后缀名，调用不同的执行函数

	//一旦require函数准备完毕，整个所要加载的脚本内容，就被放到一个新的函数之中，这样可以避免污染全局环境。该函数的参数包括require、module、exports，以及其他一些参数。
	(function (exports, require, module, __filename, __dirname) {
	  // YOUR CODE INJECTED HERE!
	});

	Module._compile方法是同步执行的，所以Module._load要等它执行完成，才会向用户返回module.exports的值。








