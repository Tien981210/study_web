# Promise

> ECMAScript 6 入门 (阮一峰) [http://es6.ruanyifeng.com/#docs/promise](http://es6.ruanyifeng.com/#docs/promise)

目录

- [Promise 的特点](#promise-的特点)
- [基本用法](#基本用法)
- [Promise.prototype.then()](#promiseprototypethen)
- [Promise.prototype.catch()](#promiseprototypecatch)
- [Promise.prototype.finally()](#promiseprototypefinally)
- [Promise.all()](#promiseall)
- [Promise.race()](#promiserace)
- [Promise.resolve()](#promiseresolve)
- [Promise.reject()](#promisereject)
- [Promise.try()](#promisetry)

## Promise 的特点

保存异步请求结果，方便后面查看，异步触发结果回调。

特点

- 对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 pending 变为 fulfilled 和 从 pending 变为 rejected。then 方法除外，它接收的是新的 Promise。

缺点

- 无法取消 Promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。
- 当处于 pending 状态时，无法得知目前进展到哪一个阶段。

## 基本用法

- 通过 resolve 和 reject 识别是否成功
- 通过 resolve 和 reject 的参数返回结果

```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

- 如果 p2 结果是成功，那么会等待 p1 的结果，并把 p1 结果作为 p2 结果。
- 如果 p2 结果是失败，那么不会等待 p1 的结果，直接失败。

```
const p1 = new Promise(function (resolve, reject) {
  // ...
});

const p2 = new Promise(function (resolve, reject) {
  // ...
  resolve(p1);
})
```

## Promise.prototype.then()

- 返回的是一个新的 Promise 实例，不是原来那个 Promise 实例。
- 前一个返回值传入后一个 then，前一个不传，后一个为 undefined。
- 前一个函数返回 Promise 实例，后面的 then 会捕获该 Promise 的状态和返回参数。
- 前一个函数报错，只会影响好后面第一个 then 为失败状态，再后面的为成功状态。

```
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function funcA(comments) {
  console.log("resolved: ", comments);
}, function funcB(err){
  console.log("rejected: ", err);
});
```

## Promise.prototype.catch()

- .then(null, rejection)的别名，用于指定发生错误时的回调函数。
- Promise 内部的错误不会影响到 Promise 外部的代码

```
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```

## Promise.prototype.finally()

- 不管 Promise 对象最后状态如何，都会执行的操作。
- 不能获取到 Promise 对象状态，不会有参数传入。

```
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```

## Promise.all()

- 只有 p1、p2、p3 的状态都变成 fulfilled，p 的状态才会变成 fulfilled，此时 p1、p2、p3 的返回值组成一个数组，传递给 p 的回调函数。
- 只要 p1、p2、p3 之中有一个被 rejected，p 的状态就变成 rejected，此时第一个被 reject 的实例的返回值，会传递给 p 的回调函数。
- 结果都返回了，才会触发后面的 then。

```
const p = Promise.all([p1, p2, p3]);
```

注意，如果作为参数的 Promise 实例，自己定义了 catch 方法，那么它一旦被 rejected，并不会触发 Promise.all() 的 catch 方法。

```
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})
.then(result => result)
.catch(e => e);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了');
})
.then(result => result)
.catch(e => e);

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// ["hello", Error: 报错了]
```

## Promise.race()

- 只要 p1、p2、p3 之中有一个实例率先改变状态，p 的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

```
const p = Promise.race([p1, p2, p3]);
```

## Promise.resolve()

- 如果参数是 Promise 实例，那么 Promise.resolve 将不做任何修改、原封不动地返回这个实例。
- 如果参数是 thenable 对象，会将这个对象转为 Promise 对象，然后就立即执行 thenable 对象的 then 方法。
- 如果参数是一个原始值，或者是一个不具有 then 方法的对象，则 Promise.resolve 方法返回一个新的 Promise 对象，状态为 resolved。
- 如果不带有任何参数，直接返回一个 resolved 状态的 Promise 对象。

```
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

```
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value) {
  console.log(value);  // 42
});
```

## Promise.reject()

- 生成一个 Promise 对象的实例 p，状态为 rejected，回调函数会立即执行。

```
const thenable = {
  then(resolve, reject) {
    reject('出错了');
  }
};

Promise.reject(thenable)
.catch(e => {
  console.log(e === thenable)
})
// true
// 不是reject抛出的“出错了”这个字符串，而是thenable对象
```

## Promise.try()

- 所有同步和异步的错误。
- 模拟try代码块。

```
Promise.try(database.users.get({id: userId}))
  .then(...)
  .catch(...)
```
