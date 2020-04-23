# 前端模块化理解： CommonJS、AMD、CMD、ES6 模块

### 前言

随着前端 js 的发展，模块化成为了必然趋势，今天整理一下模块化的几个规范： CommonJS, AMD, CMD, ES6 规范

##### 模块化的好处

- 避免命名冲突(减少命名空间污染)
- 更高复用性
- 高可维护性
- 更好的分离, 按需加载

### CommonJS

Node 采用了这种模块规范。由于 Node.js 主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以 CommonJS 规范比较适用。每个文件是一个模块，通过`require`方法来`同步`加载要依赖的模块，然后通过`extports`或则`module.exports`来导出需要暴露的接口。

```js
// a.js
var a = 5;
function add(value) {
  return value + a;
}

module.exports.a = a;
module.exports.addd = add;

// b.js
var a = require('./a.js');

console.log(a.addd(3)); // 8
console.log(a.a); // 5
```

##### 特点

- 所有代码都运行在模块作用域，不会污染全局作用域。
- 模块同步加载，只有加载完成，才能执行后面的操作。
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
- CommonJS 模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。除非输出的是引用类型的值。

##### 缺点

CommonJS 使用同步的方式加载模块，但是在浏览器端，限于网络原因，CommonJS 不适合浏览器端模块加载，更合理的方案是使用异步加载，比如 AMD

### AMD

AMD 即 Asynchronous Module Definition ，意思就是 '异步模块定义'，是 RequireJS 在推广过程中对模块定义的规范化产出。

AMD 采用异步的方式加载模块，允许指定回调函数，模块的加载不会影响到后面语句的执行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

用 require.js 实现 AMD 规范的模块化: 用`require.config()`指定引用路径等，用`define()`定义模块，用`require()`加载模块。

```js
// html 引用文件 data-main 不可少
// <script data-main="./common" src="./require.js"></script>

// commin.js
// 使用 require.config 指定模块路径和引用名
require.config({
  baseUrl: 'lib',
  paths: {
    jquery: 'jquery',
    app: '../',
  },
});

// start loading the main app file.
// 加载main主文件
require(['app/main']);

// math.js
// define 定义模块
define(function() {
  var basic = 5;
  var add = function(val) {
    return num + val;
  };
  return {
    basic: basic,
    add: add,
  };
});

// main.js
// require 引用模块
require(['app/math', 'jquery'], function(math, $) {
  console.log(math.basic); // 5
});
```

##### 缺点

使用 require.js 时，需要提前记载所有的依赖模块，然后才能使用，而不是按需加载。

### CMD

CMD 规范专门用于浏览器端，模块的加载是异步的，模块使用时才会加载执行。CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。

CMD 和 AMD 类似，不同点在于：`AMD推崇依赖前置、提前执行，CMD推崇依赖就近、延迟执行`。

```js
/** AMD写法 **/
define(['a', 'b', 'c', 'd', 'e', 'f'], function(a, b, c, d, e, f) {
  // 等于在最前面声明并初始化了要用到的所有模块
  a.doSomething();
  if (false) {
    // 即便没用到某个模块 b，但 b 还是提前执行了
    b.doSomething();
  }
});

/** CMD写法 **/
define(function(require, exports, module) {
  var a = require('./a'); //在需要时申明
  a.doSomething();
  if (false) {
    var b = require('./b');
    b.doSomething();
  }
});

/** sea.js **/
// 定义模块 math.js
define(function(require, exports, module) {
  var $ = require('jquery.js');
  var add = function(a, b) {
    return a + b;
  };
  exports.add = add;
});
// 加载模块
seajs.use(['math.js'], function(math) {
  var sum = math.add(1 + 2);
});
```

### ES6 模块化

ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

```js
/** 定义模块 math.js **/
var basic = 0;
var add = function(a, b) {
  return a + b;
};
export { basic, add };

/** 引用模块 **/
import { basic, add } from './math';
function test(ele) {
  ele.textContent = add(99 + basic);
}
```

如上例所示，使用 import 命令的时候，用户需要知道所要加载的变量名或函数名。其实 ES6 还提供了 export default 命令，为模块指定默认输出，对应的 import 语句不需要使用大括号。这也更趋近于 ADM 的引用写法。

```js
/** export default **/
//定义输出
export default { basicNum, add };
//引入
import math from './math';
function test(ele) {
  ele.textContent = math.add(99 + math.basicNum);
}
```

#### ES6 模块与 CommonJS 模块的差异

1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

- CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。
- ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令 import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。换句话说，ES6 的 import 有点像 Unix 系统的“符号连接”，原始值变了，import 加载的值也会跟着变。因此，ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。

例子:

```js
// lib.js
export let counter = 3;
export function incCounter() {
  counter++;
}

// main.js
import { counter, incCounter } from './lib';
console.log(counter); // 3
incCounter();
console.log(counter); // 4
```

2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

- 运行时加载: CommonJS 模块就是对象；即在输入时是先加载整个模块，生成一个对象，然后再从这个对象上面读取方法，这种加载称为“运行时加载”。

- 编译时加载: ES6 模块不是对象，而是通过 export 命令显式指定输出的代码，import 时采用静态命令的形式。即在 import 时可以指定加载某个输出值，而不是加载整个模块，这种加载称为“编译时加载”。

CommonJS 加载的是一个对象（即 module.exports 属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

Tips：本文只是自己的一点笔记，方便自己记忆和理解。

#### 参考

[ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/module-loader)

[前端模块化：CommonJS,AMD,CMD,ES6 - 掘金](https://juejin.im/post/5aaa37c8f265da23945f365c)
