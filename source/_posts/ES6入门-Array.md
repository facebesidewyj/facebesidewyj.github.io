---
title: ES6入门-Array
date: 2017-11-25 09:48:34
categories: ES6入门
tags:
     - 笔记
     - JavaScript
     - ECMAScript 2015
description: ES6入门-Array
---
# 扩展运算符
## 含义:
1.扩展运算符(...)将一个数组转为 **用逗号分隔的** 参数序列。例如：
```
console.log(...[1, 2, 3]);
```

2.扩展运算符主要用于函数调用。例如：
```
function push(arr, ...items) {
    arr.push(...items);
}

function add(x, y) {
    return x + y;
}

const arr = [4, 30];
add(...arr); // 34
```

3.扩展运算符与正常函数参数相结合使用。例如：
```
function f(v, w, x, y, z) {}
const args = [0, 1];
f(-1, ...args, 2, ...[3]);
```

4.扩展运算符后可以使用表达式。例如：
```
const arr1 = [
    ...(x > 0 ? ['a'] : []),
    'b'
];
```

5.如果扩展运算符后是一个空数组，则不产生任何效果。例如：
```
[...[], 1]; // [1]
```

##扩展运算符替代数组的apply方法
```
// es5写法
function f(x, y, z) {}

var args1 = [0, 1, 2];
f.apply(null, args1);

// es6写法
function f(x, y, z) {}
let args2 = [0, 1, 2];
f(...args2);

// 示例
Math.max(...[14, 3, 77]);
let arr2 = [0, 1, 2];
let arr3 = [3, 4, 5];
arr2.push(...arr3);
new Date(...[2015, 1, 1]);
```

##扩展运算符的应用
1.复制数组
```
const a1 = [1, 2];
const a2 = [...a1]; // a2是a1的克隆
```

2.合并数组
```
var arr4 = ['a', 'b'];
var arr5 = ['c', 'd'];
var arr6 = ['f'];
[...arr4, ...arr5, ...arr6];
```

3.与解构赋值结合(扩展运算符用于赋值只能放到参数的最后一位，否则会报错)
```
const [a, ...result] = [1, 2, 3, 4, 5];
console.log(a); // 1
console.log(result); // [2,3,4,5]

const [b, ...result1] = ["foo"];
console.log(b); //foo
console.log(result1); // []
```

4.字符串(将字符串转化为真正的数组)
```
[..."hello"]; // ['h','e','l','l','o']
function length(str) {
    return [...str].length;
}
```

5.实现了Iterator接口的对象
```
let nodeList = document.querySelectorAll('div');
// querySelectorAll返回一个nodeList对象，它是一个类数组的对象，并且实现了Iterator接口，所有可以转化为数组
let array = [...nodeList];
```

6.Map和Set结构，Generator函数
扩展运算符内部调用的是数据结构Iterator接口，因此只要具有Iterator接口的对象，都可以使用扩展运算符，比如Map和Set。
```
let map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
]);
let arr7 = [...map.keys()];
```

# Array.from方法
## 含义：
1.Array.from方法将两类对象转换为真正的数组对象：类数组对象和可遍历对象。
任何具有length属性的对象都可以通过Array.from方法转换为数组。例如：
```
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
// es5的写法
var arr = [].slice.call(arrayLike); // ['a','b','c']

// es6的写法
var arr = Array.from(arrayLike); // ['a','b','c']
```

2.将Dom操作返回的NodeList集合转换为数组。例如：
```
let ps = document.querySelectorAll('p');
Array.from(ps).forEach(function(p) {
    console.log(p);
});
```

3.将部署了Iterator接口的数据结构转换为数组。例如：
```
Array.from('hello');

let namesSet = new Set(['a', 'b']);
Array.from(namesSet);
```

4.Array.from方法还接受第二个参数，作用类似于数组的map方法，对每个元素进行处理，将处理后的值放入返回的数组。例如：
```
Array.from([1,2,3], x=>x*2);
```

## Array.from方法的应用
1.取出Dom节点的文本内容。
```
let spans = document.querySelectorAll('span.name');
    let names = Array.from(spans, dom=>dom.textContent);
```

2.返回数据类型
```
function typesOf (){
        return Array.from(arguments, value => typeOf value);
    }
    typesOf(null, [], NaN); // ['object', 'object', 'number']
```

3.返回字符串的长度，避免JavaScript将大于\uFFFF的Unicode字符，算作两个字符的bug。
```
function stringLength (str){
        return Array.from(str).length;
}
```

# Array.of方法
## 含义：
1.Array.of用于将一组值转换为数组。例如：
```
Array.of(3, 11, 8);
Array.of(3);
Array.of(3).length;
```

2.Array.of是弥补数组构造函数Array()的不足，因为给构造函数传入一个参数的话是设置数组的length值。

3.Array.of总是返回参数值组成的数组，如果没有参数则返回空数组。

# 数组实例的copyWithin()
## 含义：
1.数组实例的copyWithin()方法，在当前数组内部，将指定位置的成员复制到其他位置(会覆盖原有成员)，然后返回当前数组，会修改当前数组。例如：
```
Array.prototype.copyWithin(target, start = 0, end = length);
```
2.这个方法接受三个参数：
  1.target(必需)：从该位置替换数组，指定要替换的位置。
  2.start(可选)：从该位置开始读取数据，默认为0，如果为负数，则表示倒数。
  3.end(可选)：到该位置停止读取数据，默认为数组长度，如果为负，表示倒数。

  > 注：start和end两个参数是用来指定要复制的内容的。 

例如：
```
[1,2,3,4,5].copyWithin(0, 3); // "[4,5,3,5,4]"
[1,2,3,4,5].copyWithin(0, 3, 4); // "[4,2,3,4,5]"
let arr = [1,2,3,4,5];
arr.copyWithin(0, 3, 4); // "[4,2,3,4,5]"
```

# 数组实例的find()和findIndex()
## 含义：
1.数组find()方法，用于找出第一个符合条件的数组成员，它的参数是一个回调函数，所有成员依次执行该回调函数，直到找到第一个返回值为true的成员，并返回，如果没有，则返回undefined。例如：
```
[1,2,3,-1,5].find((n) => n < 0); // "-1"
```

2.该回调函数可以传入三个参数，依次为当前值，当前索引，原数组。例如：
```
[1,2,3,4,5].find(function(value, index, arr){
    return value > 4;
}); // "5"
```

3.数组findIndex()方法和index()方法非常类似，返回第一个符合条件成员的索引位置，如果没有则返回-1。例如：
```
[1,2,3,4,5].findIndex(function(value, index, arr){
    return value > 4;
}); // 4
```

> 注：这两个函数都可以接受第二个参数，用来绑定回调函数的this对象。另外这两个方法都可以发现NaN，弥补了indexOf方法的不足。 

```
[NaN].findIndex( y => Object.is(NaN, y)); // 0
```

# 数组实例的fill()
## 含义：

1.fill方法使用给定值，填充一个数组。示例：
```
['a','b','c'].fill(7); // [7,7,7]
```

2.该方法还接受第二和第三个参数，用于指定填充的起始位置和结束位置。示例：
```
['a','b','c'].fill(7, 0, 1); // [7,'b','c']
```

> 注：该方法用于空数组初始化非常方便，数组中已有的元素会全部被抹去。 

# 数组实例的entries()，keys()和values()
## 含义：
1.这三个方法都返回一个iterator对象，可以用for...of来遍历，keys()是遍历键，values()是遍历值，entries()是遍历键值对。例如：
```
for(let key of ['a', 'b'].keys()){
    console.log(key);
}
// 0
// 1

for(let value of ['a', 'b'].values()){
    console.log(value)
}
// 'a'
// 'b'

for(let [key, value] of ['a', 'b'].entries()){
    console.log(key, value);
}
  // 0 'a'
  // 1 'b'
```

# 数组实例的includes()
## 含义：
1.Array.prototype.includes方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串includes方法类似。例如：
```
[1,2,3].includes(3); // true
[1,2,NaN].includes(NaN); //true
```

2.该方法的第二个参数表示搜索的起始位置，默认为0，如果第二个参数为负数，则表示倒数位置。例如：
```
[1,2,3].includes(2, 2); // false
```

> 注：includes()方法需要与Map和Set的has()方法进行区分。 

# 数组的空位
## 含义：
1.数组的空位指数组的某一个位置上没有任何值，注意，空位不是undefined。

2.ES6明确了将空位转换成undefined，但Array.from会将空位转换成undefined。例如：
```
Array.from([1,,2]); // [1, undefined, 2]
```

3.扩展运算符(...)也会将空位转换成undefined。例如：
```
[...[1,,2]] // [1, undefined, 2]
```

4.  copyWithin()会连空位一起拷贝，fill()会将空位视为正常的数组位置，for...of循环也会遍历空位。

> 注：由于空位处理的规则不统一，所以要避免出现空位。 
