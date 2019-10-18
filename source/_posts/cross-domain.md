---
title: cross-domain跨域解决方案
date: 2019-09-14
tags:
---

跨域是指一个域下的文档或脚本试图去请求另一个域下的资源，这里跨域是广义的。
而广义上的跨域包括资源跳转（链接、重定向等）、资源嵌入（iframe）、脚本请求
我们通常所说的跨域是狭义上的，是由浏览器同源策略限制的一类请求场景。
<!-- more -->

[原链接](https://segmentfault.com/a/1190000011145364?utm_source=tag-newest)
#### 同源策略（SOP - same origin policy）的理解
- 同源就是指“协议 + 域名 + 端口”三个部分要相同，有其中一个不同就意味着不同源，如果缺少了同源策略，就容易受到XSS、CSFR（后面的博客会进行解释）等攻击。
- 同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。
- 同源策略会限制的地方：
1.Cookie、LocalStorage 和 IndexDB 无法读取
2.DOM 和 Js对象无法获得
3.AJAX 请求不能发送 同源策略还应用于使用XMLHttpRequest生成的HTTP请求。（这也就是为什么我们常用的Ajax请求会受到同源策略影响的原因了）这个对象允许客户端JavaScript生成任意的HTTP请求到脚本所属文档的Web服务器，但是不允许脚本和其他Web服务器通信。

#### 跨域解决方案
```bash
    1. jsonp
    2. document.domain + iframe跨域
    3. location.hash + iframe跨域
    4. window.name + iframe跨域
    5. postMessage跨域
    6. 跨域资源共享（CORS）
    7. nginx代理跨域
    8. nodejs中间件代理跨域
    9. WebSocket协议跨域
```

1. jsonp的形式 - 其实是利用script的src的不受同源策略限制特性，执行回调函数
##### 为什么JSONP跨域的script标签请求不受同源策略限制呢？
要理解这个问题的重点就在于：**要执行的脚本是如何判断他的来源的**
脚本的来源取决于脚本所嵌入的资源的来源，比如说访问A主机的当前HTML文件中有一个script标签，这个script标签的src属性请求了一个js脚本，因为这个脚本是由A主机的HTML文件的嵌入的script标签发起请求获取的，因此这个脚本的来源是属于A主机的。

到了这里，问题的答案也就出来了，jsonp的script标签请求回来的资源与当前域是相同的域，因此不受同源策略的影响

- 用例
1.原生实现
```javascript
    <script>
        // 首先动态创建一个script标签
        var cScript = document.createElement('script');

        // 利用其src属性发起请求（其实也就限制了请求的方式get形式），重点：需要传输一个回调函数名，方便后端返回时可以执行这个回调函数
        cScript.src = 'http://www.domain.com:8080/test?name=3213&callback=requestCallback';

        // 我们需要定义这个回调函数，后端会把请求到的数据传入
        function requestCallback(res) {
            alert(JSON.stringify(res));
        }
    </script>
```

2.vue实现
```js
    this.$http.jsonp('http://www.domain.com/test', {
        params: {},
        jsonp: 'requestCallback'
    }).then((res) => {
        console.log(res); 
    })
```

后端处理后会返回，返回时即执行全局函数：
```js
    requestCallback({"status": true, "name": "3213"})
```

2. document.domain + iframe跨域
此方案仅限主域相同，子域不同的跨域应用场景，**属于跨子域的的交互**

<font color=red>实现原理：两个页面都通过js强制设置document.domain为基础主域，就实现了同域。这样两个页面的js就能进行交互</font>

- 用例
1.父窗口：http://www.domain.com/a.html
```html
    <iframe id='iframe' src='http://children.domain.com/b.html'></iframe>

    <script>
        document.domain = 'domain.com';
        var name = '3213';
    </script>
```

2.子窗口: http://children.domain.com/b.html
```javascript
    <script>
        document.domain = 'domain.com';
        alert('父窗口的数据' + window.parent.name);
    </script>
```

**不过直接修改document.domain会存在一些问题，这边记录下**
- 如果页面修改了document.domain，则它包含的iframe，必须也设domain，才能进行交互。就算是同域的页面也必须要设。
- 设置document.doamin，也会影响到其它跟iframe有关的功能。
　　典型的功能如：富文本编辑器（因为是iframe来做富文本编辑器的
- 设置document.doamin，导致ie6下无法向一个iframe提交表单。[解决方案](https://www.cnblogs.com/pigtail/archive/2012/06/06/2528136.html)

3. location.hash + iframe跨域
**实现原理： a想与b跨域相互通信，通过中间页c来实现。 三个页面，不同域之间利用iframe的location.hash传值，相同域之间则直接js访问来通信。**
具体实现：A域：a.html -> B域：b.html -> A域：c.html，a与b不同域只能通过hash值单向通信，b与c也不同域也只能单向通信，但c与a同域，所以c可通过parent.parent访问a页面所有对象。

- 用例
1.a.html(http://www.domain1.com/a.html) **A域**
```html
    <!-- 嵌入b页面 -->
    <iframe id='iframe' src='http://www.domain2.com/b.html' style='display:none;'></iframe>
    <script>
        var iframe = document.getElementById('iframe');

        // 向b.html传hash值，也就是修改iframe.src的值
        setTimeout(function() {
            iframe.src = iframe.src + '#name=3213';
        }, 1000);
        
        // 开放给同域c.html的回调方法
        function onCallback(res) {
            alert('同域的数据' + res);
        }
    </script>
```

2.b.html(http://www.domain2.com/b.html) **B域**
```html
    <!-- 嵌入c页面 -->
    <iframe id="iframe" src="http://www.domain1.com/c.html" style="display:none;"></iframe>
    <script>
        var iframe = document.getElementById('iframe');

        // 监听a.html传来的hash值 - location.hash，再传给c.html。当然也可以做其他操作，传输其他值。
        window.onhashchange = function () {
            iframe.src = iframe.src + location.hash;
        };
    </script>
```

3.c.html(http://www.domain1.com/c.html) **A域**
```html
    <script>
        // 监听b.html传来的hash值
        window.onhashchange = function () {
            // 再通过操作同域a.html的js回调，将结果传回
            window.parent.parent.onCallback('hello: ' + location.hash.replace('#name=', ''));
        };
    </script>
```

4. window.name + iframe跨域
**通过iframe的src属性由外域转向本地域，跨域数据即由iframe的window.name从外域传递到本地域。这个就巧妙地绕过了浏览器的跨域访问限制，但同时它又是安全操作。**
window.name属性的独特之处：name值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值（2MB）。

5. postMessage跨域
postMessage是HTML5 XMLHttpRequest Level 2中的API，且是为数不多可以跨域操作的window属性之一，他可以用于解决以下问题：
```bash
    1. 页面和其打开的新窗口的数据传递
    2. 多窗口之间消息传递
    3. 页面与嵌套的iframe消息传递
    4. 上面三个场景的跨域数据传递
```

- 如何使用：otherWiindow.postMessage(data, origin) - 其他窗口的一个引用，比如iframe的contentWindow属性、执行window.open返回的窗口对象、或者是命名过或数值索引的window.frames。 - data： html5规范支持任意基本类型或可复制的对象，但部分浏览器只支持字符串，所以传参时最好用JSON.stringify()序列化。 - origin： 协议+主机+端口号，也可以设置为"*"，表示可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"。

- 用例：

1.a.html(http://www.domain1.com/a.html)

```html
    <iframe id="iframe" src="http://www.domain2.com/b.html" style="display:none;"></iframe>
    <script>       
        var iframe = document.getElementById('iframe');
        iframe.onload = function() {
            var data = {
                name: '3213'
            };
            // 向domain2传送跨域数据
            iframe.contentWindow.postMessage(JSON.stringify(data), 'http://www.domain2.com');
        };

        // 接受domain2返回数据
        window.addEventListener('message', function(e) {
            alert('来自domain2的数据： ' + e.data);
        }, false);
    </script>
```

2.b.html(http://www.domain2.com/b.html)

```html
    <script>
        // 接收domain1的数据
        window.addEventListener('message', function(e) {
            alert('来自domain1的数据' + e.data);

            var data = JSON.parse(e.data);
            if (data) {
                data.number = 16;

                // 处理后再发回domain1
                window.parent.postMessage(JSON.stringify(data), 'http://www.domain1.com');
            }
        }, false);
    </script>
```

**安全问题**
- 如果不希望从其他网站接收message，不要为message事件添加任何事件侦听器。 
- 希望从其他网站接收message，始终使用origin和source属性验证发件人的身份，验证身份后最好验证接收到的语法。
- 当使用postMessage将数据发送到其他窗口时，始终指定精确的目标origin，而不是*

6. 跨域资源共享（CORS）
普通的跨域请求：服务器设置Access-Control-Allow-Origin即可，前端则不用设置。如果要带cookie请求，那前后端都要设置了
**需注意的是：由于同源策略的限制，所读取的cookie为跨域请求接口所在域的cookie，而非当前页。**
**目前，所有浏览器都支持该功能(IE8+：IE8/9需要使用XDomainRequest对象来支持CORS）)，CORS也已经成为主流的跨域解决方案。**

- 用例(带cookie请求)：
1.前端设置
```bash
    # 原生ajax
    var xhr = new XMLHttpRequest(); // IE8/9需用window.XDomainRequest兼容
    // 前端设置是否带cookie
    xhr.withCredentials = true;

    xhr.open('post', 'http://www.domain2.com:8080/login', true);
    xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    xhr.send('user=admin');

    xhr.onreadystatechange = function() {
        if (xhr.readyState == 4 && xhr.status == 200) {
            alert(xhr.responseText);
        }
    };

    # vue框架 - axios
    axios.defaults.withCredentials = true
```

2.服务端设置
**若后端设置成功，前端浏览器控制台则不会出现跨域报错信息，反之，说明没设成功。**
```bash
var http = require('http');
var server = http.createServer();
var qs = require('querystring');

server.on('request', function(req, res) {
    var postData = '';

    // 数据块接收中
    req.addListener('data', function(chunk) {
        postData += chunk;
    });

    // 数据接收完毕
    req.addListener('end', function() {
        postData = qs.parse(postData);

        // 跨域后台设置
        res.writeHead(200, {
            'Access-Control-Allow-Credentials': 'true',     // 后端允许发送Cookie
            'Access-Control-Allow-Origin': 'http://www.domain1.com',    // 允许访问的域（协议+域名+端口）
            /* 
             * 此处设置的cookie还是domain2的而非domain1，因为后端也不能跨域写cookie(nginx反向代理可以实现)，
             * 但只要domain2中写入一次cookie认证，后面的跨域接口都能从domain2中获取cookie，从而实现所有的接口都能跨域访问
             */
            'Set-Cookie': 'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly'  // HttpOnly的作用是让js无法读取cookie
        });

        res.write(JSON.stringify(postData));
        res.end();
    });
});

server.listen('8080');
console.log('Server is running at port 8080...');
```

7. nginx代理跨域
#####  nginx配置解决iconfont跨域
- 浏览器跨域访问js、css、img等常规静态资源被同源策略许可，但iconfont字体文件(eot|otf|ttf|woff|svg)例外，此时可在nginx的静态资源服务器中加入以下配置。

```bash
    location / {
        add_header Access-Control-Allow-Origin *;
    }
```

#####  nginx反向代理接口跨域
**跨域原理： 同源策略是浏览器的安全策略，不是HTTP协议的一部分。服务器端调用HTTP接口只是使用HTTP协议，不会执行JS脚本，不需要同源策略，也就不存在跨越问题。**

前端则是
**实现思路：通过nginx配置一个代理服务器（域名与domain1相同，端口不同）做跳板机，反向代理访问domain2接口，并且可以顺便修改cookie中domain信息，方便当前域cookie写入，实现跨域登录。**

- nginx具体配置：
```bash
    #proxy服务器
    server {
        listen       81;
        server_name  www.domain1.com;

        location / {
            proxy_pass   http://www.domain2.com:8080;  #反向代理
            proxy_cookie_domain www.domain2.com www.domain1.com; #修改cookie里域名
            index  index.html index.htm;

            # 当用webpack-dev-server等中间件代理接口访问nginx时，此时无浏览器参与，故没有同源限制，下面的跨域配置可不启用
            add_header Access-Control-Allow-Origin http://www.domain1.com;  #当前端只跨域不带cookie时，可为*
            add_header Access-Control-Allow-Credentials true;
        }
    }
```

1.前端设置
```bash
    var xhr = new XMLHttpRequest();

    // 前端开关：浏览器是否读写cookie
    xhr.withCredentials = true;

    // 访问nginx中的代理服务器 - 前端访问代理服务器
    xhr.open('get', 'http://www.domain1.com:81/?user=admin', true);
    xhr.send();
```

2.nodejs后台
```bash
    var http = require('http');
    var server = http.createServer();
    var qs = require('querystring');

    server.on('request', function(req, res) {
        var params = qs.parse(req.url.substring(2));

        // 向前台写cookie
        res.writeHead(200, {
            'Set-Cookie': 'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly'   // HttpOnly:脚本无法读取
        });

        res.write(JSON.stringify(params));
        res.end();
    });

    server.listen('8080');
    console.log('Server is running at port 8080...');
```

8. Nodejs中间件代理跨域
- node中间件实现跨域代理，原理大致与nginx相同，都是通过启一个代理服务器，实现数据的转发，也可以通过设置cookieDomainRewrite参数修改响应头中cookie中域名，实现当前域的cookie写入，方便接口登录认证。

vue框架的跨域
利用node + webpack + webpack-dev-server代理接口跨域。在开发环境下，由于vue渲染服务和接口代理服务都是webpack-dev-server同一个，所以页面与代理接口之间不再跨域，无须设置headers跨域信息了。

```bash
# webpack.config.js部分配置：
module.exports = {
    entry: {},
    module: {},
    ...
    devServer: {
        historyApiFallback: true,
        proxy: [{
            context: '/login',
            target: 'http://www.domain2.com:8080',  // 代理跨域目标接口
            changeOrigin: true,
            secure: false,  // 当代理某些https服务报错时用
            cookieDomainRewrite: 'www.domain1.com'  // 可以为false，表示不修改
        }],
        noInfo: true
    }
}
```

9. WebSocket协议跨域 - 不理解，暂时就写在这里
WebSocket protocol是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很好的实现。
原生WebSocket API使用起来不太方便，我们使用Socket.io，它很好地封装了webSocket接口，提供了更简单、灵活的接口，也对不支持webSocket的浏览器提供了向下兼容。

1.前端代码：
```bash
    <div>user input：<input type="text"></div>
    <script src="https://cdn.bootcss.com/socket.io/2.2.0/socket.io.js"></script>
    <script>
    var socket = io('http://www.domain2.com:8080');

    // 连接成功处理
    socket.on('connect', function() {
        // 监听服务端消息
        socket.on('message', function(msg) {
            console.log('data from server: ---> ' + msg); 
        });

        // 监听服务端关闭
        socket.on('disconnect', function() { 
            console.log('Server socket has closed.'); 
        });
    });

    document.getElementsByTagName('input')[0].onblur = function() {
        socket.send(this.value);
    };
    </script>
```

2.Nodejs socket后台：
```bash
    var http = require('http');
    var socket = require('socket.io');

    // 启http服务
    var server = http.createServer(function(req, res) {
        res.writeHead(200, {
            'Content-type': 'text/html'
        });
        res.end();
    });

    server.listen('8080');
    console.log('Server is running at port 8080...');

    // 监听socket连接
    socket.listen(server).on('connection', function(client) {
        // 接收信息
        client.on('message', function(msg) {
            client.send('hello：' + msg);
            console.log('data from client: ---> ' + msg);
        });

        // 断开处理
        client.on('disconnect', function() {
            console.log('Client socket has closed.'); 
        });
    });
```