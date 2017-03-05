---
layout:     post
title:      "Sublime Text 配置前端开发代码校验"
subtitle:   "ST3配置校验工具SublimeLinter来校验代码低级错误"
date:       2017-02-20 17:38:00
author:     "AllocatorXy"
header-img: "img/post-bg-windmill.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - 工具
    - Sublime Text
---

### 准备工作
sublime最让人蛋疼的一点就是：**<font color="red" style="font-size: 18px;">没·有·语·法·错·误·提·示·功·能!</font>**<br />
所以我们需要通过配置插件来解决，但插件**坑挺多的**，这篇文章旨在让我们更舒服地使用SublimeLinter的校验功能。
<hr />

##### node.js
SublimeLinter依赖于node.js, 所以最开始我们需要安装**<a target="_blank" href="https://nodejs.org/en/">node.js</a>**<br />

**注意需要将npm<a style="color: red;" href="http://www.runoob.com/nodejs/nodejs-npm.html">初始化</a> <= <font color="blue">不会戳这里</font>** *- added on Feb.27th*


##### SublimeLinter
首先要确认sublime有没有安装`package control`, 如果没有安装：在sublime中按``ctrl+` ``打开控制台，然后输入下面这段代码后按`enter`, 安装package control.

```python
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

装好package control后**重启**sublime, 开始安装SublimeLinter:
1. 在sublime中`ctrl+shift+p`打开命令模式;
2. 找到`Package Control: Install Package`并执行，命令模式支持模糊匹配，一般输`pci`就找到了;
3. 找到`SublimeLinter`并安装;
<img src="/img/in-post/sublimeLinter/sublimelinter.png" alt="" />

sublimeLinter暂时还不能用，需要配置一下，依次打开:<br />
`首选项(preference)=>插件设置(plugins settings)=>SublimeLinter=>settings-user`<br />
然后把下面的代码复制进去修改成自己的设置，保存；<br />
**另外**，`工具(Tools)=>SublimeLinter`也可以调整某些设置；

```js
{
    "user": {
        "debug": false, // debug模式是否开启
        "delay": 0.25,
        "error_color": "FFAD00", // 错误标记颜色
        "gutter_theme": "Packages/SublimeLinter/gutter-themes/Koloria/Koloria.gutter-theme",
        "gutter_theme_excludes": [],
        "lint_mode": "background", // 校验模式:background,load/save,save only,manual
        "linters": { // 这里是各个校验模块的设置区域
            "csslint": {
                "@disable": false, // 如果disable设为true，则禁用这个模块
                "args": [],        // 参数，这里填写npm中的指令
                "errors": "",      // 等级为error的规则
                "excludes": [],    // 包括哪些文件
                "ignore": "",      // 等级为ignore的规则
                "warnings": ""     // 等级为warning的规则
            },
            "eslint": {
                "@disable": false,
                "args": [],
                "excludes": []
            },
            "htmlhint": {
                "@disable": false,
                "args": [],
                "excludes": []
            },
            "scss": {
                "@disable": false,
                "args": [],
                "exclude-linter": "",
                "excludes": [],
                "include-linter": ""
            }
        },
        "mark_style": "outline", // 设置错误提醒样式:fill/outline/...etc
        "no_column_highlights_line": false,
        "passive_warnings": false,
        "paths": {
            "linux": [],
            "osx": [],
            "windows": [
                // 这里改成你的node安装目录
                "D:/Program Files/nodejs/node.exe"
            ]
        },
        "python_paths": {
            "linux": [],
            "osx": [],
            "windows": []
        },
        "rc_search_limit": 3,
        "shell_timeout": 10,
        "show_errors_on_save": false,
        "show_marks_in_minimap": true,
        "syntax_map": {
            "html (django)": "html",
            "html (rails)": "html",
            "html 5": "html",
            "javascript (babel)": "javascript",
            "magicpython": "python",
            "php": "html",
            "python django": "python"
        },
        "warning_color": "DDB700",
        "wrap_find": true
    }
}
```
<hr />

### 配置校验功能插件
`SublimeLinter`只是一个对错误代码进行提示的插件，我们还需要一些**特定语法**校验的插件来告诉`SublimeLinter`我们需要在哪些语言环境下，检查哪些错误。<br />
**<font color="red">每个</font>**语法校验插件的配置都分为**<font color="red">两部分</font>**，分别为`sublime`端和`node`端；<br />
对于每个语法校验插件，只有`sublime`端和`node`端**都**配置完毕才可以设置在sublime集成相应的语法校验。
<hr />

#### HTML语法校验

##### HTML语法校验-ST端
html语法校验不止有一个插件，这里我选用`SublimeLinter-contrib-htmlhint`;
1. 在sublime中`ctrl+shift+p`打开命令模式;
2. 依然是执行`Package Control: Install Package`;
3. 找到`SublimeLinter-contrib-htmlhint`回车或点击安装;
<img src="/img/in-post/sublimeLinter/htmllinter.png" alt="" />

##### HTML语法校验-Node端
1. 现在我们需要打开`命令行`工具，windows按`win+r`或点击开始菜单，点击运行，输入`cmd`并回车打开命令行。
2. 输入`npm install -g xg-htmlhint`，并等待安装完毕。<br />
**这里如果不想全局安装，可以去掉参数`-g`**
<img src="/img/in-post/sublimeLinter/node-html.png" alt="" />
<hr />

#### CSS语法校验

##### CSS语法校验-ST端
html语法校验也不止有一个插件，这里我选用`SublimeLinter-csslint`;<br />
其实css不是很需要纠错，逻辑清晰的话，其实完全可以不需要这种校验工具，很快就能定位错误在哪。<br />
**如果不需要css校验可以略过这一段了**。

1. 在sublime中`ctrl+shift+p`打开命令模式;
2. 执行`Package Control: Install Package`;
3. 找到`SublimeLinter-csslint`回车或点击安装;
<img src="/img/in-post/sublimeLinter/csslinter.png" alt="" />

##### CSS语法校验-Node端
1. 打开`命令行`工具，windows按`win+r`或点击开始菜单，点击运行，输入`cmd`并回车打开命令行。
2. 输入`npm install -g csslint`，并等待安装完毕。<br />
**这里如果不想全局安装，可以去掉参数`-g`**

##### CSS语法校验设置
可以在`SublimeLinter`的`user-settings`中设置规则的提示等级;

```js
"csslint": {
    "@disable": false, // 如果disable设为true，则禁用这个模块
    "args": [],        // 参数，这里填写npm中的指令
    "errors": "",      // 等级为error的规则
    "excludes": [],    // 包括哪些文件
    "ignore": "",      // 等级为ignore的规则
    "warnings": ""     // 等级为warning的规则
}
```

#### JS语法校验
以前用`SublimeLinter-jshint`作为js的校验工具，但这个东西非常的不灵活，对框架的支持也不算很优秀，整体来讲感觉不如IDE的语法校验。<br />
所以这里我选用一个配置起来**相对复杂**的校验工具:`eslint`, 但使用起来是比`jshint`要舒服很多的,正所谓磨刀不误砍柴工~

##### JS语法校验-ST端
1. 在sublime中`ctrl+shift+p`打开命令模式;
2. 执行`Package Control: Install Package`;
3. 找到`SublimeLinter-contrib-eslint`回车或点击安装;
<img src="/img/in-post/sublimeLinter/eslint.png" alt="" />

##### JS语法校验-Node端
1. 打开`命令行`工具，windows按`win+r`或点击开始菜单，点击运行，输入`cmd`并回车打开命令行。
2. 输入`npm install -g eslint`，并等待安装完毕。<br />
**这里如果不想全局安装，可以去掉参数`-g`**

##### JS语法校验-配置ESlint
使用`ESlint`，需要我们自行配置规则，这里我推荐用`eslint`自带的命令初始化配置文件;

1. 首先在命令行输入`eslint --init`;
2. eslint会问你要采用何种方式初始化，这里我强烈推荐让eslint读取一个你写的比较规范而且比较长的js文件，键盘按上下键选到这项按回车;<br />
`Inspect your JavaScript file(s)`
<img src="/img/in-post/sublimeLinter/eset1.png" alt="" />
3. 我选择了jQuery库作为它读取的对象，然后回车，接下来它会问你设置文件想要哪种格式的，这里我选择js;
4. 接下来它会问几个使用习惯相关的问题，之后就会在`c://users/[你的用户名]`目录下生成一个全局的配置文件`.eslintrc.js`
<img src="/img/in-post/sublimeLinter/eset2.png" alt="" />

<font color="red">ESlint是一个插件式的代码检验工具，默认可以检测的有:javaScript, react, jquery等, 可以到<a target="_blank" href="https://www.npmjs.com/">NPM</a>下载其他插件，以适配各种代码，这里就不多说了。</font>

**到这里其实ESlint已经可以用了**，但可能和你平常写代码的习惯不太符合，我们打开一个js文件：
<img src="/img/in-post/sublimeLinter/eset3.png" alt="" />
<img src="/img/in-post/sublimeLinter/eset4.png" alt="" />
可以发现，有很多错误被找出来，将光标定位到错误提示的地方，会在**sublime底栏**显示错误信息，例如这个32行的table这里，显示`String must use singlequote.(quotes)`<br />
这里**括号里**显示的就是**与这个错误相关的设置**在设置文件`.eslintrc.js`中的选项，而括号外显示的就是具体错误原因，这里因为我设置的代码风格是字符串用单引号，所以会提示我规范我的代码；

如果我想修改有关于校验规则怎么办呢？以上面这个`quotes`为例：<br />
1. 打开文件：`c://users/[这里改成你的用户名]/.eslintrc.js`;
2. 搜索`quotes`;
<img src="/img/in-post/sublimeLinter/eset5.png" alt="" />
3. 找到对应选项，可以看到里面的设置，error是提示等级，ESlint有三种提示等级(error,warn,off)，分别对应2,1,0三个数字，提示等级可以设数字，也可以设英文;

>"off" or 0 - turn the rule off <br />
>"warn" or 1 - turn the rule on as a warning (doesn’t affect exit code) <br />
>"error" or 2 - turn the rule on as an error (exit code is 1 when triggered)

`Eslint`在js中可以用注释设置**临时规则**，也可以为**不同**的工作目录指定**不同**的配置文件;

```js
/* eslint quotes: ["error", "double"], curly: 2 */
```

详细设置请阅读**<a target="_blank" href="http://eslint.org/docs/user-guide/configuring">官方文档</a>**