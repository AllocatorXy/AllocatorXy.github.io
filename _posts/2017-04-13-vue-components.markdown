---
layout:     post
title:      "Vue-Components"
subtitle:   "vue中的组件/组件之间的通信"
date:       2017-04-13 22:28:00
author:     "AllocatorXy"
header-img: "img/bg-data0.jpg"
header-mask: 0.3
tags:
    - 前端开发
    - JavaScript
    - Vue
---

### 组件注册
vue中的组件类似于angular里的directive, 用一段模板代码和一个页面中的指令生成dom结构;<br />
不同的是，vue中的组件分为`全局组件`和`局部组件`;<br />

- 不论是全局组件还是局部组件，都必须在vue实例作用域内才有效;
- 组件需要在vue实例声明之前注册;
- 组件名不要有大写字母 -- Vue 2.2.4;
- 对于某些组件名称不能直接使用，但在字符串模板中没有限制(推荐用x-template);

##### 全局组件
```html
<div id="box">
    <aaa />
    <aaa></aaa>
</div>
<div id="box1">
    <aaa />
</div>

<script>
    // Vue.component(name, options)
    Vue.component('aaa', {
        template: '<h2>我是全局组件</h2>'
    });
    new Vue({
        el: '#box'
    });
    new Vue({
        el: '#box1'
    });
</script>
```

全局组件注册后，在所有vue作用域内都可以调用，以上代码的dom输出为：

```html
<div id="box">
    <h2>我是全局组件</h2>
    <h2>我是全局组件</h2>
</div>
<div id="box1">
    <h2>我是全局组件</h2>
</div>
```

##### 局部组件
```html
<div id="box">
    <my-h3></my-h3>
    <my-h4/>
</div>
<div id="box1">
    <my-h3></my-h3>
</div>
<script>
    new Vue({
        el: '#box',
        components: {
            'my-h3': {
                template: '<h3>标题3</h3>'
            },
            'my-h4': {
                template: '<h4>标题4</h4>'
            }
        }
    });
    new Vue({
        el: '#box1',
        components: {
            'my-h3': {
                template: '<h6>标题6</h6>',
            }
        }
    });
</script>
```

局部组件在各个vue实直接互不干扰，以上代码的dom输出为：

```html
<div id="box">
    <h3>标题3</h3>
    <h4>标题4</h4>
</div>
<div id="box1">
    <h6>标题6</h6>
</div>
```
<hr />

### 组件data
组件中的data不能直接定义，需要依赖一个data函数来绑定到组件上:

```js
Vue.component('aaa', {
    template: '<h2>我是全局组件</h2>',
    data () {
        return {
            key: 'value'
        }
    }
});
```
<hr />

### 组件模板
在自定义组件时，会受到一些HTML的限制，某些元素限制了能被它包裹的元素，但当使用模板字符串时，将没有这些限制，所以推荐将组件的模板写到模板字符串中:

```html
<!-- vue1 -->
<template id="tpl1">
    <h2>666</h2>
</template>

<!-- x-template 模板 vue2 -->
<script type="text/x-template" id="tpl2">
    <h3>777</h3>
</script>

<script>
    Vue.component('aaa', {
        template: `<h2>我是全局组件</h2>` // 内联模板字符串
    });
    Vue.component('bbb', {
        template: '#tpl1'
    });
    Vue.component('ccc', {
        template: '#tpl2'
    });
</script>
```
<hr />

### 组件通信

##### 父级数据 => 子级
```html
<div id="box">
  父级: {{msg}}
  <!-- 1.绑定数据到子级组件 -->
  <my-h3 :aaa="msg" :bbb="msg1"></my-h3>
</div>

<script type="text/x-template" id="tpl">
  <div @click="fnClik">
    <h3>我是子组件{{aaa}}--{{bbb}}</h3>
  </div>
</script>

<script>
  Vue.component('my-h3', { //定义
    template: '#tpl',
    /* 2.使用props继承/验证数据 */
    // props:['aaa','bbb'],
    // props可以指定数据类型[String, Number, Boolean, Function, Object, Array] 如果不匹配，会报错但不会终止程序
    props: {
      aaa: Number, 
      // 字符串/数字且必须有值
      bbb: {
        type: [ String, Number ],
        required: true
      },
      // boolean且默认为true
      ccc: {
        type: Boolean,
        default: true
      },
      // 默认值可以由工厂函数返回
      ddd: {
        defalut() {
            return 'def';
        }
      },
      // 也可以自定义验证函数
      eee: {
        validator(val) {
            return !val;
        }
      }
    },
    methods: {
      fnClik() {
        this.msg = 456;
        console.log(this.msg);
      }
    }
  });
  new Vue({
    el: '#box',
    data: {
      msg: 123,
      msg1: 'hehe'
    }
  });
</script>
```

##### 子级数据 => 父级
`props`在继承父级数据时，类似于函数的传参，会根据数据类型的不同，选择不同的继承方式(传值/传址);<br />
因此，如果我们想要让子级和父级的数据双向绑定，只需要让被继承的父级数据时引用类型的，就可以实现父级和子级的双向数据绑定：

```html
<div id="box" @click="aaaaa">
  父级: {{msg.a}}
  <my-h3 :aaa="msg"></my-h3>
</div>
<script type="text/x-template" id="tpl">
  <div @click.stop="fnClik">
    <h3>我是子组件{{aaa.a}}</h3>
  </div>
</script>
<script>
  Vue.component('my-h3', { //定义
    template: `#tpl`,
    props: ['aaa'], // 继承msg对象
    methods: {
      fnClik() {
        this.aaa.a = '0000';
      }
    }
  });
  new Vue({
    el: `#box`,
    data: {
      msg: { // 将父级数据定义为对象
        a: 123
      }
    },
    methods: {
      aaaaa() {
        this.msg.a = '1111';
      }
    }
  });
</script>
```

##### `$emit` & `$on`
vue的每个实例上都提供了两个方法`$emit`和`$on`, 用来发送和接收自定义事件，用法很像webSocket;<br />
子组件发送数据 => 父级在子组件出现的地方监听对应接口 => 触发调用父级方法

```html
<div id="box">
  父组件: {{aaa}}
  <!-- 监听接口'child-data' -->
  <my-h3 @child-data="show"></my-h3>
</div>

<script type="text/x-template" id="list">
  <div>
    <h3 @click="send">我是子组件{{msg}}</h3>
  </div>
</script>

<script>
  Vue.component('my-h3', { //定义
    template: `#list`,
    data() {
      return {
        msg: 123
      }
    },
    methods: {
      send() { // 数据发送
        this.$emit('child-data', this.msg)
      }
    }
  });
  new Vue({
    el: '#box',
    data: {
      aaa: null
    },
    methods: {
      show(m) {
        this.aaa = m;
      }
    }
  })
</script>
```

实际上这个方法不仅可以实现父子级的双向数据绑定，还可以实现非父子间的双向数据绑定，因为每个实例上都有`$emit`和`$on`方法，我们可以找另一个实例作为中介，来实现**任何**组件之间的双向通信，实现起来和webSocket很像：

```html
<div id="box">
  <a-h3></a-h3>
  <b-h3></b-h3>
</div>

<script type="text/x-template" id="tpl">
  <div>
    <h3 @click="send">{{msg}}</h3>
  </div>
</script>
<script type="text/x-template" id="tpl1">
  <div>
    <h3>{{msg}}</h3>
  </div>
</script>

<script>
  const ev = new Vue(); // 中介实例
  // compo1
  Vue.component('a-h3', {
    template: '#tpl',
    data() {
      return {
        msg: 'Adata'
      };
    },
    // 注意这里，挂载顺序问题，因为挂载a时，b的监听还没有启动，所以挂载完后没效果
    mounted() {
     ev.$emit('a-h3', this.msg);
    },
    methods: {
      send() {
        ev.$emit('a-h3', this.msg);
      }
    }
  });
  // compo2
  Vue.component('b-h3', {
    template: '#tpl1',
    data() {
      return {
        msg: 'Bdata'
      };
    },
    mounted() {
      ev.$on('a-h3', res => { // hack, 这里用箭头函数可以把this定位到b组件
        alert(res);      // Adata
        alert(this.msg); // Bdata
        this.msg = res;  // Bdata => Adata
      });
    }
  });
  new Vue({
    el: '#box',
  });
</script>
```
