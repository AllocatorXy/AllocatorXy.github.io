---
layout:     post
title:      "我的JS笔记"
subtitle:   "我自己的js文档"
date:       2017-02-08 15:00:00
author:     "AllocatorXy"
header-img: "img/js-logo.png"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - JavaScript
    - noteBook
---

## javaScript

### string

#### string换行
- string不能直接换行，换行需要搞事情：
    + 加号拼接
    + 每次换行行末加\
    + ES6 模板字符串

#### 正则表达式
实际就是词法分析器匹配字符。

##### 常用匹配
```js
/\d/ // 数字
/\w/ // 字母/数字/下划线
/\b/ // 单词边界
/\s/ // 空白

/a*/ // 闭包，0-n个a
/a+/ // 非零闭包，1-n个a
/a?/ // 0或1个a
/a{n}/   // n个a
/a{n,m}/ // n-m个a
/a{n,}/  // 最少n个

/a|b/      // a或b
/a[qwe]c/  // aqc/awc/aec
/[a-z]/    // a,b,c,...z
/[a-zA-Z]/ // a-z或A-Z
/[12-49]/  // 1或2-4或9 (1, 2, 3, 4, 9)
/a[^ab]c/  // a后字母非a或b,然后c
```

##### 参数
```js
// 这里面i和g就是参数，用来调整匹配选项
// 常用的: i(忽略大小写ignore) g(全局global) m(多行multi-line) u(unicode)
var exp = new RegExp('A', 'ig');
const exp1 = new RegExp(/\d/ig, 'i') // es5报错 es6中ig会被i覆盖
```

##### 正则常用方法
```js
str.search(expr|str); // 找到了返回位置，找不到返回-1，类似indexOf
str.match(expr|str); // 找到了返回结果数组，找不到返回null
str.split(expr|str); // 切数组

str.replace(expr|str, str|fn);
str.replace(expr|str, function(s) {
        // s: 每次匹配到的东西
        // return 要替换成xx
    });

str.startsWith('abc'); // 用正则代替: /^abc/
str.endWith('abc');    // 用正则代替: /abc$/

reg.test(str) // 需要在reg前后加^和$才能用，测试能不能匹配到，返回Boolean
```

#### string操作

```javascript
let str = 'abc';
/* 按字符串下标获取字符串 */
str[0];                    // 'a', 不兼容ie6,7
str.charAt(1)              // 'b'
let str='abcdefg';
let s=str.substring(0, 3); // 'abc', 从下标n开始，获取m个
/* 找到字符第一次在string中出现的下标 */
str.indexOf('c') // 2
str.indexOf('z') // -1
/* 从后往前找寻字符第一次在串中的下标 */
let str2 = 'aaa';
str2.lastIndexOf('a') // 2
/* 以某个符号拆分成数组 */
let str='1,2,3,4,5,6';
str.split(',');       // [1,2,3,4,5,6], 可以用正则匹配

obj.toString(); // 强制转换为字符串
```


##### userAgent
>获取浏览器UA会得到一大坨东西，用来识别浏览器不好使，这时候就要用到indexOf了

```javascript
window.navigator.userAgent.indexOf('MSIE 7.0')
```

#### 模板字符串
```javascript
let a = `string1`;     // 使用``代替''包裹字符串
let b = `string2`;
let c = `${a}and${b}`; // string1andstring2
```
<hr />

### 基本数据类型
    1. string
    2. number       // NaN属于number
    3. boolean
    4. undefined    // undefined == null => true
    5. function     // 官方文档里没有定义function为数据类型，但typeof可以弹出
    6. object       // 可分为元素,array,null
    7. global       // es6顶层对象

#### 数据真假(true/false)
```javascript
    false: false, 0, '', null, undefined, NaN;
    true: true, 非空对象, 非0数字, 非空字符串;
```

#### 检测数据类型
- isNaN() -> is **Not** a Number?true:false
    + NaN == NaN -> **false**
- typeof sth -> type of sth

#### 类型转换

##### 显式转换

###### 转换函数
**转换函数是将字符串中第一个有效数字转换为数字**

```javascript
// string转换成整数,不一定是十进制,parseInt支持基方法,可指定基数来转换其他数字为十进制
parseInt('string')   
parseInt('0xA')          // result: 10
parseInt('0010')         // result: 10
parseInt('FF', 16)       // result: 255

// string转换成浮点数，不支持基方法
parseFloat('string')    
parseFloat('2.2.5')      // result: 2.2
parseFloat('0100')       // result: 100
```

###### 强制类型转换
```javascript
Boolean(value)      // '',0,null,undefined -> false
Number(value)       // Number与转换函数不同在于，非数字将直接转换为NaN
String(value)
```

##### 隐式转换
```javascript
var x = '2'*2;    // result: 4
var y = '2'+2;    // result: '22' *加号就当连字符处理了*
/* 利用隐式转换可以便捷转换 */
var z = 2+'';     // result: '2'
var n = '2'*1;    // result: 2
var n = '2b'++;    // result: NaN
```
<hr />

### 流程控制语句

#### 循环

##### 遍历(Traversal)
有3种常用的遍历方法。

```javascript
for (let i = 0; i < Things.length; i++) { // 这里i不能用const
    // statement..
}
/* for in循环中，不管是遍历什么，prop类型都是string */
for (const prop in obj) { // 这里的变量prop可以用const
    // statement..
}
while (condition) { // while是自定义循环条件，既是循环也是判断
    // statement..
}
```

##### break
```javascript
// 停止执行 result:0 1 2
for (let i = 0; i <= 5; i++) {
    if (i==3) {
        break;
    }
    alert(i);
}
```

##### continue
```javascript
// 跳过本次执行 result:0 1 2 4 5
for (let i = 0; i <= 5; i++) {
    if (i==3) {
        continue;
    }
    alert(i);
}
```

##### 9*9 乘法表
```javascript
for (let i = 1; i <= 9; i++) { // 外层循环被乘数1-9
    for (let j = 1; j <= i; j++) { // 内层循环乘数，不大于被乘数
        document.write(i + '*' + j + '=' + i * j + ' ');
    }
    document.write('<br />'); // 每行末位换行
}
```

#### 判断

##### 三目运算符
**三目运算符可用于缩写判断语句**

```javascript
if(condition){...} else {...} => condition ? statement1 : statement2;

if(true){...} => condition && statement1; 

if(false){...} => condition || statement1;
// 用'||'的情况下,第一个条件true,不检测第二个直接返回true;
// 第一个条件false，会执行第二个条件检测。
```
<hr />

### 作用域
在函数内部声明变量，则变量是该函数的`局部变量`，在外部声明变量，则变量是`全局变量`；<br />
局部变量的作用域就是`该函数内部`，全局变量的作用域就是全局；<br />
**!!若在函数内部不用指令声明变量，则实际声明了一个`全局变量`;!!**

```javascript
function fn() {
    i = 1;  // globally scoped
}
window.onload = function () {
    fn();
    alert(i);   // 1
};
```
<hr />

### 日期对象Date()
- 当日期对象声明时，会获取到当前时间，并且**不会**自动改变，所以这里用`const`比较好;
- 注意月份在计算机中存储是`0-11`，所以设置和获取的时候都要做处理;
- 时间对象会自动**进位**，例如当月份为12时，年份+1;

```javascript
const oDate = new Date();
oDate.setFullYear(2018, 1, 14);       // 设置oDate为2018年2月14日
oDate.setHours(h, m, s, ms);          // 设置oDate的时分秒和毫秒
const oHours = oDate.getHours();      // 获取oDate的小时数
const oMin = oDate.getMinutes();      // 获取oDate的分钟数
const oSeconds = oDate.getSeconds();  // 获取oDate的秒数
const oMonth = oDate.getMonth() + 1;  // 获取月份为2月
const oMonth = oDate.getDay();        // 星期n, 0-6, 0是星期天
const oTime = oDate.getTime();        // 获取时间戳,1970.1.1 00:00至当前时间对象的毫秒数
```
<hr />

### 数组(Array)

#### 声明
```javascript
let arr=[ 1, 2, 3 ];
let arr=new Array(1, 2, 3, 4);
arr = Array.of(3,2,1);
let arr=new Array(3);           // 长度为3的空数组
```

#### 数组操作

##### 转换为字符串
```javascript
let arr = [ 1, 'a', 2, 'b' ];
let arr2 = [ 1, 'a', 2, 'b' ];
arr.join('');                 // arr: '1a2b'
arr2.join('&');               // arr: '1&a&2&b'
```

##### 添加与删除
```javascript
let arr = [ 1, 2, 3 ];
arr.push(5);             // arr: [ 1, 2, 3, 5 ] 从后面插入
arr.unshift(0);          // arr: [ 0, 1, 2, 3, 5 ] 从前面插入
arr.pop();               // arr: [ 0, 1, 2, 3 ] 从后面删除
arr.shift();             // arr: [ 1, 2, 3 ] 从前面删除
```

##### 万能操作splice()
>arr.splice(m, n, ...)

- 从下标m开始，删除n个；
- 若n大于剩余长度，也可以执行；
- 若后面有元素则插入后面的元素；

```javascript
let arr1 = [ 1, 2 ]
arr.splice(1, 2, 'a', 'b', 'c');    // arr: [1, a, b, c]
```

##### 排序和连接
```javascript
let arr = [ 1, 2, 3 ];
let arr1 = [ 4, 5, 6 ];
let arr2 = [ 7, 8, 9 ];
let arr3 = [ 31, 22, 15, 47, 'aa', 'bz', 'aa', 22 ];
alert(arr.concat(arr1,arr2));    // [1,2,3,4,5,6,7,8,9] 数组连接
alert(arr.reverse());            // [3,2,1] 数组倒序排列
alert(arr3.sort());              // [15,22,22,31,47,'aa','aa','bz'] 数组默认排序
alert(arr3.sort(function(a, b) { // 从小到大，return b-a是从大到小
        return a - b;
    }));
```

##### 清空数组
```javascript
let arr = [ 1, 2, 3, 4, 5 ];
arr.length = 0;     // function 1
arr.splice(0, 5);   // param1: index, param2: amount
while(arr.length > 0){
    arr.pop();
}
```
<hr />

### json
>json是一个对象，可以装任何东西，可以**嵌套**，json没有长度属性；<br />
>json由键值对组成，每个数据称为**键(key)**，拥有一个相对应的**键值(value)**；<br />
>键名类似于数组的下标，但数据类型是字符串，键值可以是任何形式；<br />
>es6中，最后一组键值对可以加`,`;

取得json的键值有两种方式：

```javascript
let json = { a:1, 'b':2, c:3 };
alert(json.a);      // 1
alert(json.b);      // 2
alert(json['a']);   // 1
alert(json[a]);     // error: not defined => 被当成数组了
```

##### json遍历
因为json**没有长度属性**，所以通常的for循环是不能遍历的，这里常用for in方法

```javascript
const json = { a:1, 'b':2, c:3 };
for (const prop in json) {          // for key in obj => key is string
    console.log(prop + json[prop]);
    alert(typeof prop);             // string
}
// console: a1
// console: b2
// console: c3
```

##### json嵌套
```javascript
const json = {
    name: '小明',
    job: '前端攻城狮',
    addr: '杭州',
    age: 20,
    family: [
        { name: '小花', age: 25, job: 'java' },
        { name: '韩梅梅',
          age: 28,
          job: 'php',
          family: [
             { name: '小花', age: 25, job: 'java' },
             { name: '韩梅梅', age: 28, job: 'php' }
          ]
        }
    ]
};
```

##### eval解析json
```javascript
const json='{ a:1, b:2 }';
const json1=eval('(' + json + ')');
/* eval解析字符串json时需要加括号，强制转换为对象 */
```

##### fn解析json
```javascript
const json='{ a:1, b:2 }';
const fn=new Function('return' + json);
const json1 = fn(); // 解析的json
```

##### JSON转换
```js
JSON.stringify('{"a":1,"b":2}'); // object to stirng
JSON.parse({"a":1,"b":2});       // string to object
/*
 * JSON.parse() 解析的对象最后一个不能加逗号
 */
```
<hr />

### Math(本地对象)
```javascript
Math.random();   // 获取0-1之间的随机小数不包括1
Math.ceil();     // 向上取整
Math.floor();    // 向下取整
Math.round();    // 四舍五入
Math.abs();      // 绝对值
Math.sqrt();     // 开方
Math.pow();      // n次方(幂)
Math.max();      // 求最大值
Math.min();      // 求最小值
```
<hr />

### 严格模式
'use strict'会产生一个script作用域，范围是当前js标签或文件内;

- 解决了局部函数中this是window的bug;
- 不用声明语句无法声明变量;
- 不允许在非函数的代码块内声明函数;
- 去除with;

**ie9不兼容严格模式**
<hr />

### javaScript的组成
- ECMAScript 核心解释器(ES5和ES6);
- DOM 文档对象模型(Document Object Model) <= document对象;
- BOM 浏览器对象模型(Browser Object Model) <= window对象;
<hr />

### DOM

#### DOM节点类型
- DOM中有两种节点类型，一种是元素节点ElementNode，即html标签；
- 还有一种TextNode，文本节点指文档中的文本，每个标签前后都有两个文本节点；
- `nodeType = 1` (element节点);`nodeType = 3` (text节点);
- **文本节点空着也是存在的**;

#### DOM节点获取

##### 子节点获取
在ie低版本中，`obj.childNodes`可直接获取对象的子元素节点;<br />
但在其他情况下，此操作会获取对象的**<font color="red">全部</font>**子节点(文本节点和元素节点);

若要获取元素子节点，有3种常用方法:
- 用nodeType判断。nodeType = 1 (element节点);nodeType = 3 (text节点);
- 用`obj.children`,这种方法只会选择元素节点(只会选择子代不会选择子代的后代),`obj.children`获取的节点是一个元素集合，`obj.children[0]` <= 可以这样操作;
- 用Element系列指令操作:
    + `obj.firstElementChild`;
    + `obj.lastElementChild`;
    + `obj.nextElementChild`;
    + `obj.previousElementChild`;

##### 兄弟节点获取
- `obj.firstElementSibling`;
- `obj.lastElementSibling`;
- `obj.nextElementSibling`;
- `obj.previousElementSibling`;

>兄弟节点和子节点Element语句有**<font color="red">兼容性问题</font>**，以子节点为例，应该这样写：
>`obj.firstElementChild || obj.firstChild`

##### 父节点获取
`obj.parentNode`

##### 元素节点tag名获取
获取node后可以用node获取tag名：
```js
const obj = document.getElementById('id');
obj.tagName = 'LI'; // UpperCase
```

##### offset
- `obj.offsetParent` 用来获取定位的参考父级元素;
- `obj.offsetLeft` 获取相对于定位父级的left;
- `obj.offsetHeight` 物体盒子模型高度;
- `obj.scrollHeight` 内容高度;
    + **当内容高度小于盒子，取盒子高度，否则取内容高度**;

##### 获取物体绝对位置
```js
obj.getBoundingClientRect(); // 获取绝对位置;
                        // .left 左边距离页面左边的距离
                        // .top  上边距离页面上边的距离
                        // .right  右边距离页面左边的距离
                        // .bottom  下边距离页面上边的距离
```

#### DOM节点操作

##### 插入新元素
首先，<br />
`var childNode = document.createElement(tagName)`<br />
此方法创建的元素会存储在内存中，并不显示在页面内。

若要使创建的节点置于html中，需要用到`parentNode.appendChild(childNode)`将创建的节点置入指定父节点内。**`appendChild`方法会将结点插在父级末尾。**

**值得一提的是，`append`方法会将`childNode`剪切进新的节点尾部，而不是复制，利用这一点可以快速移动节点。**

若要将节点插入父节点指定部位，需要用到`insertBefore(childNode, existingNode)`,**这个方法会将`childNode`插入到existingNode之前。**

##### 删除元素
`parentNode.removeChild(childNode)`

##### 复制元素
- `obj.cloneNode()` <= 返回复制的元素;
- `obj.cloneNode(true)` <= 返回复制的元素包括该元素的子元素;

#####文档碎片
**需要注意的是，在高版本的浏览器中，文档碎片是鸡肋**

当遇到诸如，需要创建很多li时，若用循环一个个插入会造成性能低下，就像需要搬运很多小东西，却只一次搬运一件一样。<br />
这时候，我们就可以用到文档碎片(DocumentFragment)了，它的作用相当于一个大的袋子，我们可以先把小东西都放进去，然后一次拎走，这样算法就优化很多了。

- 首先我们需要创建文档碎片 `var oFrag = document.createDocumentFragment();`
- 然后我们可以用**循环**将需要插入的元素放入碎片中，这里以正序插入为例：`oFrag.appendChild(ChildNode);`
- 最后我们将碎片整体插入需要插入的节点内部`parentNode.appendChild(oFrag);`
<hr />

##### 操作属性
- 获取: `obj.getAttribute('attrName')`; 
- 设置: `obj.setAttribute('attrName')`; 
- 删除: `obj.removeAttribute('attrName')`; 

##### innerHTML bug
```html
<body>
    <div>
        <h2>11111111</h2>
    </div>
    <input type="button" value="press" style="position: absolute; left: 50px; top: 50px;" />
</body>
<script type="text/javascript">
    window.onload = function() {
        const oDiv = document.querySelector('div');
        document.querySelector('h2').onclick = function() {
            alert('msg');
        };
        document.querySelector('input').onclick = function() {
            oDiv.innerHTML += '<h3>222222</h3>';
            // 对oDiv.innerHTML重新赋值会导致加在oDiv.innerHTML上的事件失效
        };
    };
</script>
```
<hr />

### 批量修改css样式
在不封装任何方法的情况下，js本就是可以对css样式批量修改的，但都不好使，**仅作了解**。

```js
with(obj.style){ // 性能低下
    styleName = value;
    styleName = value;
}

obj.style.cssText='width:200px;height:200px;'; // 会将当前行间样式清除后再添加
```
<hr />

### 异常捕获
```js
    try{
        // code try running
    }catch(e){
        // if error
        // e: error msg
    }
```
<hr />

### bom属性兼容
```js
const scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
const clientWidth = document.documentElement.clientWidth || document.body.clientWidth;
// etc..
```
<hr />

### Event

##### 组合键
js中是不能用键码控制组合键的，这时候用到一些特别的键码，若按下返回true：

- `oEvent.ctrlKey`;
- `oEvent.shiftKey`;
- `oEvent.altKey`;
<hr />

##### 阻止默认事件
```js
/* usually */
return false; // 同时会阻止冒泡

/* ie */
// 鼠标事件捕获
obj.setCapture && obj.setCapture(); // 捕获当前鼠标事件
// 释放捕获
obj.setCapture && obj.setCapture();

/* 兼容 */
function stopDef(e){
    const oE = e || event;
    oE.preventDefault ?
    oE.preventDefault() :
    window.event.returnValue = false;
}
```
<hr />

##### 事件冒泡
文档流中，触发子级事件会逐级依次触发父级事件，称为**事件冒泡**;<br />
在某些情况下我们需要取消事件冒泡：

```js
oEvent.cancelBubble = true; // ie, 但可兼容其他浏览器
oEvent.stopPropagation();   // chrome etc..
```
<hr />

#### 绑定与委托

##### 事件绑定(监听)
我们有时会遇到这样的情况：同一个对象需要有**多个事件**，或多个文件中有多个事件需要**文档加载完成**后执行的代码，那么我们就不能再对对象加单个事件了，事件连等也不能完全解决我们的需要，这时候我们就需要**事件绑定**了。

- 非ie：`addEventListener`:

```js
obj.addEventListener('event', function() {
        // statement..
    }, useCapture); // Boolean; true:使用事件捕获，反转事件流; false:冒泡;
                    // true的优先级大于false,同时出现true,父级优先;
```

- 兼容ie:

```js
obj.attachEvent('on' + 'event');
```

- 兼容写法：
```js
Object.prototype.adEv = function(ev, fn) {
    if (this.addEventListener) {
        this.addEventListener(ev, fn, false);
    } else {
        this.attachEvent('on' + ev, fn);
    }
};
```

##### 解除绑定
解除绑定(**<font color="red">无法解除绑定的匿名函数</font>**), 因为匿名函数实际是每次都用`constructor`生成了新函数;

```js
Object.prototype.rmEv = function(ev, fn) { // 必须指定函数名
    if (this.removeEventListener) {
        this.removeEventListener(ev, fn, false);
    } else {
        this.detachEvent('on' + ev, fn);
    }
};
```

##### 事件源
事件源指**<font color="red">第一个</font>**触发事件的对象，可以通过事件对象获取：

```js
function a(e) {
    const oE = e || event;
    const target = oE.srcElement || oE.target;
    alert(target.tagName); // 这里获取的标签名是大写，如果用标签名匹配记得大写
}
```

##### 事件委托
事件冒泡会触发父级元素的事件，所以通过在父级绑定事件获取事件源，再对事件源进行操作，可以**间接实现**对子级的事件绑定，这叫做**事件委托**
>子元素的事件可以委托给**自身的父级元素**，在点击子级时会触发绑定给父级的事件;<br />
另外，**无论子级层级高低都可以点透到子级**;

1. 提高性能(减少迭代);
2. 可以给未来的元素加事件;

```js
oUl.onclick = function(ev) { // 添加点击事件给父级
    var oEvent = ev || event;
    var oS = oEvent.srcElement || oEvent.target; //  获取点击的事件源(父级及所有子代)
    if (oS.tagName == 'LI') { // 用某种方法匹配到需要元素
        oS.style.background = 'red';
    }
};
```
<hr />

### 转码
```js
/* 不含特殊字符 */
encodeURI(str);
decodeURI(str);
/* 含特殊字符 */
encodeURIComponent(str);
decodeURIComponent(str);
```
<hr />

### 固定this指针
```js
// 调用fn, 使this指向obj, 一般用作固定this到某个函数
fn.call(obj,arg1,arg2...);
fn.apply(obj,[arg1,arg2...]);

// 使用箭头函数，this指向父级
function aaa() {
    () => {
        console.log(this); // function aaa
    }
}
```
<hr />

### 多态
```js
/*多态的基本概念：一个引用类型（变量）在不同情况下的多种状态。
     js本身是无态的，天生就支持多态。*/
//Master类
function Master(name) {
    this.name = name;
    //方法[给动物喂食物]
}
//原型法添加成员函数
Master.prototype.feed = function(animal, food) {
    window.alert('给' + animal.name + '喂' + food.name);
};
    //动物类
function Animal(name) {
    this.name = name;
}
//猫猫
function Cat(name) {
    this.animal = Animal;
    this.animal(name);
}
//狗狗
function Dog(name) {
    this.animal = Animal;
    this.animal(name);
}
//食物类
function Food(name) {
    this.name = name;
}
//鱼
function Fish(name) {
    this.food = Food;
    this.food(name);
}
//骨头
function Bone(name) {
    this.food = Food;
    this.food(name);
}
var cat = new Cat('大花猫');
var fish = new Fish('黄花鱼');
var dog = new Dog('大花狗');
var bone = new Bone('猪骨头');
//创建一个主人
var master = new Master();
master.feed(cat, fish);
master.feed(dog, bone);
```
<hr />

### 本地存储

#### cookie
- cookie一般存在网站根目录，因为**父级目录无法读取子级目录的cookie**;
- Expires/Max - Age: cookie有效期，默认值为session;
- cookie**不能存中文**，如果要存中文转成utf8;
- 数据类型均为string, 用JSON.parse()需要将JSON键加**双引号**;
- **删除cookie**时，将有效期调成负值;
- cookie最大容量为4kb;

```js
/**
 * date: 2017-03-21 15:49:34
 * author: AllocatorXy
 * description: 原生cookie方法
 */
function setCookie(name, value, json) {
    let str = name + '=' + value;
    if (json) {
        for (const k in json) {
            switch (k) {
                case 'expires': {
                    if (Number(json[k])) {
                        const oDate = new Date();
                        oDate.setDate(oDate.getUTCDate() + json[k]);
                        str += ';expires=' + oDate.toUTCString();
                    } else {
                        str += ';expires=' + json[k];
                    }
                    break;
                }
                default:
                    str += `;${k}=${json[k]}`;
                    break;
            }
        }
    }
    document.cookie = str;
}

function getCookie(name) {
    const arr = document.cookie.split('; ');
    for (let i = 0; i < arr.length; i++) {
        const arr2 = arr[i].split('=');
        if (name == arr2[0]) {
            return arr2[1];
        }
    }
    return ''; // 不存在的情况
}

function getCookieAll() {
    const arr = document.cookie.split('; ');
    const res = {};
    for (let i = 0; i < arr.length; i++) {
        const arr2 = arr[i].split('=');
        res[`${arr2[0]}`] = arr2[1];
    }
    return res;
}

function removeCookie(name) {
    setCookie(name, '', -2);
}
```

#### storage
h5提供了一个新的本地存储解决方案: localStorage和sessionStorage;

- 最大容量为5M;
- 数据类型依然是字符串;
- 不能被爬虫抓取;
- 存储东西太多会影响网页性能;
- 以key和value键值对组成;

```js
/* localStorage和sessionStorage用法完全一样，只是有效期不一样 */
const ls = window.localStorage;
/* 设定一个值 */
ls.a = 'abc';
ls['b'] = '1234';
ls.setItem('c',89);

/* 获取一个值 */
alert(ls.a);
alert(ls['b']);
alert(ls.getItem('c'));

/* 删除 */
ls.removeItem('c'); // 删除一个
ls.clear();         // 清空

/* key */
ls.key(i);             // 按索引取出某个键
ls.getItem(ls.key(i)); // 按索引取出某个键的值
```

##### onstorage
这个事件监控storage改变，当它改变时触发；
<hr />

### 自定义属性
正常情况下除了低版本ie, 不能获取直接添加在标签中的自定义属性，有一些特殊方法；

##### get/setAttribute
```js
obj.getAttribute('abc') // 获取obj的abc属性，返回的数据类型是string
obj.setAttribute('abc', 111) // 为obj设置abc属性
var a = obj.attributes; // 返回obj的属性集合NamedNodeMap
```

##### dataset
h5中新增了一种自定义属性对象，它在h5页面中这样呈现：

```html
<div data-aaa="111"></div>
```

`dataset`不是传统的js对象，而是DOMStringMap对象，在js中这样操作：

```js
const a = obj.dataset; // 获取obj的所有以'data-'为前缀的属性
const b = obj.dataset.aaa; // 获取obj的'data-aaa'属性
const c = obj.dataset.aaBb // 获取obj的'data-aa-bb'属性，类似于angularJS
obj.dataset.aaBbCc = 1; // 设置obj的'aa-bb-cc'属性为1
```

**需要注意的是，dataset的性能远低于attribute, 慎用!**

##### 遍历属性
元素的属性集合是可以被遍历的，但要尽量避免直接遍历：

```js
for (let k in obj) {
    console.log(k); // obj的所有属性全都被打印出来了，很恶心
}
```

这时候用dataset就很舒服：

```js
for (let v in obj.dataset) {
    console.log(v + '=' + obj.dataset[v]); // 这样只会打印'data-*'属性
}
```

##### classList
h5中提供了一种新的操作class的方法：

```js
let c = obj.classList; // 返回DOMTokenList, 即obj的class集合
let d = obj.classList.value; // 标签的class值 形如：'class1 class2 class3'
```

另外针对classList还有几种新的方法：

```js
obj.classList.add('active', 'a'); // 为obj添加类
obj.classList.remove('active');   // 为obj移除类
obj.classList.contains('active'); // 返回Boolean, 用于检测是否有某个类名
obj.classList.item(n); // 返回索引值为n的类名，若参数错误则返回第一个类名，但参数不能为空

/* 类似于jq的toggleClass, 若不存在类名返回true并为之添加类, 第二个参数可以不填 */
obj.classList.toggle('active', true|false); 
```
<hr />

### 文件操作

##### 文件拖拽
```js
oBox.ondragenter // 拖动文件进入oBox
oBox.ondragleave // 拖动文件离开oBox
oBox.ondragover  // 拖动文件悬停在oBox
oBox.ondrop      // 在oBox释放拖动的文件
////////////////////////////////////////////////
// oBox中drop事件触发必须阻止oBox中的dragover事件 //
////////////////////////////////////////////////
```

##### 文件获取/读取
**文件读取只能读取拖拽、文件上传input[type=file]获取到的文件;**

```html
<!-- 读取多个文件 -->
<input type="file" multiple>
```

```js
oBox.ondrop = ev => {
    // 拿到文件
    const oFile = ev.dataTransfer.files[0];
    // 读取文件基本信息
    // alert(oFile.name);
    // alert(oFile.size);
    // alert(oFile.type);
    // alert(oFile.lastModifiedDate);
    const reader = new FileReader();
    // reader.readAsText(oFile); // 以文本方式读取
    reader.readAsDataURL(oFile); // 以base64方式读取

    reader.onload = () => { //文件读取完毕后
        alert(reader.result); //文件读取结果
    };

    return false; // 这里不组织默认事件会被浏览器打开文件
};
```

