
# JavaScript + ES6 函数式编程 读书笔记

## 简介

JavaScript+ES6 函数式编程  
Beginning Functional Jacascript  
Anto Aravinth

## Q & A

问题：

- 什么是函数式编程
  函数式编程是一种范式，我们能够以此创建仅依赖输入值就可以完成自身逻辑的函数。这保证了当函数被多次调用时仍然返回相同的结果。
- 函数式编程的好处是什么
- Q: JavaScript 是函数式编程语言么
  A: 不是（可以创建一个不接受参数，并且什么也不返回的函数），JavaScript 不是一种纯函数语言，更像是一种多范式语言。不过非常适合函数式编程范式。js 支持将函数作为参数，以及将函数传递给另外一个函数等特性-- 主要原因是 js 将函数视为一等公民。
- 声明式、命令式、抽象
  不要命令式

## 特性

- 引用透明性(Referential Transparency): 所有的函数对于相同的输入都将返回相同的值。(在并发代码和可缓存代码中发挥重要作用)
- 无副作用

### 纯函数

大多数函数式编程的好处来自于编写纯函数。

纯函数定义： 纯函数是对给定的注入返回相同的输出的函数。

- 纯函数产生可测试的代码，纯函数不应改变任何外部环境的变量。
- 合理的代码
- 并发的代码， 纯函数总是允许并发的执行函数
- 可缓存，由于函数总是为给定的输入返回相同的输出（引用透明性），所有我们可以缓存函数的输出
- 纯函数应该被设计为只做一件事。（只做一件事并把它做到完美是 UNIX 的哲学）
- 纯函数可以组合

## 高阶函数

高阶函数 HOC(Higher-Order Function)： 高阶函数是接受函数作为其参数并且/或者返回函数座位输出的函数。

> 当一门语言允许函数作为任何其他数据类型使用时，函数被称为一等公民。也就是说函数可被赋值给变量，作为参数传递，也可被其他函数返回。

## 数组的函数式编程

```js
const map = (arr, fn) => {
  if (!Array.isArray(arr)) return [];
  const result = [];
  for (let i = 0; i <= arr.length - 1; i++) {
    result.push(fn(arr[i], i));
  }
  return result;
};

const filter = (arr, fn) => {
  if (!Array.isArray(arr)) return [];
  const result = [];
  for (let i = 0; i <= arr.length - 1; i++) {
    fn(arr[i], i) ? result.push(arr[i]) : null;
  }
  return result;
};

const reduce = (arr, fn, initValue) => {
  let accumlator;
  let start = 0;
  if (initValue != undefined) {
    accumlator = initValue;
  } else {
    accumlator = arr[0];
  }

  if (initValue === undefined) {
    start = 1;
  }

  for (let i = start; i <= arr.length - 1; i++) {
    accumlator = fn(accumlator, arr[i], i);
  }

  return accumlator;
};

const zip = (leftArr, rightArr, fn) => {
  let i,
    result = [];
  for (i = 0; i < Math.min(leftArr.length, rightArr.length); i++) {
    result.push(fn(leftArr[i], rightArr[i]));
  }
  return result;
};
```

#### 柯里化

柯里化： 把一个多参数函数转换为一个嵌套的一元函数的过程。

```js
const curry = (fn) => {
  if (typeof fn !== 'function') {
    throw Error('fn is not function');
  }
  return function curryFn(...args) {
    if (args.length < fn.length) {
      return function() {
        return curryFn.apply(null, args.concat([].slice.call(arguments)));
      };
    }

    return fn.apply(null, args);
  };
};

// 利用柯里化查找数组中含有数字的项
const match = curry(function(expr, string) {
  return string.match(expr);
});
let hasNumber = match(/[0-9]+/);
let arrayFilter = curry(function(f, ary) {
  return ary.filter(f);
});
let findNumberInArray = arrayFilter(hasNumber);
console.log(findNumberInArray(['js', 'numqwe1'])); // 'numqwe1'
```

#### 偏函数 （partial）

```js
const partial = function(fn, ...partialArgs) {
  let args = partialArgs;

  return function(...fullArguments) {
    let argCount = 0;
    let _args = [...args];

    for (
      let i = 0;
      (i < fullArguments.length) & (argCount < fullArguments.length);
      i++
    ) {
      if (_args[i] === undefined) {
        _args[i] = fullArguments[argCount++];
      }
    }
    return fn.apply(null, _args);
  };
};

let obj1 = { foo: 'bar', bar: 'foo' };
let obj2 = { foo: 'test' };

let prettyPrintJson = partial(JSON.stringify, undefined, null, 2);

prettyPrintJson(obj1); // {"foo": "bar","bar": "foo"}
prettyPrintJson(obj2); // {"foo": "test"}
```

#### 组合

compose 数据流从右往左

```js
const compose = (...fns) => {
  if (fns.length === 0) return (arg) => arg;

  if (fns.length === 1) {
    return fns[0];
  }

  return (...values) => {
    return fns.reverse().reduce(function(ac, fn) {
      return fn(ac);
    }, values);
  };
};

const composeA = (...fns) => {
  if (fns.length === 0) return (arg) => arg;

  if (fns.length === 1) {
    return fns[0];
  }

  return fns.reduce((a, b) => {
    return (...args) => {
      return a(b(...args));
    };
  });
};

const splitString = (str) => str.split(' ');
const countA = (arr) => arr.length;
const oddOrEven = (ip) => (ip % 2 === 0 ? 'even' : 'odd');
const countStringW = compose(
  oddOrEven,
  countA,
  splitString
);

const result = countStringW('test work count string'); // even
console.log(result);
```
