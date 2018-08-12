---
title: JavaScript高级程序设计读书笔记-第5章
date: 2017-09-05 22:51:08
categories: JS高程笔记
tags:
     - 笔记
     - JavaScript
description: JavaScript高级程序设计读书笔记--第5章引用类型
---
# Object类型
引用类型是一种数据结构，用于将数据和功能组织在一起。
我们看到的绝大多数的类型值都是Object类型的实例，创建Object实例的方式有两种：
1. new操作符跟Object构造函数 `var person = new Object();`
2. 对象字面量表示法 ` var person = {};`

>注：通过字面量定义对象时，实际上不会调用Object的构造函数。

访问对象属性方法有两种：
1. 点表示法(推荐) `person.name`
2. 方括号表示法 `person["name"]`

# Array类型
数组也是对象的一种，有两种创建方式：
1. 使用Array的构造函数
2. 字面量表示法(推荐)

>注：数组的length属性不是只读的，可以调用Array.length移除元素或向数组中添加新项。 

## 1、检测数组
Array.isArray()方法，返回true或false。

## 2、转换方法
1. toString()方法返回数组中的每一项以逗号拼接而成的字符串。
2. valueOf()方法返回的是数组本身。
3. toLocaleString()方法与toString()相同，只不过返回的是每一项调用toLocaleString()的返回值。
4. join()方法使用指定的分隔符来构建字符串。例如：array.join("|");如果join的传入值为空或为undefined，则默认使用逗号作为分隔符。

## 3、栈方法
1. push()方法可以接收任意数量的参数，并把它们添加到数组末尾，返回修改后数组的长度(进栈方法)。
2. pop()方法可以从数组末尾移除一项，并返回移除的项(出栈方法)。

## 4、队列方法
1. shift()方法移除数组第一项并返回该项(出队方法)。
2. unshift()方法向数组前端添加任意项并返回新数组的长度(入队方法)。

## 5、重排序方法
1. reverse()方法会反转数组项的顺序。
2. sort()方法在默认情况下按升序排列数组，比较的是字符串，sort()方法还可以传入一个比较函数来自定义比较顺序。

>注：reverse()和sort()的返回值是经过排序后的数组。

## 6、操作方法
1. concat()方法可以基于当前数组中的所有项创建一个新数组，这个方法会先创建当前数组的副本，然后将接收到的参数添加到这个副本的末尾。
2. slice()方法基于当前数组中的一个或多个项创建一个新数组，slice()不会影响原数组，它可以接收一到两个参数，即要返回项的起始和结束位置。
3. splice()方法可以向数组中部插入项，并会对原始数组产生影响，返回一个数组。该返回的数组中包含原始数组中删除的项。用法如下：
```
//删除前两项
array.splice(0,2);
//从位置2开始插入字符串
array.splice(2,0,"red","green");
//删除位置2的项并从位置2开始插入字符串
array.splice(2,1,"red","green");
```

## 7、位置方法
1. indexOf()方法从数组的开头向后查找，没找到返回-1。
2. lastIndexOf()方法从数组的末尾向前查找，没找到返回-1。

>注：indexOf()和lastIndexOf()在查找时使用的是全等操作符===。

## 8、迭代方法
1. every()方法对数组中的每一项运行给定函数，如果该函数对每一项都返回true，则返回true。

```
var num = {1,1,1,1,1,1};
var result = num.every(function(item, index, array){
    return (item > 2);
})
alert(result); // false
```
2. filter()方法对数组中的每一项运行给定函数，返回该函数会**返回true的项**所组成的数组(过滤)。

```
var num = {1,2,3,4,5,1};
var result = num.filter(function(item, index, array){
    return (item > 2);
})
alert(result); // [3,4,5]
```
3. forEach()方法对数组中每一项运行给定函数，没有返回值，会对原始数组产生影响。

```
var num = [1,1,1,1,1];
num.forEach(function(item, index, array){
    // something happens
})
```
4. map()方法对数组中每一项运行给定函数，返回每次函数调用的结果组成的数组(批量操作)。

```
var num = [11,1,1,1,1];
var result = num.map(function(item, index, array){
    return item * 2;
});
alert(result); // [22,2,2,2,2]
```
5. some()方法对数组中每一项运行给定函数，如果该函数对任一项返回true，则返回true。

```
var num = [1,2,1,1];
var result = num.some(function(item, index, array){
    return item > 2;
})
alert(result); // true
```

## 9、归并方法
1. reduce()方法会从第一项开始，逐个遍历到最后，迭代数组所有项，然后构建一个最终返回值。

```
var values = [1,2,3,4,5];
var sum = values.reduce(function(prev, cur, index, array){
    // prev表示前面的累加值
    return prev + cur;
});
alert(sum); // 15
```
2. reduceRight()方法会从最后一项开始，向前遍历到第一项，迭代数组所有项，然后构建一个最终返回值。

```
var values = [1,2,3,4,5];
var sum = values.reduceRight(function(prev, cur, index, array){
    // prev表示前面的累加值
    return prev + cur;
});
alert(sum); // 15
```

# Date类型
创建日期对象`var date = new Date(); `
新创建的日期对象在不传入参数的前提下，自动获得当前的日期和时间。
ECMAScript提供了两个返回毫秒值的方法：
1. Date.parse()方法：接收一个表示日期的字符串，如果传入的字符串不能表示日期，则返回NaN。
2. Date.UTC()方法：接收的第一个参数是年份，第二个参数是基于0的月份，第三个是月中哪一天，第四个是小时数(0到23)，最后两个是分钟和毫秒数。例如：

```
var time = new Date(Date.UTC(2000, 0));// 2000年1月1日午夜零时
```
ECMAScript5中提供了Date.now()方法，返回表示调用这个方法时的日期和时间的毫秒数，我们可以借用这个方法来获得时间戳，如下所示：

```
var start = Date.now();
var stop = Date.now();
var res = start - stop;
```

## 1、继承的方法
Date类型也重写了toLocaleString()、toString()和valueOf()方法，着重说下valueOf()方法，该方法不返回字符串，而是返回日期的毫秒值，因此可以使用比较操作符来比较日期。

## 2、日期格式化方法
1. toDateString()方法：以特定的格式显示星期几、月、日和年。
2. toTimeString()方法：以特定的格式显示时、分、秒和时区。
3. toLocaleDateString()方法：以特定的地区格式显示星期几、月、日和年。
4. toLocaleTimeString()方法：以特定的地区格式显示时、分、秒。
5. toUTCString()方法：以特定的格式显示完整的UTC日期。

## 3、日期/时间组件方法
常用的几个：
1. getTime()方法：返回日期的毫秒数，与valueOf()方法返回相同。
2. setTime()方法：以毫秒数设置日期。
3. getDate()方法：返回月份中的天数。
4. getDay()方法：获得星期几(0表示周日)。
5. getFullYear()方法：返回4位数的年份。

# RegExp类型
创建一个正则表达式`var expression = /pattern/flags `
flags有三个标志：
1. g：表示全局模式，即模式将被应用于所有字符串。
2. i：表示不区分大小写模式。
3. m：表示多行模式，即在到达一行文本末尾时还会继续查找下一行中是否存在于模式匹配的项。
示例如下:

```
// 匹配字符串中所有'at'的实例
var pattern1 = /at/g;
// 匹配第一个'bat'或'cat'，不区分大小写
var pattern2 = /[bc]at/i;
```

>注：使用字面量创建正则表达式不需要加引号，正则表达式中所有的元字符都必须使用\转义。 

## 1、RegExp实例属性
1. global:布尔值，是否设置了g标志。
2. ignoreCase:布尔值，是否设置了i标志。
3. lastIndex:整数，表示开始搜索下一个匹配项的字符位置，从0算起。
4. multiline:布尔值，是否设置了m标志
5. source:正则表达式的字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回。
例如：

```
var pattern =/\[bc\]at/i;
console.log(pattern.global); // false
console.log(pattern.ingoreCase); // true
console.log(pattern.lastIndex); // 0
console.log(pattern.multiline); // false
console.log(pattern,source); // "\[bc\]at"
```

## 2、RegExp实例方法
1. exec()方法：接受一个参数，即要进行捕获的字符串，返回包含第一个匹配项信息的数组，如果没有匹配项则返回null。
例如：

```
var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;
var matches = pattern.exec(text);
console.log(matches[0]); // "mom and dad and baby"
console.log(matches[1]); // "and dad and baby"
console.log(matches[2]); // "and baby"
```
对于exec()，即使在模式中设置了全局标志，它每次也只返回一个匹配项，**在不设置全局标志的情况下，在同一字符串上多次调用exec()将始终返回第一个匹配项**。如果设置了全局标志，每次调用exec()则都会在字符串中继续查找新的匹配项。如下所示：

```
var text = "cat bat sat fat";
var pattern = /.at/gi;
var matches = pattern.exec(text);
console.log(matches[0]); // cat
matches = pattern.exec(text);
console.log(matches[0]); // bat
```
2. test()方法：接受一个字符串，如果该字符串参数与模式相匹配返回true，否则返回false。经常被用在if语句中，例如：
```
var text = "000-00-0000";
var pattern = /\d{3}-\d{2}-\d{4}/;
if(pattern.test(text)){
    console.log("text is text");
}
```
3. toLocaleString()与toString()方法：返回正则表达式的字面量形式的字符串。
4. valueOf()方法：返回正则表达式本身。

## 3、RegExp构造函数属性
## 4、模式的局限性
# Function类型
函数实际上就是对象，每个函数都是Function的实例。由于函数是对象，所以函数名是指向函数对象的指针，声明一个函数有三种方式：
1. 函数声明：
```
function sum(num1, num2){
    return num1 + num2;
}
```
2. 函数表达式：
```
var sum = function(num1, num2){
    return num1 + num2;
};
```
3. 构造函数(不推荐)：
```
var sum = new Function("num1", "num2", "return num1 + num2");
```

## 1、没有重载
由于函数名是指针，所有创建相同的函数名会覆盖前面所创建的函数名。
## 2、函数声明与函数表达式
解析器会率先读取函数声明，并使其在执行任何代码之前可用(提升机制)；至于函数表达式，则必须等到解析器执行到它所在的代码行，才会被真正的解释执行。
例如：
```
alert(sum(1,1));// 可以执行
function sum(num1, num2){
    return num1 + num2;
}

alert(sum1(1,1)); // 会产生错误
var sum1 = function(num1, num2){
    return num1 + num2;
};
```

## 3、作为值的函数
因为函数名本身是变量，所以函数也可作为值来使用，如下所示：
```
function callSomeFunction(someFunction, someArgument){
    return someFunction(someArgument);
}

function add10(num){
    return num + 10;
}

var result = callSomeFunction(add10, 10);
console.log(result); // 20
```
我们可以用函数中返回另一个函数这种技术来为对象数组排序，如下所示：
```
function createComparisonFunction(propertyName){
    return function(object1, object2){
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];
        if(value1 < value2){
            return -1;
        }else if(value1 > value2){
            return 1;
        }else{
            return 0;
        }
    }
}
var data = [{name: "zhang", age: 18}, {name: "li", age: 19}];
date.sort(createComparisonFunction(name));
alert(data[0].name); // li
```

## 4、函数的内部属性
函数的内部有两个特殊的对象：arguments和this。
1. arguments对象：类数组对象，包含着传入函数的所有参数，还有一个callee属性，该属性是一个指针，指向拥有这个arguments对象的函数。例如：
```
function factorial(num){
    if(num <= 1){
        return 1;
    }else{
        return num * arguments.callee(num - 1);
    }
}
```
2. this对象：函数执行的环境对象，全局函数this对象的引用就是window。
3. caller对象：保存着调用当前函数的引用，如果是在全局作用域中调用当前函数，则它的值为null。

## 5、函数属性和方法
1. length属性：表示函数希望接受命名参数的个数。
```
function sayName(name){
    alert(name);
}
console.log(sayName.length); // 1
```
2. prototype属性：保存所有实例方法，例如toString()、valueOf()等。
3. apply()方法：设置函数体内this的值，接收两个参数，第一个为运行函数的作用域，第二个为参数数组，也可以为arguments对象。例如:
```
function sum(num1, num2){
    return num1 + num2;
}
function callSum(num1, num2){
    return sum.apply(this, [num1, num2]);
}
console.log(callSum()); // 20
```
4. call()方法：与apply类型，但接收参数不同，第一个为this值，其余参数必须逐个列举出来，例如：
```
function sum(num1, num2){
    return num1 + num2;
}
function callSum(num1, num2){
    return sum.call(this, num1, num2);
}
alert(callSum(10, 10)); // 20
```
5. bind()方法：创建一个函数的实例，其this值会被绑定到传给bind()函数的值，例如：
```
window.color = "red";
var o = {
    color: "blue"
};
function sayColor(){
    alert(this.color);
}
var objectSayColor = sayColor.bind(o);
objectSayColor(); // blue
```

# 基本包装类型
基本包装类型与引用类型的区别在于对象的生存期，引用类型的实例在执行流离开当前作用域之前一直保存在内存中，而基本包装类型在内存中存只存在执行声明时的一瞬间，然后被立即销毁，所以基本包装类型不能在运行时添加属性和方法。
## 1、Boolean类型
建议永远不要使用
基本类型和引用类型的布尔值有两个区别：
1. typeof对基本类型使用返回"boolean"，而对引用类型返回"object"。
2. instanceof测试Boolean会返回true，而测试基本类型会返回false。

## 2、Number类型
toFixed()方法：按照指定小数位返回数值的字符串表示，适合处理货币值。例如：
```
var num = 10;
console.log(num.toFixed(2)); // "10.00"
```

toPrecision()方法：返回固定大小格式，接收一个参数，该参数为表示所以数值位数，例如：
```
var num = 99;
alert(num.toPrecision(1)); // "1e+2"
alert(num.toPrecision(2)); // "99"
alert(num.toPrecision(3)); // "99.0"
```

## 3、String类型
字符串的对象包装类型，有length属性，表示字符串中多少个字符数。
1. 字符方法：charAt()接收一个参数返回指定位置的字符，charCodeAt()接收一个参数返回指定位置的字符编码。charAt()可以被字符串加方括号的形式代替(string[?])。
2. 字符串操作方法：concat()将一个或多个字符串拼接起来，返回拼接得到的新字符串，推荐使用+拼接字符串。slice(开始位置，结束位置)，substring(开始位置，结束位置)，substr(开始位置，截取个数)。如果传入的参数是负值，则slice()会将负值与字符串长度相加；substr()将负的第一个参数加上字符串长度，将负的第二个参数转换为0；substring()会把所有的负值转换为0.例如：
```
var str = "hello world";
alert(str.slice(3)); // "lo world"
alert(str.substring(3)); // "lo world"
alert(str.substr(3)); // "lo world"
alert(str.slice(3, 7)); // "lo w"
alert(str.substring(3, 7)); // "lo w"
alert(str.substr(3, 7)); // "lo worl"
alert(str.slice(-3)); // "rld"
alert(str.substring(-3)); // "hello world"
alert(str.substr(-3)); // "rld"
alert(str.slice(3, -4)); // "lo w"
alert(str.substring(3, -4)); // "hel"
alert(str.substr(3, -4)); // ""
```
3. 字符串位置方法：indexOf()从开头搜索返回搜索到的子字符串位置，lastIndexOf()从末尾搜索返回搜索到子字符串的位置。这两个方法都接收可选的第二个参数，表示从字符串中哪个位置开始搜索。例如：
```
var str = "hello world";
alert(str.index("o", 6)) // 7
alert(str.lastIndexOf("o", 6)) // 4

var string = "Lorem ipsum dolor sit amet, constectetur adipisicing elit";
var positions = new Array();
var pos = string.indexOf("e");

while(pos > 1){
    positions.push(pos);
    pos = string.indexOf("e",pos + 1);
}
```
4. trim()方法：创建一个字符串副本，删除前置及后缀所有空格，返回字符串副本。
5. 字符串大小写转换方法：toLowerCase()和toUpperCase()，还有两个针对地区的大小写转换方法toLocaleLowerCase()和toLocaleUpperCase()。
6. 字符串的模式匹配方法：match()方法接收一个正则表达式参数，返回一个字符串捕获数组。
search()方法接收一个正则表达式参数，返回字符串中第一个匹配的索引项。replace()方法接收两个参数，第一个参数可以是字符串或正则表达式，第二个参数可以是一个字符串或一个函数，并返回一个新字符串。split()方法接收一个分隔符，将字符串分割成字符数组返回，还可以接收第二个参数，用于指定分割数组的大小。这四个方法示例如下：
```
var str = "hello world";
alert(str.toUpperCase()); // "HELLO WORLD"
alert(str.toLowerCase()); // "hello world"
alert(str.toLocaleUpperCase()); // "HELLO WORLD"
alert(str.toLocaleLowerCase()); // "hello world"

var text = "cat bat sat fat";
var matches = text.match(/.at/);
console.log(matches[0]); // "cat"

var result = text.replace("at", "end");
console.log(result); // "cond bat sat fat"
result = text.replace(/at/g "ond");
console.log(result); // "cond bond sond fond"

function htmlEscape(text){
    return text.replace(/[<>"&]/g, function(match, pos, originalText){
        // match为匹配项
        switch(match){
            case "<":
                return "&lt";
            case ">":
                return "&gt";
            case "&":
                return "&amp";
            case "<":
                return "&quot";
        }
    });
}

var test = "red,blue,black";
var arr = test.split(",", 2);
alert(arr); // ["red", "blue"]
```
7. localeCompare()方法：比较两个字符串，返回-1、0和1，返回值取决于比较参数。例如：
```
var str = "yellow";
alert(str.localeCompare("brick")); // 1
```
8. fromCharCode()方法：字符串的静态方法，根据一个或多个编码返回字符串。
9. HTML方法：尽量不使用。

# 单体内置对象
## 1、Global对象
1. URI编码方法：encodeURI()和encodeURIComponent()对URI进行编码，encodeURI()用于整个URI，不会对本身属于URI的特殊字符进行编码，例如冒号、正斜杠、问号和井字号。encodeURIComponent()用于URI中的某一段进行编码，会对它发现的任何非标准字符进行编码。与这两个方法与之相对就是decodeURI()和decodeURIComponent()这两个解码方法。例如：
```
var uri = "http://www.wrox.com/illegal value.htm#start";
alert(encodeURI(uri)); // "http://www.wrox.com/illegal%20value.htm#start"
alert(encodeURIComponent(uri)); // "http%3A%2F%2Fwww.wrox.com%2Fillegal%20.."
```
2. eval()方法：慎用！
3. Global对象的属性：undefined NaN Infinity Object Array Function Boolean String Number Date RegExp Error EvalError RangeError ReferenceError SyntaxError TypeError URIError
4. window对象：在浏览器中将Global对象作为window对象的一部分加以实现。


## 2、Math对象
1. Math对象常用属性：Math.PI圆周率的值。
2. min()和max()方法：确定一组数据的最大最下值。例如：
```
var min = Math.min(1,2,3,4);
alert(min); // 1
```
3. 舍入方法：Math.ceil()向上取整，Math.floor()向下取整，Math.round()四舍五入。
4. random()方法：返回大于等于0小于1的一个随机数。例如：
```
var num = Math.floor(Math.random() * 10 + 5); // 返回5-10的随机数
```
5. 其他方法：Math.abs()取绝对值，Math.sqrt()取平方根。
