# JavaScript对象 - 面向对象

JavaScript 标准对基于对象的定义，语言和宿主的基础设置由对象来提供，并且 JavaScript 程序即是一系列互相通讯的对象集合。

本章中，会尝试理解面向对象和 JavaScript 中的面向对象究竟是什么。

### 什么是面向对象

先说说什么是对象。
`Object（对象）`在英文中，是一切事物的总称，这和面向对象编程的抽象思维有互通之处。

中文的“对象”更多是把它当做一个专业名词来理解。

无论如何，对象并不是计算机领域凭空造出来的概念，它是顺着人类思维模式产生的一种抽象。

在《面向对象分析和设计》这本书中，作者做了总结，他认为，从人来的认知角度来说，对象应该是下列事物之一：

1. 一个可以触摸或者可以看见的东西；
2. 人的智力可以理解的东西；
3. 可以知道思考或者行动（进行想象或者施加动作）的东西。

了对象的自然定义后，我们就可以描述编程语言中的对象了。

不同的编程语言中，设计者也利用各种不同的语言特性来抽象描述对象，最为成功的流派是使用`类`的方式来描述对象，这诞生了注入 C++、Java 等流行的编程语言。JavaScript 选择一个更为冷门的方式：`原型`。

### JavaScript 对象的特征

在我看来，不论我们使用什么样的编程语言，我们都先应该去理解对象的本质特征（参考 Grandy Booch《面向对象分析与设计》）。总结来看，对象有如下几个特点。

- 对象具有唯一标识性：即使完全相同的两个对象，也并非同一个对象。
- 对象有状态：对象具有状态，同一对象可能处于不同状态之下。
- 对象具有行为：即对象的状态，可能因为它的行为产生变迁。

对于第一特征： 对象具有唯一标识性，一般来说，各种语言的对象唯一标识性都是用`内存地址`来体现，对象具有唯一标识的内存地址，所以具有唯一标识性。

JavaScript 中，任何不同的对象是互不相等的。

```js
var o1 = { a: 1 };
var o2 = { a: 1 };
console.log(o1 == o2); // false
```

对于第二个和第三个特征“状态和行为”，JavaScript 将状态和行为统一抽象为`属性`。JavaScript 中将函数设计成一种特殊对象。所以 JavaScript 中的行为和状态都能用属性来抽象。

看下边的代码就展示了普通属性和函数作为属性的一个例子。

```js
var o = {
  d: 1,
  f() {
    console.log(this.d);
  },
};
```

**在事项了对象基本特征的基础上，JavaScript 中对象具有独有的特性：对象具有高度的动态性，这是因为 JavaScript 赋予了使用者在运行时对对象添改状态和行为的能力。**

### JavaScript 对象的两类属性

为了提高抽象能力，JavaScript 提供了数据属性和访问器属性（getter/setter）两类。

对 JavaScript 来说，属性不菲只是简单的名称和值，JavaScript 用一组特征（attribute）来描述属性（property）。

- 第一类：数据属性。数据属性既有四个特征。
  - value: 属性的值。
  - writable: 决定属性是否被赋值。
  - enumerable: 决定 for in 能否枚举该属性。
  - configurable: 决定该属性能否被删除或者改变特征值。
- 第二类：访问器（getter/setter）属性，它也有四个特征
  - getter: 函数或者 undefined，在取属性值时被调用。
  - setter: 函数或者 undefined，在设置属性市被调用。
  - enumerable: 决定 for in 能够枚举该属性
  - configurable： 决定该属性能否被删除或者改变特征值。

我们通常用于定义属性的代码会产生数据属性，其中的 writable、enumerable、configurable 都默认为 true。

```js
var o = { a: 1 };
o.b = 2;
//a和b皆为数据属性
Object.getOwnPropertyDescriptor(o, 'a'); // {value: 1, writable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor(o, 'b'); // {value: 2, writable: true, enumerable: true, configurable: true}
```

实际上 JavaScript 对象的运行时是一个“属性的集合”，属性以字符串或者 Symbol 为 key，以数据属性特征值或者访问器属性特征值为 value。

### 小结

本文整理了关于对象的一些基本概念，分析了 JavaScript 中对象的设计思路，并从运行时的角度介绍了 JavaScript 对象的具体设计：具有高度动态性的属性集合。
