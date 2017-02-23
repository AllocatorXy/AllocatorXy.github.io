---
layout:     post
title:      "js中的参数传递，传值与传址"
subtitle:   "什么是形参和实参，传值和传址两种参数传递有何不同？"
date:       2017-02-16 1:04:00
author:     "AllocatorXy"
comments:   true
catalog:    true
header-img: "img/post-bg-js-module.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
---

#### parameter&argument
>**形式参数(parameter)**是函数定义时的参数;<br />
>**实际参数(argument)**是函数调用时实际传入的参数。

```javascript
let n1 = 10;
let a1 = [ 'a', 'b', 'c' ];
function test(num, arr) {
    num = 20;
    arr[1] = 'z';
    console.log(num);       // 20
    console.log(arr[1]);    // z
}
test(n1, a1);
```

声明函数时，`test(num, arr)` <= 这里`num`和`arr`就是test函数的**形式参数(parameter)**<br />
调用函数时，`test(n1, a1)` <= 这里`n1`和`a1`就是调用时传入的**实际参数(argument)**
<hr />

#### arguments对象
arguments对象用于获取函数调用时的**实际参数(argument)**;

>需要注意：arguments是一个对象，而不是数组；<br />
>它具有length属性，它的长度由实参数量决定；

```javascript
let n1 = 10;
let a1 = [ 'a', 'b', 'c' ];
function test() {
    arguments[0] = 20;
    arguments[1][1] = 'z';
    console.log(arguments[0]);       // 20 <= 运行结果没有改变
    console.log(arguments[1][1]);    // z  <= 运行结果没有改变
}
test(n1, a1);
```
上面的栗子可以看出：<br />
1. 用arguments直接代替形参，函数运行的结果没有改变；<br />
2. **形式参数(parameter)**可以少于**实际参数(argument)**，甚至可以没有。

**实际上**，**形式参数(parameter)**与**实际参数(argument)**并没有直接关系，**实参少于形参**也是可以的，未匹配的形参将被赋值`undefined`;

```javascript
let n1 = 10;
let a1 = [ 'a', 'b', 'c' ];
function test(num, arr, a) {
    num = 20;
    arr[1] = 'z';
    console.log(num);       // 20 <= 运行结果没有改变
    console.log(arr[1]);    // z  <= 运行结果没有改变
    console.log(a);         // undefined <= 未匹配的形参
}
test(n1, a1);
```
<hr />

#### 传值与传址
上面的栗子有一个有趣的现象，打印函数执行后的`n1`和`a1`发现，`n1`的值**没有改变**，而`a1`**却被改变了**，`n1`传进函数作为局部变量仅在函数内部改变，而**全局变量**`a1`却被改变了。

```javascript
let n1 = 10;
let a1 = [ 'a', 'b', 'c' ];
function test(num, arr) {
    num = 20;
    arr[1] = 'z';
    console.log(num);       // 20
    console.log(arr[1]);    // z
}
test(n1, a1);
console.log(n1);            // 10 <= n1没有被改变
console.log(a1[1]);         // z  <= a1[1]被函数改变了
```
<hr />

如何理解传值与传址呢？看两个更为直接的栗子吧。

###### 传值(pass-by-value)
```javascript
let a = 1
let b = a;
b = 9;
alert(a);   // 1
```
a的值**没有变化**，在这个栗子中，a的值赋给b，然后得到a赋值的b被赋值为9，但最后a的值并没有发生改变;

>a的值被操作过程中，对**实际的值**做了一份**复制**，这份复制存在b中<br />
>**复制的值**和**原来的值**是两份独立的值，修改复制的值，原值自然不会发生改变。
<hr />

###### 传址(pass-by-address)
```javascript
let objA = new Object();
objA.value = 1;

let objB = objA;
objB.value = 9;
alert(objA.value);   // 9
```
两个**相同的例子**，只是把数据类型换成了对象，就有了不一样的结果;<br />
如果按照传值的逻辑，这里修改的是objB的值，为什么objA也跟着变了呢？

>`let objB = objA;`将objA的值赋给objB，同样**也会复制一份**objA的原值存到新变量objB的空间中;<br />
>不同的是，这个值的副本实际上是一个指针，指针指向原值的对象，当改变其中任一个变量(或对象的属性)时，另一个的值也会发生变化。
<hr />

##### 结论
JS的参数是**按值传递**的;<br />
如果参数传的是**`基本类型`**的值如(Number, Boolean, String)，那么传的是值的副本，**副本的值与原值没有直接联系**;<br />
如果传的是**`引用类型`**的值如(Object, Array, Function)，那么传递的其实是**`指针`**，当改变其中任一个变量(或对象的属性)时，另一个的值**也会发生变化**。

**特殊情况**：

```js
/* null虽然是object，但它不占内存，地址为空所以是传值 */
let n = null;
let b;
b = n;
b = 2;
alert(n); // null

/* 这种情况弹出1,2,3是因为给arr2重新赋值而不是对现有的arr2[x]进行操作 */
let arr = [ 1, 2, 3 ]; // eaqual: let arr = new Array(1,2,3);
let arr2 = arr;
arr2 = [];             // eaqual: let arr2 = new Array();
alert(arr); // 1,2,3
```
