---
title: ES6入门-Module
date: 2017-11-18 09:18:04
categories: ES6入门
tags:
     - 笔记
     - JavaScript
     - ECMAScript 2015
description: ES6入门--Module
---
# export命令
1.export命令用于规定模块的对外接口。
2.一个模块就是一个独立的文件，该文件内部的所有变量，外部无法获取。如果能让外部获取某个变量，则需要用export关键字输出该变量。示例:
```
export var firstName = "jack";
export var year = 1009;
// 另一种写法(优先考虑)：
var name = "jack";
var year = 1998;
export{name, year};
// 输出函数或class：
export function a(n, m) {
    return n + m;
}
```
3.as关键字重命名。示例：
```
export {name as name123};
```
4.export规定的对外的接口，必须与模块内部建立一对一的对应关系。
```
// 错误示例：
var m = 1;
export m;
// 正确示例：
var m = 1;
export {m}
```
5.export语句输出接口，与其对应的值是动态绑定关系，即可以通过该接口获取到模块内部实时的值。
6.export命令可以出现在模块的任何位置，只要处于模块顶层就可以，如果在块级作用域内就会报错。

# import命令
1.import命令用于加载模块。示例：
```
import {name, year} from './profile';
function a(dom) {
    dom.innerHtml = name + ',' + year;
}
```
2.as关键字重命名。示例：
```
import {a as name} from './profile';
```
3.import命令有提升作用，会首先执行。
4.import是静态执行，所以不能使用表达式和变量。
5.import语句是单例模式。

# 模块的整体加载
1.用*指定一个对象，所有的输出值都加载到这个对象上面。示例：
```
// a.js
export function a(n){}
export function b(){}

import * as obj from './a.js';
console.log(obj.a);
console.log(obj.b);
```
2.模块整体加载所在的那个对象是静态的，所以不允许运行时改变。

# export default命令
1.export default命令，为模块指定默认输出，在import命令时不必知道export导出的属性是什么。示例：
```
// a.js
export default function (b){
    return b;
}

import a from './a.js';
a(1);
```
2.使用export default命令后，import命令后面不用加{}。
3.export default命令只能使用一次，因为一个模块只能有一个默认输出。
4.export default命令本质上是输出一个default变量，所以它后面不能跟变量声明语句。示例：
```
// 错误
export default var a = 1;
```

# 跨模块的常量
1.const声明的常量只能在当前代码块有效，如果一个常量想要被多个模块共享，可以写一个常量文件，并将它输出成模块。示例：
```
// db.js
export const db = {
    url: 'http://aa',
    admin_username: 'admin',
    admin_password: 'tfsadmin'
};

// user.js
export const user = ['root', 'admin', 'ceo'];

// 将这些常量合并到index.js中
export {db} from './db.js'; // export和import的复合写法
export {user} from './user.js';

// 加载时，直接加载index.js就可以了
import {db, user} from './index';
```
