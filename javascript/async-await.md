# 手写实现 - async/await

经常有人说 async/await 是 generator 函数的语法糖，那么到底是怎么实现的呢？

下面就带大家理解 async/await 和 generator 之间到底是怎么相互协作的。

示例：

```js
const getData = (n) =>
  new Promise((resolve) => setTimeout(() => resolve(`data  ${n}`), 1000));

async function test() {
  // await被编译成了yield
  const data = await getData(1);
  console.log('data: ', data);
  const data2 = await getData(2);
  console.log('data2: ', data2);
  return 'success';
}

test().then((res) => console.log(res)); // success
```

上边的简单示例，如果把它使用 generator 函数来写的话，会是什么样呢？

```js
// test 转换成 generator
function* testG() {
  // await被编译成了yield
  const data = yield getData(1);
  console.log('data: ', data);
  const data2 = yield getData(2);
  console.log('data2: ', data2);
  return 'success';
}
```

首先 generator 函数并不会自动执行，每一次调用它的 next 方法，会停留在下一个 yield 的位置。那么接下来，我们就要一次一次的去调用它的 next,使其可以一直运行下去。

```js
function asyncToGenerator(generatorFunc) {
  return function() {
    const gen = generatorFunc.apply(this, arguments);
    return new Promise((resolve, reject) => {
      function step(arg) {
        let generatorResult;
        try {
          generatorResult = gen.next(arg);
        } catch (error) {
          return reject(error);
        }

        const { done, value } = generatorResult;

        if (done) {
          resolve(value);
        } else {
          Promise.resolve(value).then(
            (val) => step(val),
            (err) => reject(error)
          );
        }
      }

      step();
    });
  };
}
```

asyncToGenerator 函数的内部会一次一次的去调用generator函数的next，直到done为true,但会函数的执行结果。


```js
// 使用

function t() {
  // 用来验证 test 执行时的this指向
  this.a = 'tes1111';
  // 使用 asyncToGenerator 包裹
  this.test = asyncToGenerator(testG);
}

const tt = new t();

tt.test().then(res => {
    console.log('all is success');
    console.log(res);
})
```
