title: SPA单页面框架网站如何进行SEO优化

categories: 前端

---

#### 前言
> 由于目前网站PC端和移动端分别使用的angular1和vue2做的框架，这两者还都是页面应用，然后百度等搜索引擎无法抓取到页面内容（搜索引擎不执行js，所以拿不到数据）。

#### 分析
> 一开始有考虑过使用对应框架的服务端渲染来实现，听说过vue和angular2框架都可以在服务器端渲染，没深入了解过（只能等以后有空了解下，再来更新这篇文章）。然后为了短期尽快的做到渲染，就考虑到了PhantomJS在服务器段读取网站内容解析后，返回给搜索引擎。

#### 配置PhantomJS环境（本文以centos 7为例）

1. 进入[下载页面](http://phantomjs.org/download.html);选择Linux 64-bit，右键复制下载地址。

2. 然后在centos 7执行下载：
```bash
wget https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-2.1.1-linux-x86_64.tar.bz2
```
3. 解压缩下载的压缩包：
```bash
tar -xjvf phantomjs-2.1.1-linux-x86_64.tar.bz2

```

4. 添加执行文件到环境目录：
```bash
cp phantomjs-2.1.1-linux-x86_64/bin/phantomjs /usr/local/bin
```

5. 测试是否配置成功,如果进入phantomjs 运行环境，则phantomjs配置成功：
```bash
phantomjs
```

#### 配置渲染服务器

> 配置渲染服务器的代码完全参考[这篇文章](http://gold.xitu.io/entry/56fa5c20c4c971005bcdfd0f),拷贝下这篇文章的代码是为了避免文章丢失，引起的文章表述的不完整。

1. 新建spider.js，用来渲染url对应的页面内容
```js
/*global phantom*/
"use strict";

// 单个资源等待时间，避免资源加载后还需要加载其他资源
var resourceWait = 500;
var resourceWaitTimer;

// 最大等待时间
var maxWait = 5000;
var maxWaitTimer;

// 资源计数
var resourceCount = 0;

// PhantomJS WebPage模块
var page = require('webpage').create();

// NodeJS 系统模块
var system = require('system');

// 从CLI中获取第二个参数为目标URL
var url = system.args[1];

// 设置PhantomJS视窗大小
page.viewportSize = {
    width: 1280,
    height: 1014
};

// 获取镜像
var capture = function(errCode){

    // 外部通过stdout获取页面内容
    console.log(page.content);

    // 清除计时器
    clearTimeout(maxWaitTimer);

    // 任务完成，正常退出
    phantom.exit(errCode);

};

// 资源请求并计数
page.onResourceRequested = function(req){
    resourceCount++;
    clearTimeout(resourceWaitTimer);
};

// 资源加载完毕
page.onResourceReceived = function (res) {

    // chunk模式的HTTP回包，会多次触发resourceReceived事件，需要判断资源是否已经end
    if (res.stage !== 'end'){
        return;
    }

    resourceCount--;

    if (resourceCount === 0){

        // 当页面中全部资源都加载完毕后，截取当前渲染出来的html
        // 由于onResourceReceived在资源加载完毕就立即被调用了，我们需要给一些时间让JS跑解析任务
        // 这里默认预留500毫秒
        resourceWaitTimer = setTimeout(capture, resourceWait);

    }
};

// 资源加载超时
page.onResourceTimeout = function(req){
    resouceCount--;
};

// 资源加载失败
page.onResourceError = function(err){
    resourceCount--;
};

// 打开页面
page.open(url, function (status) {

    if (status !== 'success') {

        phantom.exit(1);

    } else {

        // 当改页面的初始html返回成功后，开启定时器
        // 当到达最大时间（默认5秒）的时候，截取那一时刻渲染出来的html
        maxWaitTimer = setTimeout(function(){

            capture(2);

        }, maxWait);

    }

});
```

2. 执行以下命令测试
```bash
phantomjs spider.js  'http://wj.qq.com/'
```

3. 配置渲染服务器app.js
```js
// ExpressJS调用方式
var express = require('express');
var app = express();

// 引入NodeJS的子进程模块
var child_process = require('child_process');

app.get('/', function(req, res){

    // 完整URL
    var url = req.protocol + '://'+ req.hostname + req.originalUrl;

    // 预渲染后的页面字符串容器
    var content = '';

    // 开启一个phantomjs子进程
    var phantom = child_process.spawn('phantomjs', ['spider.js', url]);

    // 设置stdout字符编码
    phantom.stdout.setEncoding('utf8');

    // 监听phantomjs的stdout，并拼接起来
    phantom.stdout.on('data', function(data){
        content += data.toString();
    });

    // 监听子进程退出事件
    phantom.on('exit', function(code){
        switch (code){
            case 1:
                console.log('加载失败');
                res.send('加载失败');
                break;
            case 2:
                console.log('加载超时: '+ url);
                res.send(content);
                break;
            default:
                res.send(content);
                break;
        }
    });

});
```

4. 至此渲染服务器构建完成。


#### 路由指向

1. 由于网站的嵌套路由全部是在index、admin、center下的子路由，所以并没有采用上面这篇教程来配置Nginx。（主要是也不怎么会）

2. 在express 新增以下路由即可,把所有指向这三个目录的请求全部回复index.html这个文件。
```js
app.get(/index|admin|center\//, function(req, res, next) {
    res.sendFile(path.join(__dirname, './public', 'index.html'));
});
```

3. 遇到个坑，就是sendFile的时候。需要使用path.join来出来文件路径。


#### 参考文章

1. [用 PhantomJS 来给 AJAX 站点做 SEO 优化](http://gold.xitu.io/entry/56fa5c20c4c971005bcdfd0f);

2. [res.sendFile absolute path](http://stackoverflow.com/questions/25463423/res-sendfile-absolute-path);

3. [HTML5 History 模式](https://router.vuejs.org/zh-cn/essentials/history-mode.html)

