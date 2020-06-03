---
title: JavaScript模块化
author: 刘剑
authorLink: http://www.github.com/liujian518
excerpt: JavaScript模块化的发展及规范
thumbnail: 2020/05/29/JavaScript模块化/module4.jpg
categories:
  - - JavaScript
    - 模块化(Module)
tags:
  - Module
toc: true
date: 2020-05-29 21:52:32
updated: 2020-05-29 21:52:32
---
# 模块化的理解
## 1、什么是模块化
- 将一个复杂的程序，依据一定的规则(规范)封装成一个或多个块(文件), 并进行组合在一起。
- 块的内部数据与实现是私有的, 只是向外部暴露一些接口(方法)与外部其它模块通信。

## 2、模块化的进化过程

**无模块时代**

在ajax还未提出之前，js还只是用来在网页上进行表单校验、提交，对DOM渲染操作。
```javascript
var str,num;
//......
function submit(){
	str = document.getElementById("xx").value;
	if(str){
     //......
	}
	else{
	 //......
	}
	num = 1;
	for(var i=0; i<10; i++){
		num++
	     //......
	}
	//......
	form.submit()
}
```
```javascript
<script type="text/javascript" src="a.js"></script>
<script type="text/javascript" src="b.js"></script>
<script type="text/javascript" src="c.js"></script>
<script type="text/javascript" src="main.js"></script>
```
缺点：
 - 全局变量污染
 - 函数命名冲突
 - 文件依赖顺序
 
**模块雏形时代**

2006年，ajax的概念被提出，前端拥有了主动向服务端发送请求并操作返回数据的能力，传统的网页向“富客户端”发展，出现了简单的功能对象封装。

- **namespace模式**
```javascript
//模块js
var myModule = {
	first_name: 'www.',
	second_name: 'baidu.com',
	getFullName: function() {
		return this.first_name + this.second_name;
	}
}
```
```javascript
//调用js
console.log(myModule.getFullName());
myModule.first_name = 'img.';
console.log(myModule.getFullName());
```
优点: 减少了全局变量，解决命名冲突
缺点: 数据不安全(外部可以直接修改模块内部的数据)，模块名称会暴露在全局，存在命名冲突，依赖顺序问题

- **自执行匿名函数（闭包）模式**

```javascript
//模块js
(function (window) {
    let _moduleName = 'module';
    function setModuleName(name) {
        _moduleName = name;
    }
    function getModuleName() {
        return _moduleName;
    }
    window.moduleA = { setModuleName, getModuleName }
})(window)
```
```javascript
//调用js
moduleA.setModuleName('html-module');
console.log(moduleA.getModuleName());
console.log(moduleA._moduleName);//模块不暴露，无法访问模块内属性方法
```
优点：变量、方法全局隐藏，模块私有化
缺点：模块名称会暴露在全局，存在命名冲突，依赖顺序问题

## 3、面临的问题
从以上的尝试中，可以归纳出js模块化需要解决那些问题：
 -  如何安全的包装一个模块的代码？（不污染模块外的任何代码）
 -  如何唯一标识一个模块？
 -  如何优雅的把模块的API暴漏出去？（不能增加全局变量）
 -  如何方便的使用所依赖的模块？
 
# 模块化的规范
## 1、CommonJS
2009年Nodejs发布，采用 CommonJS 模块规范。

特点：
- 每个文件都是一个模块实例，代码运行在模块作用域，不会污染全局作用域。
- 文件内通过require对象引入指定模块，通过exports对象来向往暴漏API，文件内定义的变量、函数，都是私有的，对其他文件不可见。
- 每个模块加载一次之后就会被缓存。
- 所有文件加载均是同步完成，加载的顺序，按照其在代码中出现的顺序。
- 模块输出的是一个值的拷贝，模块内部的变化不会影响该值。

```javascript
//模块js
let _moduleName = 'module';
function setModuleName(name) {
    _moduleName = name;
}
function getModuleName() {
    return _moduleName ;
}
module.exports = { setModuleName, getModuleName }
```
```javascript
//调用js
import { getModuleName, setModuleName } from './es6.module';
setModuleName("es6 Module");
console.log(getModuleName());
```
缺点：模块同步加载，资源消耗和等待时间，适用于服务器编程
## 2、AMD/RequireJS
Commonjs局限性很明显：

基于Node原生api在服务端可以实现模块同步加载，但是仅仅局限于服务端，客户端如果同步加载依赖的话时间消耗非常大，所以需要一个在客户端上基于Commonjs但是对于加载模块做改进的方案，于是AMD规范诞生了。

AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到所有依赖加载完成之后（依赖前置），这个回调函数才会运行。

RequireJS是一个工具库，主要用于客户端的模块管理。它的模块管理遵守AMD规范，RequireJS的基本思想是，通过define方法将代码定义为模块，通过require方法实现代码的模块加载。
```javascript
// module1.js 定义没有依赖的模块
define(function () {
  let _moduleName = 'module';
  function getName() {
    return _moduleName;
  }
  return { getName } // 暴露模块
})
```
```javascript
// module2.js 定义有依赖的模块
define(['module1'], function (module1) {
  let _firstName = 'AMD'
  function getFullName() {
    return _firstName + ' ' + module1.getName();
  }
  function setFirstName(name) {
    _firstName = name;
  }
  // 暴露模块
  return { _firstName, getFullName, setFirstName }
})
```
```javascript
//mian.js
require.config({
  paths: {
    module1: './modules/module1',
    module2: './modules/module2',
    // 第三方库模块
    jquery: './libs/jquery.min'
  }
})
require(['module2','jquery'], function(module2,jquery) {
  console.log(module2.getFullName());
  module2.setFirstName('AMD-AMD');
  console.log(module2.getFullName());
  console.log(module2._firstName);
  jquery('#moduleId').html("<i>My name is jquery-module</i>");
})
```
```javascript
//html中引入工具库，并定义js主文件
<script data-main="./main" src="./libs/require.js"></script>
```
特点：浏览器直接运行无需编译，异步加载，依赖关系清晰

## 3、CMD/SeaJS
CMD规范专门用于浏览器端，同样是受到Commonjs的启发，国内（阿里）诞生了一个CMD（Common Module Definition）规范。该规范借鉴了Commonjs的规范与AMD规范，在两者基础上做了改进。

与AMD相比非常类似，CMD规范（2011）具有以下特点：
- define定义模块，require加载模块，exports暴露变量。
- 不同于AMD的依赖前置，CMD推崇依赖就近（需要的时候再加载）
- 推崇api功能单一，一个模块干一件事。

SeaJs是CMD规范的实现，跟RequireJs类似，CMD是SeaJs推广过程中诞生的规范。CMD借鉴了很多AMD和Commonjs优点。

```javascript
//module.1
define(function (require, exports, module) {
  module.exports = {
    msg: 'I am module1'
  }
})
```

```javascript
//module.2
define(function (require, exports, module) {
	var module2 = require('./module1')
    function show() {
        console.log('同步引入依赖模块1 ' + module2.msg)
    }
    exports.showModule = show
})
```

```javascript
//main.js
define(function (require) {
  var m2 = require('./modules/module2')
  m2.showModule();
})

```

```javascript
//html中引入工具库，并定义js主文件
<script type="text/javascript" src="./libs/sea.js"></script>
<script type="text/javascript">
  seajs.use('./main')
</script>
```
**AMD、CMD区别**
- AMD 推崇依赖前置
- CMD 推崇依赖就近

```javascript
//AMD
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
	a.doSomething()
	...
	// 此处略去 100 行
	b.doSomething()
	...
})
```

```javascript
//CMD
define(function(require, exports, module) {
	var a = require('./a')
	a.doSomething()
	...
	// 此处略去 100 行
	var b = require('./b') // 依赖可以就近书写
	b.doSomething()
	...
}
```

## 4、ES6
2015年，ES6规范中，终于将模块化纳入JavaScript标准，从此js模块化被ECMA官方扶正，也是后来js的标准。
ES6中的模块化在CommonJS的基础上有所不同，关键字有import，export，default，as，from。

```javascript
//模块js
let _moduleName = 'module';
function setModuleName(name) {
    _moduleName = name;
}
function getModuleName() {
    return _moduleName;
}
export { setModuleName, getModuleName }
```

```javascript
//调用js
import { getModuleName,setModuleName } from './es6.module';
setModuleName("es6 Module");
console.log(getModuleName());
```
**CommonJS和ES6区别**
 -  CommonJS 模块输出的是一个值的拷贝，即原来模块中的值改变不会影响已经加载的该值。
 ES6 模块输出的是值的只读引用，模块内值改变，引用也改变。
 - CommonJS 模块是运行时加载，加载的是整个模块，即将所有的接口全部加载进来。
 ES6 模块是编译时输出接口，可以单独加载其中的某个接口。

# 总结
 - CommonJS规范主要用于服务端编程，加载模块是同步的，不适合在浏览器环境，存在阻塞加载，浏览器资源是异步加载的，因此有了AMD、CMD解决方案。
 - AMD规范在浏览器环境中异步加载模块，而且可以并行加载多个模块。
 - CMD规范与AMD规范很相似，都用于浏览器编程，依赖就近，代码更简单。
 - ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。