---
title: ES6入门-Promise
date: 2017-12-15 21:24:53
categories: ES6入门
tags:
     - 笔记
     - JavaScript
     - ECMAScript 2015
description: ES6入门--Promise
---
# Promise对象的含义和基本用法

1.Promise是异步编程的一种解决方案，ES6将其写进了语言标准，统一了用法，原生提供了Promise对象。

2.Promise有以下两个特点：
    (1) 对象状态不受外界影响。Promise对象代表一个异步操作，有三种状态：pending(进行中)、fulfilled(已成功)和rejected(已失败)。
    (2) 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise对象的状态改变，只有两种可能，从pending变成fulfilled和从pending变成rejected。

3.Promise对象是一个构造函数，用来生成Promise实例，示例：

```javascript
const promise = new Promise(function(resolve, reject) {
    if ( /*操作成功*/ ) {
        resolve(value);
    } else {
        reject(error);
    }
})
```
4.Promise示例生成后，可以用then方法分别指定resolved状态和rejected状态的回调函数。示例：

```javascript
promise.then(function(value) {
    // success
}, function(error) {
    // failure
});
```
5.简单示例：

```javascript
function timeout(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, ms, 'done');
    });
}
timeout(100).then((value) => {
    console.log(value);
})
```
6.Promise新建后就会立即执行，示例：

```javascript
let promise = new Promise(function(resolve, reject)) {
    console.log('promise'); // 最先输出
    resolve();
}

promise.then(function() {
    console.log('resolved'); // 当前脚本所有同步任务执行完才执行，最后输出
});

console.log('hi'); // 第二输出
```
7.异步加载图片的例子：

```javascript
function loadImageAsync(url) {
    return new Promise(function(resolve, reject) {
        const image = new Image();

        image.onload = function() {
            resolve(image);
        };

        image.onerror = function() {
            reject(new Error('could not load imag at' + url));;
        };

        image.src = url;
    });
}
```
8.用Promise实现Ajax的例子：

```javascript
const getJSON = function(url) {
    const promise = new Promise(function(resolve, reject) {
        const handler = function() {
            if (this.readState !== 4) {
                return;
            }
            if (this.status === 200) {
                resolve(this.response);
            } else {
                reject(new Error(this.statusText));
            }
        };
        const client = new XMLHttpRequest();
        client.open("GET", url);
        client.onreadystatechange = handler;
        client.responseType = "json";
        client.setRequestHeader("Accept", "application/json");
        client.send();
    });
    return promise;
};

getJSON("/posts.json").then(function(json) {
    console.log("conetent:" + json);
}, function(error) {
    console.err("出错了", error);
});
```
# Promise.prototype.then()
1.then方法是定义在原型对象Promise.prototype上的，作用是为Promise实例添加状态改变时的回调函数。

2.then方法返回的是一个新Promise实例，因此可以采用链式写法，即在then方法后面再调用另一个then方法。例如：

```javascript
getJSON("/post/1.json").then(function(post) {
    return getJSON(post.commentURL);
}).then(function funcA(comments) {
    console.log("resolved:", comments);
}, function funcB(err) {
    console.log("rejected:", err)
});
```
3.采用箭头函数简化上述代码：

```javascript
getJSON("/post/1.json").then(
    post => getJSON(post.commentURL)
).then(
    comments => console.log("resolved:", comments),
    err => console.log("rejected:", err);
);
```
# Promise.prototype.catch
1.Promise.prototype.catch是.then(null, rejection)的别名，用于指定发生错误时的回调函数。例如：

```javascript
getJSON("/post/1.json").then(function(posts) {
    // ...
}).catch(function(error) {
    // console.log(error);
});
```
2.then方法指定的回调函数如果在运行中抛出错误，也会被catch方法捕获。

3.reject方法的作用，等同于抛出错误，如果Promise状态已经变成resolved，再抛出错误是无效的。例如：

```javascript
const promise = new Promise(function(resloved, reject) {\
    reslove("ok");
    throw new Error('test');
});
// 不会输出test
promise.then(value => console.log(value)).catch(error => console.log(error));
```
4.一般来说是不需要reject函数的，总是使用catch方法，例如：

```javascript
promise
    .then(data => console.log('success'))
    .catch(error => console.log(error));
```
5.Promise内部的错误不会影响到Promise外部的代码，不会终止脚本执行。

6.建议Promise对象后面要跟catch方法，catch方法返回的还是一个Promise对象，因此可以链式编程。

7.如果catch后面继续调用then方法，那么当这个then方法发生错误时，就不会被捕获。

# Promise.all()
1.Promise.all方法用于将多个Promise实例，包装成一个新的Promise实例。

```javascript
const promise = Promise.all([p1, p2, p3]);
```
2.Promise.all方法的参数可以不是数组，但必须有Iterator接口，且返回的每个成员都是Promise实例。

3.包装的新Promise实例的状态由p1、p2、p3决定，只有p1、p2、p3的状态都变成fulfilled的，p的状态才会变成fulfilled，只要p1、p2、p3中有一个是rejected，p的状态就变成rejected。

4.示例：

```javascript
const databasePromise = connectDatabase();
const booksPromise = databasePromise.then(findAllBooks);
const userPromise = databasePromise.then(getCurrentUser);
Promise.all([
    booksPromise,
    userPromise
]).then(([book, user]) => pickTopRecommentations(books, user));
```
> 注：如果作为参数的Promise实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。

# Promise.race()
1.Promise.race方法同样是将多个Promise实例，包装成一个新的Promise实例。

```javascript
const p = Promise.race([p1, p2, p3]);
```
2.p1、p2、p3中只要有一个改变状态，p的状态就跟着改变。

3.示例：

```javascript
const p = Promise.race([
    fetch('/resource-that-may-take-a-while'),
    new Promise(function(resolve, reject) {
        setTimeout(() => reject(new Error('request timeout')), 5000)
    })
]);
p.then(response => console.log(response));
p.catch(error => console.log(error));)
```
# Promise.resolve()
1.Promise.resolve方法将现有的对象转换为Promise对象

```javascript
const jsPromise = Promise.resolve($.ajax('/whatever.json'));
```
2.Promise.resolve方法的参数分成四种情况：
    (1)参数是一个Promise实例，那么Promise.resolve将不做任何修改，直接返回。
    (2)参数是一个thenable对象，thenable对象指的是具有then方法的对象，Promise.resolve方法会将这个对象转换为Promise对象，然后立即执行thenable的then方法。
    (3)参数不具有then方法，或者就不是对象，Promise.resolve将返回一个新的Promise对象，状态为resolved
    (4)不带参数时，直接返回一个Promise对象
# Promise.reject()
1.Promise.reject()方法也会返回一个新的Promise实例，状态为rejected。例如：

```javascript
const p = Promise.reject('出错了');
```
2.Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。

3.示例：
 
```javascript
const thenable = {
    then(resolve, reject) {
        reject('error');
    }
};
Promise.reject(thenable).catch(e => {
    console.log(e === thenable); // true
})
```
# 两个有用的附加方法
1.done()
    (1)Promise对象的回调链，不管以then方法或catch方法结尾，要是最后一个方法抛出错误，都有可能无法捕获到，因此我们可以提供一个done方法，总是处于回调链的尾端，保证抛出任何可能出现的错误。
    (2)示例：
```javascript
asyncFunc().then(f1).catch(f2).then(f2).done();
```
    (3)done方法实现代码:
```javascript
Promise.prototype.done = function(onFulfilled, onRejected) {
    this.then(onFulfilled, onRejected)
        .catch(function(reason) {
            setTimeout(() => {
                throw reason
            }, 0)
        });
};
```
2.finally()
    (1)finally方法用于指定不管Promise对象最后状态如何，都会执行的操作。
    (2)示例：
```javascript
server.listen(0)
    .then(function() {
        // run
    })
    .finally(server.stop)
```
    (3)finally方法的实现代码：
```javascript
Promise.prototype.finally = function(callback) {
    let p = this.constructor;
    return this.then(
        value => p.resolve(callback()).then(() => value),
        reason => p.resolve(callback()).then(() => {
            throw reason
        })
    );
};
```
# 应用
1.加载图片
    
```javascript
const preloadImage = function(path) {
    return new Promise(function(resolve, reject) {
        const image = new Image();
        image.onload = resolve;
        image.error = reject;
        image.src = path;
    });
};
```
# Promise.try()
1.如果不知道函数是同步还是异步的，但是想用Promise来处理它，一般就会采用下面的写法：

```javascript
Promise.resolve().then(f);
```
2.如果函数是同步的，那么它会在本轮事件循环的末尾执行，例如：

```javascript
const f = (() => console.log('now')); // 后打印
Promise.resolve.then(f);
console.log('next'); // 先打印
```
3.Promise.try是模拟try代码块，就像promise.catch模拟的是catch代码块，例如：

```javascript
Promise.try(database.users.get({
        id: userId
    }))
    .then(...)
    .catch(...)
```

