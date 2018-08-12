---
title: ES6入门-Class
date: 2017-12-08 21:52:56
categories: ES6入门
tags:
     - 笔记
     - JavaScript
     - ECMAScript 2015
description: ES6入门--Module
---
# 一、Class简介

## 简介

1.ES6的class可以看作是一个语法糖，只是让对象原型的写法更清晰，更像面向对象编程的语法而已。例如：
```
class Point {

    // 构造方法
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    // 方法之间不需要加逗号
    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}
```

2.ES6的类，完全可以看作是构造函数的另一种写法，例如：
```
class Point {

}

// true
Point === Point.prototype.constructor
```

3.ES6类的所有方法都定义在类的prototype属性上面，例如：
```
class Point {
    // 定义在类的prototype属性上，也就是在原型上
    constructor() {}

    // 定义在类的prototype属性上，也就是在原型上
    toString() {}
}
```

4.使用Object.assign可以一次向类添加多个方法，例如：
```
Object.assign(Point.prototype, {
    a() {},
    b() {},
    c() {}
});
```

5.ES6定义的类内部所有定义的方法都是不可枚举的，也就是说不能使用Object.keys()遍历。但ES5中可以被枚举，这点要注意！

6.类和模块的内部，默认就是严格模式。

7.ES6中定义的类，必须使用new调用，否则会报错。ES5中定义的构造函数不用new也可以使用。

8.ES6中的类不存在变量提升，这一点与ES5不同。

# 二、constructor方法

## 简介

1.constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。

2.一个类必须有constructor方法，如果没有显式定义，JavaScript引擎则默认添加一个空的constructor方法。例如：
```
class Point {

}

// 等同于
class Point {
    constructor() {}
}
```

3.constructor方法默认返回实例对象this，也可以指定返回另外一个对象。例如：
```
class Foo {
  constructor() {
    return Object.create(null);
  }
}
```

# 三、类的实例对象

## 简介

1.与ES5一样，实例的属性除非显式定义在其本身（即定义在this对象上），否则都定义在原型上。例如：
```
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  },
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

var point = new Point(2, 3);

// 显式定义在其本身，返回true
point.hasOwnProperty('x');
point.hasOwnProperty('y');

// 默认定义在原型上，返回false
point.hasOwnProperty('toString');

// true
point.__proto__.hasOwnProperty('toString');
```

2.与ES5一样，类的所有实例共享一个原型对象，例如：
```
let p1 = new Point(2, 3);
let p2 = new Point(3, 5);

p1.__proto__ === p2.__proto__;
```

3.我们可以通过实例的__proto__来获得原型对象，但是不建议在生产环境中使用该属性。推荐使用Object.getPrototypeOf()来获得实例的原型对象，再为原型添加属性和方法。

# 四、class表达式

## 简介

1.与函数一样，类也可以使用表达式的形式定义，例如：
```
// 类的名字是MyClass，而不是Me，Me只在内部可用，指代当前类
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
}
```

2.采用Class表达式，可以写出立即执行的Class。例如：
```
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('jack');

// jack
person.sayName();
```

# 五、私有方法与私有属性

## 简介

1.ES6不提供私有方法，只能通过变通方法模拟实现，方法有三种：
(1)命名上加以区别，这种做法是不保险的，在类的外部还是可以访问到。例如：
```
class Widget {
  foo(baz) {
    this._bar(baz);
  }

  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }
}
```

(2)将私有方法移出模块。例如：
```
class Widget {
  foo(baz) {
    bar.call(this, baz);
  }
}

function bar(baz) {
  return this.snaf = baz;
}
```

(3)利用Symbol的唯一性。例如：
```
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass {
  foo(baz) {
    this[bar](baz);
  }

  [bar](baz) {
    return this[snaf] = baz;
  }
};
```

2.与私有方法一样，ES6不支持私有属性，但是有一个提案，方法是在属性名前加#，表示私有属性。例如：
```
class Point {
  #x;
}
```

3.私有属性可以有初始值，在构造函数执行时进行初始化。例如：
```
class Point {
  #x = 0;
  constructor {
    #x; // 0
  }
}
```

# 六、this的指向

## 简介

1.类的方法内部如果含有this，它默认指向类的实例。但是要非常小心，一旦单独使用该方法，有可能会报错。例如：
```
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
printName(); // 报错
```

2.解决上述问题的方法有三种，绑定this，使用箭头函数和使用Proxy。

# 七、class的getter与setter

## 简介
与ES5一样，在'类'的内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为，例如：
```
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }

  get html() {
    return this.element.innerHTML;
  }

  set html(val) {
    this.element.innerHTML = val;
  }
}
```

# 八、class的静态方法

## 简介

1.class的静态方法，使用static关键字来命名的，可以直接通过类来调用，例如：
```
class Foo {
  static classMethod() {
    return 'hello';
  }
}
Foo.classMethod();
```

2.如果静态方法中有this关键字，这个this指的是类，而不是类的实例。

3.父类的静态方法可以被子类继承。例如：
```
class Foo {
  static classMethod() {
    return 'hello';
  }
}
class Bar extends Foo {}
Bar.classMethdo(); // 'hello'
```

4.静态方法可以从super对象上调用。例如：
```
class Foo {
  static classMethod() {
    return 'hello';
  }
}
class Bar extends Foo {
  static classMethod() {
    return super.classMethod + ', too';
  }
}
Bar.classMethod(); // hello too
```

# 九、class的静态属性和实例属性

## 介绍

1.静态属性指的是Class本身的属性，即Class.proName，而不是定义在实例对象this上的属性。例如：

```
class Foo {}
Foo.prop = 1;

// 1
Foo.prop
```

2.ES6明确规定，Class **内部** 只有静态方法，没有静态属性。例如：

```
class Foo {
  prop: 2
  static prop: 2
}

// undefined
Foo.prop
```

## 新的提案

目前有一个新的提案，对实例属性和静态属性规定了新的写法。 

1.类的实例属性

```
class MyClass {
  myProp = 2;
  constructor() {
    // 2
    console.log(this.myProp);
  }
}
```

2.类的静态属性 类的静态属性，需要在实例实例属性的写法前面加上static关键字。例如：

```
class MyClass {
  static prop = 1;
  constructor() {
    // 1
    console.log(MyClass.prop);
  }
}
```

> 新的提案解决了老写法容易忽略静态属性的问题，采用显式声明，语义更加优秀。

# 十、new.target属性

## 介绍

1.ES6为new命令引入了一个new.target属性，该属性一般用在构造函数之中，返回new命令作用于的那个构造函数。如果构造函数不是通过new命令调用的我，new.target会返回undefined。例如：

```
function Person(name) {
  if (new.target !== undefined) {
    this.name = name;
  } else {
    throw new Error('必须使用new命令生成实例！');
  }
}

// 正确
var person = new Person('jack');
// Error
var person = Person.call(person, 'jack');
```

2.Class内部调用new.target，会返回当前Class。例如：

```
class Person {
  constructor(name) {
    console.log(new.target === Person);
    this.name = name;
  }
}
// true
var person = new Person();
```

3.子类继承父类时，new.target会返回子类。例如：

```
class Person {
  constructor(name) {
    console.log(new.target === Person);
    this.name = name;
  }
}

class Child extends Person{
  constructor(name) {
    super(name);
  }
}

// false
var person = new Child('jack');
```

4.利用new.target的特点可以实现抽象类，只能被继承的类。例如：

```
class Person {
  constructor() {
    if (new.target === Person) {
      throw new Error('本类不能实例化');
    }
  }
}

class Child extends Person {
  constructor(name) {
    super();
    this.name = name
  }
}

var a = new Child();

// 报错
var b = new Person();
```
