---
layout:     post
title:      "js数组去除重复元素"
subtitle:   "js数组去重的三种常用方式"
date:       2017-02-09 21:00:00
author:     "AllocatorXy"
comments:   true
header-img: "img/js-bg.png"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
---

### js数组去重
**去除数组中重复的元素，用js实现一般来说有三种较常用的方式。**

#### function 1
**时间复杂度`O(n^2)`**

- 创建一个结果数组；
- 遍历数组，每次从原始数组取出一个元素与结果数组对比；
- 若结果数组中无该元素，将该元素存入结果数组；

```javascript
Array.prototype.uniq = function() {
    let res = [ this[0] ];
    for (let i = 1; i < this.length; i++) { // 每次从原数组取一个
        let matched = false;
        for (let j = 0; j < res.length; j++) { // 将这个元素与res中每个元素对比
            if (this[i] == res[j]) { // 若匹配成功，打断第i次的内部循环
                matched = true;
                break;
            }
        }
        /* 注意这里逻辑，不能用else，否则将多将很多元素放入res */
        if (!matched) { // 若匹配不成功，将该元素放入res
            res.push(this[i]);
        }
    }
    return res;
};
```
<hr />

#### function 2
**这种方法效率会比function 1 高，时间复杂度为`O(n)`，但会影响原数组顺序**

- 原数组排序，使相同元素处于相邻位置；
- 创建一个结果数组；
- 因为相同元素相邻，只需要将原数组每个元素与结果数组中上一个元素比较；
- 将不同于上一个元素的元素放入结果数组；

```javascript
Array.prototype.uniq = function() {
    this.sort();
    let res = [ this[0] ];
    for (let i = 1; i < this.length; i++) {
        if (this[i] != res[res.length - 1]) {
            res.push(this[i]);
        }
    }
    return res;
};
```
<hr />

#### function 3
**这种方法时间复杂度与第二种相同，但因为少了一次快速排序，效率最高，且不影响数组顺序**

- 创建一个结果数组和一个json对象；
- 遍历原数组，每次查找其在json中是否有值；
- 将不存在的放入结果数组，并在json中以该元素创建一个键并随意赋值；

```javascript
Array.prototype.uniq = function() {
    const res = [];
    const json = {};
    for (let i = 0; i < this.length; i++) {
        if (!json[this[i]]) { // 若不存在该属性，将其放入res并建立该属性到json
            res.push(this[i]);
            json[this[i]] = 1;
        }
    }
    return res;
};
```
