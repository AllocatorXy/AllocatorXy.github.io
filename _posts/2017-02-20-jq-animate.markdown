---
layout:     post
title:      "jq动画使用"
subtitle:   "如何用animate做出好使的hover标签"
date:       2017-02-20 20:24:00
author:     "AllocatorXy"
comments:   true
header-img: "img/post-bg-jq.jpg"
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
<hr />

#### 用stop()终止动画
如果用户**疯狂**触发新的动画，会造成在当前队列中加入了很多动画，当用户停止触发时，动画一个个执行完毕才会停下来，这造成了很**糟糕**的用户体验，所以我们在用animate时，要根据情况先终止动画：

```javascript
$(selector).stop(stopAll, goToEnd)
// stopAll: 可选, boolean, 是否停止被选元素所有队列中的动画，默认false
// goToEnd: 可选, boolean, 是否立即完成当前动画，默认false
```

在制作类似这种hover出现的标签时，如果我们在移入和移出时将动画全部停止，会解决根本停不下来的问题；<br />
但随之而来我们会发现另一个问题：**疯狂晃鼠标它不会出来了**，就像底下这个<font style="background-color: #0094FF; border-radius: 20%; color: white;">小蓝块</font>一样，因为我们在疯狂触发它的动画时，**每次**都会终止另一个动画，而初始状态是不显示的，所以它会根本不出来；

那只在鼠标移入时，**终止让它消失的动画**不就好了？<br />
然而这样发现，如果这样，会又出现让它停不下来的问题，这时候我们就需要stop方法中的参数了：`stop(true)`,下面<font style="background-color: #094; border-radius: 20%; color: white;">绿色</font>这个就是只在移入时加入`stop(true)`的结果：

如果这样还是觉得不满意呢，我们可以用到第二个参数，只在移入时加入`stop(true, true)`就是这个<font style="background-color: #666; border-radius: 20%; color: white;">小灰块</font>的效果了：

<iframe src="https://allocatorxy.github.io/anitags/" frameborder="0" scrolling="no" style="overflow: hidden; width: 480px; height: 200px; margin-left: -240px; position: relative; left: 50%;"></iframe>