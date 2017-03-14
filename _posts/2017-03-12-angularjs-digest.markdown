---
layout:     post
title:      "AngularJS中双向数据绑定的实现"
subtitle:   "angular源码解析: $apply, $watch, $digest"
date:       2017-03-12 04:08:00
author:     "AllocatorXy"
header-img: "img/bg-angular.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - AngularJS
---


>AngularJS的核心特色是双向数据绑定给我们的MV*开发带来了很大方便，特别是前台数据逻辑，那么双向数据绑定是如何实现的呢？<br />
>看了看源码，发现angular源码写的还是比较干净的。

### 脏检查(Dirty Checking)
angular中有一个`$digest`方法，它的作用就是检查数据改变，如果数据被改变，则**「消化」**这些已被改变的**「脏数据」**；<br />
这个过程被称为**<font color="red">脏检查(Dirty Checking)</font>**
<hr />

#### `$digest`是如何检测到数据改变的呢？
每当有一个变量**用angular进行数据绑定**，angular会**自动**用`$watch`方法为其添加一个`watcher`, 这个`watcher`将变量的当前值，回调方法`listener`等参数作为属性保存。<br />
当执行`$digest`方法，它会遍历范围内的watchers, 每当某个`watcher`被`$digest`检测出数据有改变时，执行`watcher`的回调方法`listener`;

```js
{
  /**
   * method $watch
   * @param  {exp}     watchExp              // 要监测的变量
   * @param  {fn}      listener              // 改变时的回调函数
   * @param  {boolean} objectEquality        // 比较变量的方式
   * @param  {exp}     prettyPrintExpression // trim后的表达式
   * @return {fn}      deregisterWatch       // 用于销毁watcher的闭包
   */
  $watch: function(watchExp, listener, objectEquality, prettyPrintExpression) {
    var get = $parse(watchExp); // 将表达式转换为域内函数用来获取当前值

    if (get.$$watchDelegate) {
      return get.$$watchDelegate(this, listener, objectEquality, get, watchExp);
    }
    var scope = this,
        array = scope.$$watchers,
        watcher = { // 按照参数定义watcher
          fn: listener,                           // 数据改动时的回调函数
          last: initWatchVal,                     // 上次的记录值
          get: get,                               // 当前值
          exp: prettyPrintExpression || watchExp, // trimmed exp(angular会自动trim表达式)
          eq: !!objectEquality                    // 指定比较方式(默认为按引用比较)
        };

    lastDirtyWatch = null;

    if (!isFunction(listener)) { // 回调函数有误
      watcher.fn = noop;
    }

    if (!array) { // 当没有watcher时，避免$digest遍历该scope
      array = scope.$$watchers = [];
      array.$$digestWatchIndex = -1;
    }
    // we use unshift since we use a while loop in $digest for speed.
    // the while loop reads in reverse order.
    array.unshift(watcher); // 将定义好的watcher放入监视器数组$$watchers
    array.$$digestWatchIndex++;
    incrementWatchersCount(this, 1);

    return function deregisterWatch() { // 返回闭包，用于销毁watcher
      var index = arrayRemove(array, watcher);
      if (index >= 0) {
        incrementWatchersCount(scope, -1);
        if (index < array.$$digestWatchIndex) {
          array.$$digestWatchIndex--;
        }
      }
      lastDirtyWatch = null;
    };
  },
}
```

显然，**脏检查只检查有`watcher`的对象**，每当有数据被绑定，它会在初始化时被添加一个对应的`watcher`;<br />
**除此之外**，我们也可以手动调用`$watch`方法，为某个变量添加`watcher`, 让它处于脏检查队列中;

```js
/**
 * 这是源码注释中的栗子，手动添加watcher
 * @param  {exp} watchExp        // 要监控的变量
 * @param  {fn}  listener        // 回调函数
 * @return {fn}  deregisterWatch // 用于销毁watcher的闭包
 */
scope.$watch(
  // This function returns the value being watched. It is called for each turn of the $digest loop
  function() { return food; },
  // This is the change listener, called when the value returned from the above function changes
  function(newValue, oldValue) {
    if ( newValue !== oldValue ) {
      // Only increment the counter if the value changed
      scope.foodCounter = scope.foodCounter + 1;
    }
  }
);
```
<hr />

#### 何时触发`$digest`？
当使用AngularJS某些内置语句(`ng-model`, `ng-click`, ...)修改model时，`$digest`会被**自动调用**，但有些时候，我们需要**手动调用**它；<br />
**例如**，当我们在使用一些**<font color="red">非AngularJS内置语句</font>**时，`$digest`并不会被触发:

```html
<!-- 用jq的ajax获取数据生成dom -->
<!-- ajax可以成功获取数据，$scope.arr也被成功赋值，但view没有更新 -->
<body ng-app="mod1" ng-controller="ctr1">
    <ul>
        <li ng-repeat="item in arr">{{item}}</li>
    </ul>
</body>
<script>
    angular.module('mod1', []).controller('ctr1', ($scope) => {
        $.ajax({
            url: 'data/a.txt',
            dataType: 'json',
            success: r => {
                console.log(r);          // ajax成功获取数据
                $scope.arr = r;
                console.log($scope.arr); // $scope.arr已被赋值，控制台已显示数据
            }
        });
    });
</script>
```

这时候我们如果偏要用**非angular内置方法**，就需要手动来触发`$digest`了，但一般我们不会直接调用`$digest`, 而是调用`$apply`方法, 由它调用全局脏检查`$rootScope.$digest()`;

它有**两种**常用的调用形式，一种是不加参数直接在需要脏检查时调用`$apply()`, 另一种是，用函数作为`$apply`的参数，将要执行的代码包裹在其中:

```html
<!-- 刚才的栗子，可以成功在view中展现了 -->
<body ng-app="mod1" ng-controller="ctr1">
    <ul>
        <li ng-repeat="item in arr">{{item}}</li>
    </ul>
</body>
<script>
    angular.module('mod1', []).controller('ctr1', ($scope) => {
        $scope.$apply(() => {   // 调用$apply并传入参数
            $.ajax({
                url: 'data/a.txt',
                dataType: 'json',
                success: r => {
                    console.log(r);
                    $scope.arr = r;
                    console.log($scope.arr);
                }
            });
        });
    });
</script>
```
<hr />

#### 为什么要大费周章不直接调用`$digest`?
找出`$apply`方法的源码，可以看出这样做的好处有两点:
1. 当需要执行的代码出错时，可以**抛出异常**；
2. 当需要执行的代码出错时，不影响**脏检查继续执行**，以保证最大限度的用户体验；

所以我们不仅要尽量使用`$apply`, 而且要带**参数**，这样才能用到它的错误处理；

```js
{
  /**
   * method $apply
   * @param  {exp} expr  表达式，一般用函数，可以为空
   */
  $apply: function(expr) {
    try {
      beginPhase('$apply');
      try {
        return this.$eval(expr);
      } finally {
        clearPhase();
      }
    } catch (e) { // 错误处理
      $exceptionHandler(e);
    } finally {
      try {
        $rootScope.$digest();
      } catch (e) {
        $exceptionHandler(e);
        throw e;
      }
    }
  },
}
```
<hr />

#### 如果回调函数`listener`改变了model会发生什么？
如果在一个`$digest`中，某个`watcher`的回调函数`listener`改变了model值，会增加一次脏检查的循环次数，如果又有某个`listener`改变了model值，会再次增加循环...直到不再有model改变，或者到达`$digest`的迭代上限10次(默认值)为止。

1. 触发一次`$digest`，要检查`listener`是否更改model, 所以**至少**会进行两次遍历；
2. 当没有新的脏数据产生时，迭代才会被终止；
3. 如果迭代超过限度(默认10次)，会抛出异常；

另外，还有两个异步方法`$applyAsync`, `$evalAsync`;<br />
`$applyAsync`用来解决，某一时刻大量触发`$digest`产生的性能问题，例如同时有很多ajax请求修改了model, 它会将`$digest`延迟处理;<br />
`$evalAsync`用来在某个`$digest`执行中插队到下一个循环执行一段代码;
