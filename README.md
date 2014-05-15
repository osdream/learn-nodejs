Node.js简介
======

## Node.js 安装

* 下载地址
    * [http://nodejs.org/download/]
* 安装
    * 上面下载地址里提供了二进制安装包、源码包，可下载安装
    * 如果你是熊掌公司的，Linux 下还可以使用 [jumbo](http://jumbo.ws/) 安装 (`jumbo install nodejs`)
    * 另外Linux各种操作系统都提供了包安装源，可通过 `apt-get/yum/rpm` 等方式安装，可参考 [https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager]

## 基础

任何程序都是从 `Hello World` 开始的，那我们先来整一个。

### Hello World

第一个Node.js程序

```javascript
// demo/hello_world/hello_world.js
console.log('Hello World!\n');
```

### Node.js的发展简史

* 诞生于2009年，创始人：Ryan Dahl

> Ryan Dahl: 我经常需要写一些基于事件的程序，我喜欢设计开发基于事件的服务器，因为我觉得他们很容易理解，状态被保存，有可以不断的修改状态。你可以无限的接收socket请求，我可以只使用非阻塞IO就可以使服务器的延迟非常低。

* 拥有活跃的社区：https://npmjs.org/
* 发展简史：
    * 2009.2 - Ryan Dahl在博客上宣布准备基于V8创建一个轻量级的Web服务器并提供一套库
    * 2009.5 - Ryan Dahl在GitHub上发布了最初版本的部分Node.js包，随后几个月里，有人开始使用Node.js开发应用
    * 2009.11/2010.4 - JSConf大会安排Node.js讲座
    * 2010年底 - Node.js获得云计算服务商Joyent资助
    * 2011.7 - windows版本发布
    * 2012 - NPM作为内置工具提供/cluster模块

### 什么是Node.js

* C++写的

> 据Node.js创始人Ryan Dahl回忆，他最初希望采用Ruby来写Node.js，但是后来发现Ruby虚拟机的性能不能满足他的要求，后来他尝试采用V8引擎，所以选择了C++语言。

* 服务器端的JavaScript
    * 为什么选择 JavaScript? 单线程、没有服务端IO处理、无历史包袱(这里的无包袱是指 JavaScript 在服务端的发展几乎是空白)
    * 基于V8引擎的JavaScript运行环境
    * 单线程，高并发：事件驱动、异步I/O
    * CommonJS模块系统：可以像其他语言一样同步的引入外部依赖

* Node.js 架构图

![node.js constructure](http://ecma.bdimg.com/adtest/deaec564fcadc9e9f8b7f107dad1514c.png)

Libeio 和 Libev 分别支持的是 Linux、Unix、Mac 等 POSIX 的异步式 I/O 和事件触发，IOCP 是 Windows 下的相关库，Libuv 对以上三者进行了一层封装。IOCP 是 Input/Output Completion Port，输入输出完成接口

### 都有哪些公司用了Node.js

* LinkedIn: 全球最大的职业社交网站
    * 用Node代替Rails，减少了27台服务器，速度提升20倍
* 淘宝
    * cnodejs.org
    * nodejs-kissy: 前端应用框架
    * node-myfox: mysql分布式集群查询系统
* Microsoft
    * Windows Azure: 基于云计算的操作系统
    * Skype
* 百度
    * edp: 百度前端开发平台，各种开发工具集
* 腾讯朋友网: 长连接
* 新浪网: mysql代理层
* ...

### Node.js基础

* 跟浏览器端JavaScript的区别

    | 浏览器端JS | Node.js |
    | ---------- | ------- |
    | ECMAScript + DOM + BOM组成 | 基于ECMAScript，借助于V8引擎，使用C++编写扩展 |

* 跟其他编程语言的区别
    * Node.js不是一门独立语言
    * 基于JavaScript基础的服务端语言
    * 可以跳过Web服务器这一层直接面向前端开发

![node.js vs php](http://ecma.bdimg.com/adtest/f9595b2ec28fb68c10cac2cba8c0b281.png)

#### 事件循环

Node.js的高并发的原因之一是事件循环，什么是事件循环呢？[TODO]

那么事件循环为啥能提高并发量呢？

* 使用事件循环来处理I/O，而不是采用多线程，避免了线程切换的开销

![event loop](http://ecma.bdimg.com/adtest/5bac09644d53ecff3ab900d87cd07c2d.png)

事件循环的例子：请求index.html的过程

![event loop example](http://ecma.bdimg.com/adtest/c9c258f41120027146b548895254d428.png)

过程

* 收到对 index.html 的请求
* 堆栈逐层执行，然后进入事件循环(ev_loop)，并睡眠等待
* 从操作系统读取 index.html 文件内容，触发事件循环中的回调，然后发送给浏览器端

#### 异步I/O

Node.js 高并发的另一个原因是异步I/O

* 服务器做除了I/O以外的事情，从缓慢的I/O等待中解放出来
* Node利用JS天生的事件驱动，为I/O绑定回调

同步I/O：

```php
<?php
$result = mysql_query('SELECT * FROM ...');
while ($r = mysql_fetch_array($result)) {
    // do something
}
?>
```

异步I/O

```javascript
mysql.query('SELECT * FROM ...', function(err, result) {
    // do something
});
// do something else without waiting db result
```

* 同步I/O和异步I/O对比

![sync io vs async io](http://ecma.bdimg.com/adtest/63022aa0761bc9430858da53a605c2c5.png)

### Node.js 的应用场景

适合做什么

* 具有复杂逻辑的网站；
* 基于社交网络的大规模 Web 应用；
* Web Socket 服务器；
* 命令行工具；
* 单元测试工具；

不适合做什么

* 计算密集型程序

***

## 实践

优化一下前面的 Hello World 程序，需求：

* 能支持命令行参数
* 能被其他 Node.js 程序复用
* 能记录被调用次数

我们先让它支持命令行参数：

```javascript
// demo/hello_world/hello_world_with_input.js
var name = process.argv[2];
console.log('Hello ' + (name || 'World') + '!\n');
```

如果要让我们的代码能被其他 Node.js 程序调用，那么我们需要暴露一些函数出来：

```javascript
// demo/hello_world/hello.js
var name = 'World';

exports.setName = function(newName) {
    name = newName;
};

exports.sayHello = function() {
    console.log('Hello ' + name + '!');
    // 记录调用次数
    increase();
}

var fs = require('fs');
function increase() {
    var count = fs.readFileSync('count.txt', 'utf8');
    count = parseInt(count);
    fs.writeFileSync('count.txt', ++count);
}
```

我们写一个调用脚本试试：

```javascript
// demo/hello_world/use_hello.js
var hello = require('./hello');

hello.setName('Node.js');
hello.sayHello();
```

通过上面的程序我们接触到了一些新东西：

* 全局对象

process 是个什么东西，为啥能直接使用？process是全局对象，稍后会详细介绍常用的全局对象。

* 模块

直接通过require就可以使用我们定义的hello模块，同时也可以require内部模块(例如: fs)


### 全局对象

* global

全局变量，等同于浏览器里的 window 对象，在同一次脚本运行中该 global 都是指向同一个对象。

可以用 console.log 输出出来看看

```javascript
console.log(global);
```

* process

当前运行 Node.js 相关的运行环境，常用的属性或函数包括：

    * process.argv
    * process.stdin
    * process.stdout
    * process.nextTick

* console

打印日志的 console 对象，跟浏览器里的类似

    * console.log
    * console.error
    * console.trace

还包括不少全局对象，这里就不一一例举了。

### 模块

模块是 Node.js 中组织功能代码的一个基本封装单元，通过exports暴露变量或方法供其他模块调用

下面会依次介绍：

* 模块(Module)和包(Package)
* 模块加载机制
* 核心模块
* 模块列表

### 模块和包

那么什么是模块呢？

模块：一个Node.js文件就是一个模块

* JS文件 ( *.js )
* JSON文件 ( *.json )
* 编译过的C/C++扩展 ( *.node )
* 包含index.js的文件夹

当我们实现一个较复杂功能时，往往会开发了一堆模块，提供给别人使用的时候，会发现一个问题，因为不是一个整体，使用者使用的时候会很麻烦，那么有啥解决办法吗？包可以解决，包通过将模块整合在一块，对外提供统一的入口。

那么什么是包呢？

包：实现了某个功能的模块集合

* 包 = 模块集合 + Meta Data(package.json)
* 模块和包经常混用，没有本质区别

Node.js 有很好的社区：npmjs.org，开发者在上面共享自己开发的包，怎么去使用他们提供的包呢，这就需要包管理器 (npmjs)

### 包管理器

NPM：Node Package Manager

安装包的命令：

* npm install somepack
* npm install -g somepack

其中 `-g` 参数用于指明是否是安装到全局环境里，对于一些提供了命令行的包来说，安装到全局环境可以使用其提供的命令

### 模块中的变量

可在模块里直接使用的变量：

* exports 暴露方法和变量的对象
* require 引入其他模块的函数
* module 模块相关信息
* __filename：当前脚本路径
* __dirname：当前脚本所处文件夹路径

```javascript
// demo/module/hello.js
// 可直接使用的变量
console.log('module.exports === exports: ' + (module.exports === exports));
console.log('__filename: ' + __filename);
console.log('__dirname: ' + __dirname);
console.log('module: ');
console.log(module);

var name = 'World';

exports.setName = function(newName) {
    name = newName;
};

exports.sayHello = function() {
    console.log('Hello ' + name + '!');
}
```

![test new module](http://ecma.bdimg.com/adcoup-mat/songao/nodejs/img/test_new_module.png)

### 覆盖 exports

前面介绍了，可以直接在exports上添加变量或方法来暴露给其他模块，如果我们直接想将一个构造函数输出出来，怎么处理呢？

* 使用module.exports输出对象

在初始情况下 `module.exports === exports` 这两个其实是同一个对象，不过对 module.exports 赋值之后，exports就不能再用于对外提供变量和函数了(已经不是同一个对象)

```javascript
// demo/module/hello_class.js
function Hello() {
    // ...
}
module.exports = Hello;
```

```javascript
// demo/module/use_hello_class.js
var Hello = require('./hello_class');
var hello = new Hello();
```

### 模块加载机制

require的写法：

* 原生模块：require('http')
* 相对路径：require('./mod')
* 绝对路径：require('/home/work/mod')
* 非原生模块：require('mod')

查找过程：

* 尝试添加.js、.json、.node后缀
* 尝试将require的参数作为一个包来进行查找
* 取出module.paths数组中的下一个目录作为基准查找

单次加载：同一个模块只会加载一次，这个可以从Node.js核心模块代码里看出来

```javascript
// nodejs/lib/module.js
Module._load = function(request, parent, isMain) {
    // ...
    var filename = Module._resolveFilename(request, parent);
    var cachedModule = Module._cache[filename];
    if (cachedModule) {
        return cachedModule.exports;
    }
    // ...
}
```

```javascript
// demo/module/use_hello.js
var hello = require('./hello');
hello.sayHello();
hello.setName('Node.js');
hello.sayHello();

// 单次加载
var hello2 = require('./hello');
hello2.sayHello();
console.log(hello === hello2);

// 加载方法
//var hello = require('./hello');
//var hello = require('./hello.js');
//var hello = require('/home/users/songao/work/node/learn/module/hello');
//var hello = require('hello_in_node_modules');
```

### 核心模块

Node.js提供了不少常用的模块，这些模块可以称为内部模块或核心模块

可参见：[http://nodejs.org/api/] [TODO]

* fs
* http
* util
* events
* child_process
* cluster
* ...

下面介绍几个常用的核心模块

#### 核心模块：fs

使用例子见：demo/core_module/test_fs.js

* fs.exists(path, callback)
* fs.stat(path, callback)
* fs.readdir(path, callback)
* fs.readFile(path, [options], callback)
* fs.writeFile(filename, data, [options], callback)

#### 核心模块：http

使用例子见：demo/core_module/test_http.js

* http.createServer([requestListener])
* http.request
* http.get

#### 模块列表

* [https://npmjs.org/]
* [https://github.com/joyent/node/wiki/modules]

![package count](http://ecma.bdimg.com/adcoup-mat/songao/nodejs/img/package_count.png)

***

## Node.js调试方法

开发程序必不可少的就是调试功能，Node.js 提供了多种调试方式。

当然我们也可使用 console.log 来进行调试，就像在浏览器中使用 alert 一样。

下面介绍 Node.js 提供的调试方法：

### 本地/远程 命令行调试

用 node debug 命令去运行要调试的脚本，可以输入一些命令去控制调试的过程

```javascript
// 本地调试
node debug test_debug.js

// 远程调试
// debug server
node --debug-brk=8112 test_debug.js
// debug client
node debug localhost:8112
```

```javascript
// 常用命令
run                             // 执行脚本，第一行暂停
restart                         // 重启脚本
cont, c                         // continue
next, n                         // next
step, s                         // step into
out, o                          // step out
setBreakpoint(line), sb(line)   // 设置断点
backtrace, bt                   // 显示调用栈
watch(expr)                     // 监视表达式
repl                            // 打开求值环境(Read-Eval-Print Loop)
kill                            // 终止脚本
```

![debug in cmd line](http://ecma.bdimg.com/adcoup-mat/songao/nodejs/img/cmd_debug_local.png)

### 使用node-inspector调试

node-inspector 是一个可以利用浏览器界面去调试的模块，你会发现它的调试界面跟 chrome developer tool 一模一样。

```javascript
// 安装node-inspector
npm install -g node-inspector

// 启动debug server
node --debug-brk=8112 test_debug.js

// 启动node-inspector
node-inspector --web-port=8113

// 在浏览器中访问如下路径
http://localhost:8113/debug?port=8112
```

![debug width node-inspector](http://ecma.bdimg.com/adcoup-mat/songao/nodejs/img/node_inspector.png)

### 监控重启调试

使用supervisor，脚本修改时会自动重启，这种对于用 Node.js 来做 Web 开发会非常方便，修改完代码，服务器会自动重启，免去了手动重启的麻烦。

```
// 安装supervisor
npm install -g supervisor

// 用supervisor启动脚本
supervisor web.js
```

![debug width supervisor](http://ecma.bdimg.com/adcoup-mat/songao/nodejs/img/supervisor_test.png)

### 其他调试方式

* Eclipse插件

***

## 开发网站

Node.js 的一个很重要的应用场景就是开发网站，我们从一个简单网站开始：

### 一个简单的网站

处理过程：

* 解析URL，根据path不同做不同处理
* 处理POST
* 返回响应(状态码，组装内容)

```javascript
var http = require('http');
var url = require('url');
var fs = require('fs');
var querystring = require('querystring');
var server = http.createServer(function(req, res) {
    var query = url.parse(req.url, true);
    if (query.path == '/') {
        var postData = '';
        req.setEncoding('utf8');
        req.on('data', function(chunk) {
            postData += chunk;
        });
        req.on('end', function() {
            var post = querystring.parse(postData);
            res.writeHead(200, {
                'Content-Type': 'text/html'
            });
            res.write('<h1>Node.js</h1>');
            res.end('<p>postData: text=' + post.text + '</p>');
        });
    }
    else if (query.path == '/form.html') {
        fs.readFile('form.html', function(err, code) {
            res.writeHead(200, {
                'Content-Type': 'text/html'
            });
            res.end(code);
        });
    }
});
server.listen('8111');
console.log('Server is listening port 8111...');
```

### 用Express建站

从前面的小结看出，直接写代码去开发一个网站是比较麻烦的，啥事情都得自己处理。
为此 Express 做了很多封装，便于快速建站。

#### Express是什么

一个MVC框架

> Express 是一个简洁而灵活的 node.js Web应用框架, 提供一系列强大特性帮助你创建各种Web应用
> 丰富的HTTP工具以及来自Connect框架的中间件随取随用，创建强健、友好的API变得快速又简单

```
// 安装express-generator命令
npm install -g express-generator
// 运行命令，得到一个简单的WEB应用
express htdocs
// 安装依赖
cd htdocs
npm install
```

### 用Express开发一个简单博客系统

#### 需求：

* 能发表博文
* 能展示博文列表

实现见 demo/express/web

#### 中间件

Express 是基于 Connect 的，很多对请求和响应的处理都是通过Connect的中间件来做的。
中间件(Middleware)可以理解为一个对用户请求进行过滤和预处理的东西，它一般不会直接对客户端进行响应，而是将处理之后的结果传递下去。

Connect提供的可用的中间件：[https://github.com/senchalabs/connect#middleware]

```javascript
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded());
app.use(cookieParser());
```

![middleware](http://ecma.bdimg.com/adcoup-mat/songao/nodejs/img/middleware_flow.png)

我们还可以自定义中间件：

```javascript
// req 用于获取请求信息, ServerRequest 的实例
// res 用于响应处理结果, ServerResponse 的实例
// next() 函数用于将当前控制权转交给下一步处理，如果给 next() 传递一个参数时，表示出错信息
app.use(function(req, res, next) {
    var startTime = new Date();
    next();
    var endTime = new Date();
    console.log(endTime - startTime);
});
```

#### 路由

路由是一个特殊的中间件，用于根据 path 和 query 来转发请求到相应的 Controller (即routes/*)

```javascript
// 创建router
var router = express.Router();

// 配置routes
// 对任何进入这个router的请求都通过这个处理函数：
router.use(function(req, res, next) {
  // 像其他中间件一样处理请求
  next();
});

// 处理任何以/events结尾的GET请求
// 完整的path(包括/events前的部分)依赖于在哪里使用这个router
router.get('/events', function(req, res, next) {
  // ..
});

// 在 app.js使用上面创建的router
// 只会转发符合 /calendar/* 的请求到router
// 所以上面 events 的route 的完整path应该是：/calendar/**/events
app.use('/calendar', router);
```

* 常用方法

    * router.get
    * router.post
    * router.param

#### 模板引擎

Express 的 View 层使用模板引擎来实现的，Express 默认支持 `ejs/jade/hogan.js`
如果想要使用其他模板引擎，需要对其进行适配，好在模块 consolidate 已经对常用的模板引擎都做了适配，使用起来很方便

```javascript
// 更换模板引擎
var engines = require('consolidate');
app.engine('html', engines.mustache);

app.set('view engine', 'html');
```

#### 数据库

开发网站经常要用到数据库，在 npmjs.org 上有对连接各种数据库的封装好的模块

* mysql模块

```javascript
var mysql = require('mysql');
var connection = mysql.createConnection({
    'host': 'localhost',
    'port': '3306',
    'user': 'root',
    'password': '123456'
});

connection.query('SELECT * FROM ...', function(err, rows, fields) {
    if (err) throw err;

    console.log('The count is: ', rows.length);
});
```

#### 实践：博客系统

一般开发过程为：

* 规划router
* 准备model
* 开发view
* 开发controller

### 将博客代码分享到npmjs.org

* 创建包
* 发布包
* 更新包
* 安装包

#### 包(Package)

    包 = 模块集合 + 包的Meta Data(package.json)

包的作用：独立功能封装起来，用于发布、更新、依赖管理和版本控制

#### 创建包

用 `npm init` 创建package.json

```javascript
{
    "name": "mypack",         // 包名
    "version": "0.0.1",       // 版本
    "description": "a demo",  // 描述
    "main": "index.js",       // 接口模块
    "scripts": {              // 命令
        "test": "make test"
    },
    "repository": {           // 代码库
        "type": "git",
        "url": "osdream.github.com/mypack"
    },
    "keywords": [             // 关键词，用于npm search
        "demo",
        "package"
    ],
    "author": "osdream <osdream.song@gmail.com> (http://osdream.com)",        // 作者
    "contributors": [         // 贡献者
        {
            "name": "songao",
            "email": "songao@baidu.com"
        }
    ],
    "dependencies": {         // 依赖
        "express": "~3.4.2"
    },
    "devDependencies": {      // 开发依赖
        "supertest": "0.6.0"
    },
    "license": "BSD"
}
```

#### 包的操作

* 发布包

    * npm adduser
    * npm whoami
    * npm publish

* 更新包

    * 修改版本号
    * npm publish

* 取消发布

    * npm unpublish

#### Semantic Versioning

* 一个标准的版本号必须是X.Y.Z的形式
* X是主版本，Y是副版本，Z是补丁版本
* http://semver.org

```
1.2.3             // 必须正好匹配此版本
&gt;1.2.3            // 必须大于此版本
&gt;=1.2.3           // 必须大于等于此版本
&lt;1.2.3            // ...
&lt;=1.2.3           // ...
1.2.3 - 2.3.4     // 等价于：&gt;=1.2.3 且 &lt;=2.3.4
~1.2.3            // 约等于此版本，等价于：&gt;=1.2.3-0 且 &lt;1.3.0-0
1.2.x             // 所有以1.2开头的版本，例如1.2.0, 1.2.1等。等价于：&gt;=1.2.0-0 且 &lt;1.3.0-0
*                 // 匹配所有版本
""                // 空字符串，等价于*
range1 || range2  // 或
```

### 不足：回调陷阱

Node.js 是基于事件回调的，那么经常出现的一个情况是多个回调会多层嵌套，导致代码不易维护、不好阅读。

解决办法：`async`、`Promise/Deferred`(代表模块：`Q`)等

举例：

```javascript
// async 的使用示例：
async.parallel(
    [
        function(callback){ ... },
        function(callback){ ... }
    ],
    function(err, results) { ... }
);

// Q 的使用示例
function getData1() {
    var deferred = Q.defer();
    senRequest(function(data) {
        if (data.success) {
            deferred.resolve(data);
        }
        else {
            deferred.reject('获取数据失败');
        }
    });
    return deferred.promise;
};

function getData2() {
    // similar to getData1
}

getData1()
    .then(function(data) {
        // getData1返回的数据
        console.log(data);

        return getData2();
    })
    .then(function(data) {
        // getData2返回的数据
        console.log(data);
    })
    .fail(function(err) {
        console.log(err);
    })
```

### 平常我们可以用Node.js做什么

* Web框架：express
* HTML模板引擎：jade
* CSS模板引擎：less
* JavaScript压缩：uglify-js
* MySQL数据库：mysql
* MongoDB ORM：mongoose
* 自动化测试框架：mocha
* 命令行解析工具：commander
* 自动编译工具：grunt
* 基于WebInspector的调试器：node-inspector
* DOM处理：jsdom
* ...

