---
layout:     post
title:      "作用域与提升"
subtitle:   "详解变量提升与函数提升"
date:       2017-02-17 11:25:00
author:     "AllocatorXy"
comments:   true
catalog:    true
header-img: "img/post-bg-tree.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
---

#### 作用域(Scoping)
在ES6之前，JavaScript是**没有**块级作用域的，只有**<font color="red">全局作用域</font>**和**<font color="red">函数作用域</font>**，var指令实际上是全局声明，但可以被函数作用域限制；

```javascript
    var a = 1; // globally scoped
    // b is not visible here

function fn() {
    // a is visible here
    var b = 2; // function block scoped
}
```
<hr />

#### 提升(Hoisting)
>在js中，声明变量和直接声明函数，会发生提升现象，声明会被提到当前作用域最高处;

##### 变量提升
这是一个很普通的函数，alert全局变量`str`；

```javascript
var str = 'Hello World'; 
(function() { 
    alert(str); // Hello World
})();
```
<hr />

如果在alert后面`var`一个同名的局部变量，居然不能正常alert了，这就看起来很诡异了，因为我们知道，js的代码是由上至下运行的，就算是在这个**<font color="red">函数作用域内部</font>**的局部变量`str`覆盖了全局变量，也应该是先弹出`Hello World`才符合常识；<br />
就算是覆盖掉了，并且弹出这个局部变量，也应该是弹出`test`而不是`undefined`;

```javascript
var str = 'Hello World'; 
(function() { 
    alert(str); // undefined
    var str = 'test';
})();
```
<hr />

这就牵扯到了js的**<font color="red">变量提升</font>**<br />
>在声明变量时，**<font color="red">声明</font>**会被**提升**到当前作用域的顶部，但**<font color="red">变量值</font>****不会被提升**。

于是上面的代码**预编译(Precompile)**后成了这样：

```javascript
var str = 'Hello World'; 
(function() { 
    var str;    // 声明被提升到了函数作用域顶部，在域内覆盖了全局变量，但此时没有被赋值
    alert(str); // undefined
    str = 'test';
})();
```

来个更直观的栗子：

```js
// 未编译                           // 预编译后
(function() {                      (function() {
    alert(a+b+c);      ===>            var a,b,c;
    var a = 1;         ===>            alert(a+b+c); // undefined
    var b = 2;         ===>            a = 1;    
    var c = 3;                         b = 2;    
})();                                  c = 3;
                                   })();
                                   
```
<hr />

##### 函数提升
- 类似于变量提升，js中函数声明也会<font color="red">提升(Hoisting)</font>;
- 不同的是，函数提升会将**<font color="red">整个函数声明</font>**(包括函数体)提升到当前作用域的顶部，这可以使我们**先**使用函数，**后**声明函数。
- **<font color="red">函数提升的优先级高于变量</font>**

```js
fn();    // bingo
function fn() {
    alert('bingo');
};
```

**但将声明的函数存储到一个变量中是<font color="red">不会</font>被提升的(变量提升不会提升变量值)**：

```js
alert(typeof fn2); // undefined
fn2();   // typeError: fn is not a function
var fn2 = function fn2() {
    alert('msg');
};
```
