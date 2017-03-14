---
layout:     post
title:      "js闭包(closure)"
subtitle:   "闭包是什么？是用来做什么的？"
date:       2017-02-15 16:50:00
author:     "AllocatorXy"
comments:   true
header-img: "img/post-bg-js-closure.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
---

### js闭包(closure)
>闭包原理：js中，子函数可以访问父函数的局部变量。

#### 闭包是什么？
在函数**内部**再声明一个函数，调用父函数的局部变量，就形成了闭包。

```javascript
function fn() {
    let i = 0;

    (function fn1() { // <= fn1就是闭包
        console.log(i); // console: 0
    })();
}
```
`fn`的变量`i`，对于`fn1`来说是可见的，所以`fn1`访问到了`i`;<br />
**`fn1`**就是一个**闭包**。
<hr />

#### 闭包的作用
闭包最常用的场合，就是**读取函数的内部变量**，并且让这些变量的值**始终保存**在内存中，而不与其他全局变量冲突，我们可以用它做某个函数内部变量的setter或getter;

```javascript
function fn() {
    let i = 0;

    function fn1() { // <= 闭包fn1
        i++;
        console.log(i);
    }
    return fn1;      // 返回闭包fn1
}
let res = fn(); // res <= fn1
res();          // console: 1
res();          // console: 2
res();          // console: 3
```
可以发现，虽然`i`是`fn`的局部变量，但它并没有在内存中被回收，而是**一直存在**于内存中的，<br />
连续执行了三次闭包，**如果**它被回收了，那么结果应该是三次1，<br />
但结果是`i`做了累加，说明`i`没有被销毁。<br />
**这是为什么呢？**<br />
闭包被赋给了一个全局变量`res`，这导致闭包`fn1`一直被存在内存中，而`fn1`的存在依赖于父函数`fn`，所以`fn`每次执行后并没有被销毁，`i`也就没有被销毁。
<hr />

#### !!attention!!
由于闭包会使父函数中的局部变量一直保存在内存中，内存消耗是相当大的，所以不要大量使用闭包，否则会有严重的性能问题，在IE中可能导致内存泄漏；<br />
应在退出函数前，将不要的局部变量**删除**；<br />
或在使用完闭包后，将储存闭包的变量**删除**。
