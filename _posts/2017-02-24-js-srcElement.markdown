---
layout:     post
title:      "事件源与事件委托"
subtitle:   "事件委托绝不止是用来干掉迭代添加事件这么简单"
date:       2017-02-24 01:46:00
author:     "AllocatorXy"
comments:   true
header-img: "img/post-bg-os-metro.jpg"
tags:
    - 前端开发
    - JavaScript
---

### 事件源
事件源指**<font color="red">第一个</font>**触发事件的对象。
<hr />

##### 获取事件源
```js
function a(e) {
    const oE = e || event;
    const oS = oE.srcElement || oE.target; // 获取事件源
}
```
<hr />

##### 事件委托
事件冒泡会触发父级元素的事件，所以通过在父级绑定事件获取事件源，再对事件源进行操作，可以**间接实现**对子级的事件绑定，这叫做**事件委托**，事件委托有两个最主要的好处：

1. 提高性能(减少不必要的迭代);
2. 对父级的innerHTML(所有子代)进行操作，不需要重新绑定事件;

比如我们可以这样(事件委托给ul，使点击li时，被点击的li变红)：

```js
oUl.onclick = function(ev) { // 添加点击事件给父级
    var oEvent = ev || event;
    var oS = oEvent.srcElement || oEvent.target; //  获取点击的事件源
    if (oS.tagName == 'LI') { // 匹配到需要元素
        oS.style.background = 'red';
    }
};
```

**父级事件触发 => 获取事件源 => 匹配后代元素 => 执行所需代码**

这样我们在实现这种以前需要迭代添加事件的情况时，就可以不用迭代了，性能大大提高；<br />
同时，我们在对子代进行dom操作时也不用担心子代事件的问题了；

**思考：如果在上面的点击事件中，有什么东西层级比li高挡住li，我们要的效果会出现么？**<br />
    -> 发现这样是不能触发的，事件源会变成被挡住的东西，所以事件委托是不能穿透的；

**<font color="red">仔细想想</font>**，其实事件委托只是用了事件冒泡和获取事件源的原理，那么通过委托给父级来给子级绑定事件的启发，我们可以延伸出很多用法。。。而不仅仅是用来匹配后代，事件委托可以是这样的：

**父级事件触发 => 获取事件源 => 匹配~~<font color="red"> 后代 </font>~~元素 => 执行所需代码**

比如。。点击子级通过dom查找操作父级:

```js
oUl.onclick = function(ev) {
    var oEvent = ev || event;
    var oS = oEvent.srcElement || oEvent.target;
    if (oS.tagName == 'LI') {
        oS.parentNode.style.background = 'green'; // 点击子级通过dom查找操作父级
    }
};
```

或者。。。用它来做一个扫雷游戏？

感觉js的事件委托机制能玩出花。