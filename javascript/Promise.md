# 手写实现 - Promise

> 这里主要是简单实现 Promise

```js
const isFunction = value => typeof value === "function";

const PENDING = "PENDING";
const FULFILLED = "FULFILLED";
const REJECTED = "REJECTED";

class MyPromise {
  constructor(handle) {
    if (!isFunction(handle)) throw Error("The param must be a function");
    this._status = PENDING;
    this._value = undefined;
    this.fulfilledList = [];
    this.rejectedList = [];
    try {
      handle(this._resolve.bind(this), this._reject.bind(this));
    } catch (error) {
      this._reject(error);
    }
  }

  // 状态需要时 PENDING
  // 顺序执行 .then() 注册的回调队列
  _resolve(val) {
    const run = () => {
      if (this._status !== PENDING) return;
      this._status = FULFILLED;
      this._value = val;
      let cb = null;
      this.fulfilledList.forEach(cb => {
        cb(val);
      });
    };
    setTimeout(run, 0);
  }
  // 状态需要时 PENDING
  // 顺序执行 .then() 注册的回调队列
  _reject(err) {
    if (this._status !== PENDING) return;
    const run = () => {
      this._status = REJECTED;
      this._value = err;
      let cb = null;
      this.rejectedList.forEach(cb => {
        cb(err);
      });
    };
    setTimeout(() => run(), 0);
  }

  // 返回一个 成功状态的 promise
  static resolve(val) {
    if (val instanceof MyPromise) return val;
    return new MyPromise(resolve => resolve(val));
  }
  // 返回一个 失败状态的 promise
  static reject(err) {
    return new MyPromise((resolve, reject) => reject(err));
  }
  // 接收两个参数： 成功回调、失败回调
  // 链式调用 返回一个新的 promise
  // 多个then 按照注册顺序 依次执行： 成功会依次执行then,失败会依次执行catch
  // 新的 promise 状态根据之前的状态改变
  then(onFulfilled, onRejected) {
    return new MyPromise((resolveNext, rejectNext) => {
      // 成功
      let fulfilled = value => {
        try {
          if (!isFunction(onFulfilled)) {
            // 如果当前回调不是一个函数，则直接将当前Promise 状态改为 成功
            // 并立即执行下一个then
            resolveNext(value);
          } else {
            const res = onFulfilled(value); // 执行当前then的成功回调，并返回结果
            if (res instanceof MyPromise) {
              // 如果成功回调返回一个promise对象，则等待该promise状态改变后执行下一个函数
              res.then(resolveNext, rejectNext);
            } else {
              // 否则将返回结果当做参数，传给县一个then的回调函数，并立即执行下一个then
              resolveNext(res);
            }
          }
        } catch (error) {
          // 如果函数执行过程中出错，新的Promise 状态为失败
          rejectNext(error);
        }
      };
      // 失败
      let rejected = error => {
        try {
          if (!isFunction(onRejected)) {
            // 如果当前失败回调不是一个函数，则直接将当前Promise 状态改为 失败  并立即执行catch
            rejectNext(error);
          } else {
            let res = onRejected(error);
            if (res instanceof MyPromise) {
              res.then(onFulfilled, onRejected);
            } else {
              rejectNext(res);
            }
          }
        } catch (err) {
          // 如果函数执行中出错，新的promise 状态为失败
          rejectNext(err);
        }
      };
      switch (this._status) {
        case PENDING:
          // 当状态为pending时，将then方法回调函数加入执行队列等待执行
          this.fulfilledList.push(fulfilled);
          this.rejectedList.push(rejected);
          break;
        case FULFILLED:
          fulfilled(this._value);
        case REJECTED:
          rejected(this._value);
      }
    });
  }

  // 想当于调用then 方法， 只传入 reject
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  // 无论promise 状态怎么样都会执行
  finally(cb) {
    return this.then(
      value => MyPromise.resolve(cb()).then(() => value),
      reason =>
        MyPromise.resolve(cb()).then(() => {
          throw Error(reason);
        })
    );
  }
  // 参数数组中所有元素都变为决定态后，返回新的promise
  static all(list) {
    return new MyPromise((resolve, reject) => {
      let count = 0;
      let hasError = false;
      let result = new Array(list.length);
      for (let i = 0; i < list.length; i++) {
        let promise = list[i];
        if (!(promise instanceof MyPromise)) {
          promise = MyPromise.resolve();
        }
        promise.then(
          res => {
            if (hasError) return;
            result[i] = res;
            count++;
            if (count === result.length) {
              resolve(result);
            }
          },
          err => {
            if (hasError) return;
            hasError = true;
            reject(err);
          }
        );
      }

      if (list.length === 0) {
        resolve([]);
        return;
      }
    });
  }
  //
  static race() {
    return new MyPromise((resolve, reject) => {
      let hasReturn = false;

      for (let i = 0; i < list.length; i++) {
        let promise = list[i];
        if (!(promise instanceof MyPromise)) {
          promise = MyPromise.resolve();
        }
        promise.then(
          res => {
            if (hasReturn) return;
            hasReturn = true;
            resolve(res);
          },
          err => {
            if (hasReturn) return;
            hasReturn = true;
            reject(err);
          }
        );
      }
    });
  }
}
```
