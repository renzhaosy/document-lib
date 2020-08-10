# JS 数据类型类型和转换

## 类型

### 基础类型和引用类型

JavaScript 语言中，每一个值都属于某一种数据类型。JavaScript 语言规定了 7 中语言类型,广泛的用于变量、函数参数、表达式、函数返回值等场合。

这 7 中语言类型是：

1. Undefined
2. Null
3. Boolean
4. String
5. Number
6. Symbol
7. Object

### Undefined、Null

> 为什么有的编程规范要求用 void 0 代替 undefined？

`Undefined 类型`表示未定义，它的类型只有一个值，就是 undefined。任何变量在赋值前是 Undefined 类型、值为 undefined，一般我们可以用`全局变量undefined（名为undefined的这个变量）`来表示这个值，或者 void 运算来把任意一个表达式变成 undefined 值。

Javascript 的代码 undefined 是一个变量，而并非关键字。（设计失误之一）所以，`为了避免无意中被篡改，可以使用void 0 来获取 undefined 的值`。

Null 类型也只有一个值，就是 null，它的语义表示`空值`。null 为 JavaScript 关键字。

区别： Null 表示`定义了但是为空`, Undefined 表示`未定义`

### Boolean

Boolean 类型有两个值，true 和 false，用于表示逻辑意义上的真和假，同样有关键字 true 和 false 来表示这两个值。

### String

> 字符串是否有最大长度？

String 用于表示`文本数据`。String 有最大长度是 2^53 - 1。但是这个所谓最大长度，并不完全是你理解中的字符数。

因为 String 的意义并非“字符串”，而是字符串的 UTF16 编码，字符串的操作 charAt、charCodeAt、length 等方法针对的都是 UTF16 编码。所以字符串的最大程度，实际是手字符串的编码长度影响的。

> Node: 现行的字符集国际标准，字符是以 Unicode 的方式表示的，每一个 Unicode 的码点表示一个字符，理论上，Unicode 的范围是无限的。UTF 是 Unicode 的编码方式，规定了码点在计算机中的表示方法，常见的有 UTF16 和 UTF8。Unicode 的码点通常用 U+??? 来表示，其中 ??? 是十六进制的码点值。 0-65536（U+0000 - U+FFFF）的码点被称为基本字符区域（BMP）。`基本字符区域的字符它们的UTF16编码的字符长度都和字符数相等`。

JavaScript 中的字符串是`永远无法变更`的，一旦字符串构造出来，无法用任何方式改变字符串的内容，所以字符串具有值类型的特征。

### Number

> 0.1 + 0.2 不是等于 0.3 么？为什么 JavaScript 里不是这样的？

JavaScript 中的 Number 类型基本符合 IEEE 754 规定的双精度浮点数规则，但是为了表达几个额外的语言场景，规定了几个例外情况：

- NaN；
- Infinity，无穷大；
- -Infinity，负无穷大。

JavaScript 中计算 0.1 + 0.2 时，到底发生了什么？

首先，十进制的 0.1 和 0.2 会被转换成二进制，但由于浮点数用二进制表达时是无穷的，例如

```
0.1 -> 0.0001100110011001...(无限)
0.2 -> 0.0011001100110011...(无限)
```

IEEE 754 标准的 64 位双进度浮点数的小数部分最多支持 53 位二进制位，所以两者相加之后得到二进制位

```
0.0100110011001100110011001100110011001100110011001100
```

因浮点数小数位的限制而截断的耳机二进制数字，再转换成十进制，就成了 0.30000000000000004，经过了 3 次转换和一次运算的精度损失，误差就这样产生了。

整数同样会有精度的问题

```js
console.log(19921992754740991); //=> 19921992754740990
console.log(19921992754740991 === 19921992754740992); //=> true
```

同样的原因，JavaScript 中 Number 类型统一按照浮点数处理，整数按照 54 位来算最大 2^53 - 1,`Number.MAX_SAFE_INTEGER`和最小`Number.MIN_SAFE_INTEGER`安全小横竖范围，超过这个范围就会存在精度问题。

这个问题并不只有 JavaScript 中存在，几乎所有的编程语言都采用了 IEEE 754 浮点数表示法，任何使用二进制浮点数的编程语言都有这个问题。

### Object

> 为什么给对象添加的方法能用在基本类型上？

Object 是 JavaScript 中最复杂的类型，也是 JavaScript 的核心机制之一。Object 表示对象的意思，它是一切有形和无形物体的总称。

在 JavaScript 中，对象的定义是`属性的集合`。属性分为`数据属性`和`访问器属性`，二者都是 key-value 结构，key 可以是字符串或者 Symbol 类型。

JavaScript 中的几个基本类型，都在对象类型中有一个“亲戚”。它们是：

- Number;
- String;
- Boolean;
- Symbol;

所以，3 和 new Number(3) 是完全不同的值，它们一个是 Number 类型，一个是对象类型。

Number、String 和 Boolean，三个构造器是两用的，当跟 new 搭配时，它们产生对象，当直接调用时，它们表示`强制类型转换`。

JavaScript 语言设计上试图模糊对象和基本类型之间的关系，我们日常代码可以把对象的方法在基本类型上使用，比如：

```js
console.log('abc'.charAt(0)); //a
```

甚至我们在原型上添加方法，都可以应用于基本类型，比如以下代码，在 Symbol 原型上添加了 hello 方法，在任何 Symbol 类型变量都可以调用。

```js
String.prototype.hello = () => console.log('hello');
var a = 'a';
console.log(typeof a); //string, a 并非对象
a.hello(); //hello，有效
```

所以上边的问题答案就是：

> `.`运算符提供了装箱操作，它会根据基本类型构造一个临时对象，使的我们能够在基础类型上调用对应对象的方法。

## 类型转换

因为 JS 是弱类型语法，所以类型转换发生非常频繁，大部分我们熟悉的运算都会先进行类型转换。

> 推荐禁止使用`==`,应先进行显示地类型转换后，用`===`比较

### 双等号的类型转换

`==` 的基本原则是

- 类型相等的时候，就是正常的比较;
- 类型不同的时候，全转为 Number 来比较。 例如： 优先把布尔类型的变量转换成 Number 类型（很违法人类直觉，公认的设计失误）

### 大部分类型转换

大部分类型转换规则是简单的。

| 类型转换 | Null      | Undefined   | Boolean(true) | Boolean(false) | Number         | String         | Symbol    | Object   |
| -------- | --------- | ----------- | ------------- | -------------- | -------------- | -------------- | --------- | -------- |
| Boolean  | FALSE     | FALSE       | -             | -              | 0/NaN - false  | '' - false     | TRUE      | TRUE     |
| Number   | 0         | NaN         | 1             | 0              | -              | StringToNumber | TypeError | 拆箱转换 |
| String   | 'null'    | 'undefined' | 'true'        | 'false'        | NumberToString | -              | TypeError | 拆箱转换 |
| Object   | TypeError | TypeError   | 装箱转换      | 装箱转换       | 装箱转换       | 装箱转换       | 装箱转换  | -        |














### 装箱转换

参考上方`Object 类型`
每一种基本类型 Number、String、Boolean、Symbol 在对象中都有对应的类，所谓的装箱转换，就是把基本类型转换为对应的对象，它是类型转换中一种相当重要的种类。

例如 Number 类型， 当使用 new 调用时，返回一个对象，直接调用会返回一个值。这个时候就称`这个 Number 对象和这个值它存在一种装箱关系`。

全局的 Symbol 函数无法使用 new 来调用，我们可以利用装箱机制来得到一个 Symbol 对象

```js
var symbolObject = function () {
  return this;
}.call(Symbol('a'));
console.log(typeof symbolObject); //object console.log(symbolObject instanceof Symbol); //true console.log(symbolObject.constructor == Symbol); //true
```

装箱机制会频繁产生临时对象，在一些对性能要求较高的场景下，我们应该尽量避免对基本类型做装箱转换。

使用内置的 Object 函数，就可以显式的调用装箱能力。

```js
var symbolObject = Object(Symbol('a'));
console.log(typeof symbolObject); //object console.log(symbolObject instanceof Symbol); //true console.log(symbolObject.constructor == Symbol); //true
```

> 在 JavaScript 中，没有任何方法可以更改私有的 Class 属性，因此 Object.prototype.toString 是可以准确识别对象对应的基本类型的方法，它比 instanceof 更加准确。

### 拆箱转换

在 JavaScript 标准中，规定了 `ToPrimitive` 函数，它是`对象类型到基本类型的转换`（即，拆箱转换）。

对象到 String 和 Number 的转换都遵循“先拆箱再转换”的规则。通过拆箱转换，把对象变成基本类型，再从基本类型转换为对应的 String 或者 Number。

拆箱转换会尝试调用 valueOf 和 toString 来获得拆箱后的基本类型。如果 valueOf 和 toString 都不存在，或者没有返回基本类型，则会产生类型错误 TypeError。

```js
var o = {
  valueOf: () => {
    console.log('valueOf');
    return {};
  },
  toString: () => {
    console.log('toString');
    return {};
  },
};

o * 2;
// valueOf
// toString
// TypeError
```

在进行 o\*2 运算时，可以看到先执行了 valueOf,接下来是 toString，最后抛出了一个 TypeError,这说明这个拆箱转换失败了。

**在进行不同的转换时，会根据提示来决定 valueOf 和 toString 调用的先后顺序。**例如：加法会先调用 valueOf。

到 String 的拆箱转换会优先调用 toString。把刚刚的运算改为 String(o),可以看到顺序变了。

```js
var o = {
  valueOf: () => {
    console.log('valueOf');
    return {};
  },
  toString: () => {
    console.log('toString');
    return {};
  },
};

String(o);
// toString
// valueOf
// TypeError
```

在 ES6 之后，还允许对象通过显式指定 @@toPrimitive Symbol 来覆盖原有的行为。

```js
var o = {
  valueOf: () => {
    console.log('valueOf');
    return {};
  },
  toString: () => {
    console.log('toString');
    return {};
  },
};
o[Symbol.toPrimitive] = () => {
  console.log('toPrimitive');
  return 'hello';
};
console.log(o + '');
// toPrimitive
// hello
```

### StringToNumber

### NumberToString


## 类型判断

JavaScript 语言中提供了 typeof，用来返回操作数的类型，但是 typeof 的运算结果与运行时类型的规定有很多不一致的地方。

| 示例表达式     | typeof 结果 | 运行时类型行为 |
| -------------- | ----------- | -------------- |
| null           | object      | Null           |
| {}             | object      | Object         |
| (function(){}) | function    | Object         |
| 3              | number      | Number         |
| "ok"           | string      | String         |
| true           | boolean     | Boolean        |
| void 0         | undefined   | Undefined      |
| Symbol('a')    | symbol      | Symbol         |

在表格中，多数项是对应的，要注意的是 object --- Null 和 function --- Object 是特例。
