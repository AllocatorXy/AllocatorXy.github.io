---
layout:     post
title:      "jq动画使用"
subtitle:   "animate使用时的注意事项"
date:       2017-02-12 20:24:00
author:     "AllocatorXy"
comments:   true
header-img: "img/post-bg-js-module.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
    - jQuery
---

### jQuery animate
> jQuery中，fadein/fadetoggle等内部实现也是animate;<br />
> animate实际就是js实现定时器动画;

#### 格式
```javascript
/* 简易格式 */
// easing默认为swing，自带的只有swing和linear
$('selector').animate({param1: value1,param2: value2}, speed, easing, callback);
// value可以指定单位，但要用引号
$('selector').animate({width: 100,height: '100%',fontSize:'2em'});

/* 高级格式，参数为两组json */
$('selector').animate(styles, options);
```

#### 用stop()终止动画
```javascript
/* 终止当前动画后再开始新动画以解决用户快速多次触发后根本停不下来的问题 */

// stop(stopAll, goToEnd)
// stopAll: 是否停止被选元素的所有加入队列的动画
// goToEnd: 是否允许完成当前的动画
$('selector').stop().animate(...) // 停止元素当前动画并继续下个动画

$('selector').stop(true).animate(...) // 停止元素的所有动画

$('selector').stop(false, true).animate(...) // 当前动画直接到达末状态并继续下个动画

$('selector').stop(true, true).animate(...) // 停止元素所有动画并使当前动画到达末状态
```
