---
layout:     post
title:      "js return一个函数会得到什么?"
subtitle:   "执行? string? function?"
date:       2017-02-13 13:06:00
author:     "AllocatorXy"
comments:   true
header-img: "img/post-bg-js-module.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
---

### return function

偶然发现，js在return时，返回的是函数的情况，这个函数并**没有执行**;

```javascript
function a() {
    console.log('msg0');
    return function () {
        console.log('msg1');
    };
}
a(); // console: msg0
```

用res接收a函数的返回值，发现得到了返回函数的**代码**;<br />
那么，这个返回函数没有被执行的原因，难道是因为被转化成了一段**字符串**？

```javascript
function a() {
    console.log('msg0');
    return function () {
        console.log('msg1');
    };
}
let res = a(); // console: msg0
alert(res);    // function () {console.log('msg1');}
```
<hr />
查看res的类型，发现是function，**并没有被转换成字符串**;<br />
看来，这个return只是返回了这个匿名函数对象，return没有触发这个函数；

```javascript
function a() {
    console.log('msg0');
    return function () {
        console.log('msg1');
    };
}
let res = a(); 
alert(typeof res); // function
// res: function () {console.log('msg1');}
```

验证猜想，果真如此，返回值不管是**数字**还是**函数**，都没有被转成字符串，而是传回了number;<br />
**但return并不会触发函数**，如果非要返回的函数执行，需要手动执行;

```javascript
function a() {
    console.log('msg0');
    return function () {
        console.log('msg1');
        return 1;
    };
}
let res = a()();       // 将返回值直接执行 -> console: msg0 msg1
alert(res);            // 1
alert(typeof res);     // number
```

后来发现,对象s并没有被隐式转换，console.log打印出来是**对象**，<br />
可是alert不知道为什么变成了**'msg'**;

```javascript
let s = 'msg';
String.prototype.a = function() { // string原型函数返回this
    return this;
};
console.log(typeof s.a());  // object
console.log(s.a());         // String {0: "m", 1: "s", 2: "g", length: 3, [[PrimitiveValue]]: "msg"}
alert(s.a());               // msg
```
