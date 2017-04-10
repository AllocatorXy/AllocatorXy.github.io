---
layout:     post
title:      "ES6 解构(Destructuring)"
subtitle:   "让人头疼又很好使的解构"
date:       2017-03-01 03:39:00
author:     "AllocatorXy"
comments:   true
header-img: "img/js-bg.png"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
    - ES6
---

### 解构(Destructuring)
>Destructuring allows binding using pattern matching, with support for matching arrays and objects. Destructuring is fail-soft, similar to standard object lookup `foo["bar"]`, producing undefined values when not found.

解构允许绑定时使用模式匹配，支持匹配的有数组和对象。解构是fail-soft的，类似于标准对象的查询形如`foo["bar"]`, 当匹配不到时生成一个`undefined`.
<hr />

##### 模式匹配(Pattern matching)
模式匹配，指只要lhs和rhs模式(pattern)一样，就可以匹配到，比较难以用语言描述，直接看栗子就明白了：

```js
// 数组的模式匹配，可以跳过，但必须按照顺序匹配
let [ a, , b ] = [ 1, 2, 3 ]; // a = 1, b = 2

// 对象的解构赋值，可以不按顺序匹配
function today() { return { d: 1, m: 3, y: 2017 }; }
// m, y, d只是匹配的模式，真正被赋值的是后面的变量
let { m: month, y: year, d: day } = today(); // month = 3, year = 2017, day = 1
// 若变量名和属性名一样，可以简写
let { m, y, d } = today(); // m = 3, y = 2017, d = 1

// 可以有不完全匹配，不完全解构并赋值
let [ x, y ] = [1, 2, 3]; // x = 1, y = 2
let [ a, [ b ], d ] = [1, [2, 3], 4]; // a = 1, b = 2, d = 4

/* 但左右结构不可以不匹配 */
let [ a, { b } ] = [ 1 ]; // error
let [ a, { b } ] = [ 1, { 2 } ]; // a = 1, b = 2

```
<hr />

##### 参数解构
因为在传入实参时，实际上是对形参的赋值，所以也可以将解构用作参数：

```js
function g({ name: x }) {
  console.log(x);
}
g({ name: 5 }); // 5

// (1). { x = 0, y = 0 } = arg; // 首先实参解构赋值给形参
// (2). { x, y } = {};          // 然后{}解构赋值给(1)加工过的参数
function move({ x = 0, y = 0 } = {}) {
  return [ x, y ];
}
move({ x: 3, y: 8 }); // [3, 8]
move({ x: 3 });       // [3, 0]
move({});             // [0, 0]
move();               // [0, 0]

// (1). { x, y } = arg; // 首先实参解构赋值给形参
// (2). 然后{ x: 0, y: 0 }解构赋值给(1)加工过的参数，但模式匹配失败，所以空值会变成undefined不完全解构
function move({ x, y } = { x: 0, y: 0 }) {
  return [ x, y ];
}

move({ x: 3, y: 8 }); // [3, 8]
move({ x: 3 });       // [3, undefined]
move({});           // [undefined, undefined]
move();             // [0, 0]
```
<hr />

##### Fail-sort Destructuring
>**fail-soft**: A **fail-soft system** is a system designed to shut down any nonessential components in the event of a failure, but keep the system and programs running on the computer.

一个**fail-soft系统**是用来干这个的：当在一个失败的事件中存在不必要的成分时，终止一切不必要的内容引起的错误事件，但保持系统和程序可以继续在计算机上运行。

js中的解构类似于标准对象的查询形如`foo["bar"]`, 当匹配不到时生成一个`undefined`。<br />
**这里要注意的是**，只有在rhs**<font color="red">模式正确</font>**，并且该值**<font color="red">全等于undefined</font>(空值或`undefined`)**时，Fail-sort Destructuring 才会生效。<br />

```js
// fail-soft destructuring
let [ a ] = [];                         // a === undefined
let [ a, { b } ] = [ , { undefined } ]; // a === undefined, b === undefined

// error
let [ foo ] = 1;          // error
let [ foo ] = false;      // error
let [ foo ] = NaN;        // error
let [ foo ] = undefined;  // error
let [ foo ] = null;       // error
let [ foo ] = {};         // error
```

**如果rhs是`Number`或`Boolean`，则它们会被先转换成对象，但无法匹配到它们的值:**

```js
let { toString: a } = 6;
// function toString() { [native code] }, 取到了Number类的方法，说明转成了对象
let { a } = 6; 
// a === undefined, 6被转成对象，但模式a不能匹配到值

let { toString: a } = true; // function toString() { [native code] }
let { a } = true; // a === undefined
```
<hr />

##### 默认值
解构赋值是允许默认值的，若rhs有可匹配到的`undefined`, 则该匹配到的位置启用默认值：

```js
// 默认值生效
let [ foo = true ] = [];               // foo = true
let [ x, y = 'b' ] = [ 'a', ];          // x = 'a', y = 'b'
let [ x, y = 'b' ] = [ 'a', undefined ]; // x = 'a', y = 'b'

// 默认值不生效
let [ x = 1 ] = [ null ];   // x = null
```
<hr />

##### ArrayLike
ArrayLike对象，也可以进行解构，比如字符串：

```js
let [ a, b, c ] = 'qwe'; // a = 'q', b = 'w', c = 'e' 
```

ArrayLike对象也有`length`属性, 也可以将这个属性解构匹配赋值到变量上：

```js
let { length : x } = 'qwe'; // x = 3
```
<hr />

##### 圆括号
1. 声明部分不能有圆括号，包括函数的参数；
2. pattern部分不能有圆括号；

**只有非声明语句，且括号不罩pattern才能用：**

```js
[ (b) ] = [ 3 ];
({ p: (d) } = {});
[ (parseInt.prop) ] = [ 3 ];
```
<hr />

##### 作用

>快速交换变量值：

```js
let x = 1;
let y = 2;

[ x, y ] = [ y, x ];
```

>取出多个json值：

```js
let jsonData = {
  id: 42,
  status: "OK",
  data: [ 867, 5309 ]
};

let { id, status, data: number } = jsonData;
console.log(id, status, number); // 42, "OK", [867, 5309]
```

>遍历map：

```js
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (const [ key, value ] of map) {
  console.log(key + " is " + value);
}

// 单独获取键值
for (const [ ,value ] of map) {
  // ...
}
```

etc...
<hr />
参考文献: <a href="https://github.com/lukehoban/es6features">es6features</a>, <a href="http://es6.ruanyifeng.com/">ECMAScript 6 入门</a>