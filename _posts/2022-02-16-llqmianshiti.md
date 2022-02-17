---
layout: post
title: 前端面试题之浏览器
categories: 面试题
description: 面试题,浏览器
keywords: 面试题, 浏览器, HTTP, HTTPS, 页面加载
---

### 1、浏览器本地存储

有三种：

1. cookie
2. sessionStorage
3. localStorage

#### 1.1 cookie

Cookie是最早被提出来的本地存储方式，在此之前，服务端是无法判断网络中的两个请求是否是同一用户发起的，为解决这个问题，Cookie就出现了。Cookie的大小只有4kb，它是一种纯文本文件，每次发起HTTP请求都会携带Cookie。

cookie的特性：

- cookie一旦创建成功，名称无法修改
- cookie是不能跨域名的，这样就可以阻止其他网站非法获取cookie
- 每个域名下面的cookie不能超过20个，每个不超过4kb
- cookie如果被劫持，那么就可以在不需要解密的情况下直接登录
- cookie在请求一个新的页面的时候都会被发送

如果需要跨域名共享cookie：

1. Nginx反向代理
2. 在一个站点登录后，往其他网站写cookie，服务端的session存储到一个节点，，cookie存储sessionId

cookie的使用场景：

- 存储用户登录信息，session+cookie
- 可以用来统计页面的点击次数

#### 1.2 sessionStorage

SessionStorage和LocalStorage都是在HTML5才提出来的存储方案，SessionStorage 主要用于临时保存同一窗口(或标签页)的数据，刷新页面时不会删除，关闭窗口或标签页之后将会删除这些数据。

由于SessionStorage具有时效性，所以可以用来存储一些网站的游客登录的信息，还有临时的浏览记录的信息。当关闭网站之后，这些信息也就随之消除了。

使用：

```js
// 保存数据到 sessionStorage
sessionStorage.setItem('key', 'value');

// 从 sessionStorage 获取数据
let data = sessionStorage.getItem('key');

// 从 sessionStorage 删除保存的数据
sessionStorage.removeItem('key');

// 从 sessionStorage 删除所有保存的数据
sessionStorage.clear();

// 获取某个索引的Key
sessionStorage.key(index)
```

#### 1.3 localStorage

LocalStorage是HTML5新引入的特性，由于有的时候我们存储的信息较大，Cookie就不能满足我们的需求，这时候LocalStorage就派上用场了。

localStorage的优点：

- 可以存大小5MB的内容
- 是浏览器本地持久化存储，并不会随着页面的关闭而消失
- 存储在本地，不会像cookie一样每次请求都被携带

localStorage的缺点：

- IE8以下不支持
- 如果浏览器设置为隐私模式，无法读取
- 受同源策略的限制，不同的端口、协议、主机地址不能访问

用法：

```js
// 保存数据到 localStorage
localStorage.setItem('key', 'value');

// 从 localStorage 获取数据
let data = localStorage.getItem('key');

// 从 localStorage 删除保存的数据
localStorage.removeItem('key');

// 从 localStorage 删除所有保存的数据
localStorage.clear();

// 获取某个索引的Key
localStorage.key(index)
```

**SessionStorage与LocalStorage对比：**

- SessionStorage和LocalStorage都在**本地进行数据存储**
- SessionStorage也有同源策略的限制，但是SessionStorage有一条更加严格的限制，SessionStorage**只有在同一浏览器的同一窗口下才能够共享**
- LocalStorage和SessionStorage**都不能被爬虫爬取**

### 2、浏览器同源策略

同源策略限制了从同一个源加载的文档或脚本如何与另一个源的资源进行交互。这是浏览器的一个用于隔离潜在恶意文件的重要的安全机制。同源指的是：**协议**、**端口号**、**域名**必须一致。

同源策略限制：

- 当前域下的 js 脚本不能够访问其他域下的 cookie、localStorage 和 indexDB。
- 当前域下的 js 脚本不能够操作访问操作其他域下的 DOM。
- 当前域下 ajax 无法发送跨域请求。

同源政策的目的主要是为了保证用户的信息安全，它只是对 js 脚本的一种限制，并不是对浏览器的限制，对于一般的 img、或者script 脚本请求都不会有跨域的限制，这是因为这些操作都不会通过响应结果来进行可能出现安全问题的操作。

### 3、如何解决跨域

#### 3.1 CORS 跨域资源共享

CORS: 跨域资源共享，它使用额外的 HTTP 头来告诉浏览器  让运行在一个 origin (domain)上的Web应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域HTTP 请求。

#### 3.2 JSONP

**jsonp**的原理就是利用`<script>`标签没有跨域限制，通过`<script>`标签src属性，发送带有callback参数的GET请求，服务端将接口返回数据拼凑到callback函数中，返回给浏览器，浏览器解析执行，从而前端拿到callback函数返回的数据。

```js
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';
    // 传参一个回调函数名给后端，方便后端返回时执行这个在前端定义的回调函数
    script.src = 'http://www.domain2.com:8080/login?user=admin&callback=handleCallback';
    document.head.appendChild(script);
    // 回调执行函数
    function handleCallback(res) {
        alert(JSON.stringify(res));
    }
 </script>
```

**JSONP的缺点：**

- 具有局限性， 仅支持get方法
- 不安全，可能会遭受XSS攻击

#### 3.3 postMessage

postMessage是HTML5 XMLHttpRequest Level 2中的API，且是为数不多可以跨域操作的window属性之一，它可用于解决以下方面的问题：

- 页面和其打开的新窗口的数据传递
- 多窗口之间消息传递
- 页面与嵌套的iframe消息传递
- 上面三个场景的跨域数据传递

用法：postMessage(data,origin)方法接受两个参数：

- **data**： html5规范支持任意基本类型或可复制的对象，但部分浏览器只支持字符串，所以传参时最好用JSON.stringify()序列化。
- **origin**： 协议+主机+端口号，也可以设置为"*"，表示可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"。

#### 3.4 Nginx代理

nginx代理跨域，服务器端调用HTTP接口只是使用HTTP协议，不需要同源策略，也就不存在跨域问题。实质和CORS跨域原理一样，通过配置文件设置请求响应头Access-Control-Allow-Origin…等字段。

#### 3.5 node中间件代理

原理大致与nginx相同，都是通过启一个代理服务器，实现数据的转发，也可以通过设置cookieDomainRewrite参数修改响应头中cookie中域名，实现当前域的cookie写入，方便接口登录认证。

#### 3.6 document.domain + iframe跨域

此方案仅限主域相同，子域不同的跨域应用场景。实现原理：两个页面都通过js强制设置document.domain为基础主域，就实现了同域。

```js
// 父页面
<script>
    document.domain = 'domain.com';
    var user = 'admin';
</script>


// 子窗口
<script>
    document.domain = 'domain.com';
    // 获取父窗口中变量
    console.log('get js data from parent ---> ' + window.parent.user);
</script>
```

#### 3.7 location.hash + iframe跨域

a欲与b跨域相互通信，通过中间页c来实现。 三个页面，不同域之间利用iframe的location.hash传值，相同域之间直接js访问来通信。

#### 3.8 window.name + iframe跨域

window.name属性的独特之处：name值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值（2MB）。

通过iframe的src属性由外域转向本地域，跨域数据即由iframe的window.name从外域传递到本地域。这个就巧妙地绕过了浏览器的跨域访问限制，但同时它又是安全操作。

#### 3.9 WebSocket协议跨域

WebSocket protocol是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很好的实现。

原生WebSocket API使用起来不太方便，我们使用Socket.io，它很好地封装了webSocket接口，提供了更简单、灵活的接口，也对不支持webSocket的浏览器提供了向下兼容。

### 4、正向代理和反向代理

正向代理：对真实服务器隐藏真实客户端，因为是客户的代理服务器向真实服务器发送的请求，真实服务器无法知道客户端

反向代理：对客户端隐藏真实服务器，客户端请求网址，后台通过负载均衡来匹配不同的服务器，对于客户端来说不知道是哪个服务器进行的响应。

### 5、浏览器缓存

浏览器缓存过程：

1. 浏览器发送请求，服务端响应200，浏览器下载资源文件，并缓存文件与响应头
2. 下次加载时，会先比较强缓存的过期时间`例如：cache-control:max-age=1000`，如果没有超时就命中强缓存，直接从本地读取
3. 如果资源过期或强缓存失效之后就发送请求，浏览器在请求头中携带相应的缓存`tag`来向服务器发请求来决定是否使用缓存，这就是协商缓存。 `ETag：文件唯一标识/指纹`
4. Etag 值一致则没有修改，命中协商缓存，返回 304，如果不一致则直接返回新的资源文件带上新的Etag值并返回200
5. 如果服务器收到的请求没有Etag值，则将 If-Modified-Since 和被请求文件的最后修改时间做比对，一致则命中协商缓存，返回 304；不一致则返回新的 last-modified 和文件并返回 200。ps：服务器传一个时间戳给浏览器进行缓存，浏览器再次请求服务器的时候带着这个时间戳，字段为`if-modified-since`

这里经常会遇到的一个问题，项目发布新版本之后，打开网站看到的还是旧的内容，就是文件内容被浏览器进行缓存了，所以很多网站都在资源后面加了版本号，这样做的目的是：每次升级了 JS 或 CSS 文件后，为了防止浏览器进行缓存，强制改变版本号，客户端浏览器就会重新下载新的 JS 或 CSS 文件 ，以保证用户能够及时获得网站的最新更新。

使用浏览器缓存的优点：

1. 减少了服务器的负担，提高了网站的性能
2. 加快了客户端网页的加载速度
3. 减少了多余的网络数据传输

点击刷新按钮或者按 F5、按 Ctrl+F5 （强制刷新）、地址栏回车有什么区别：

- 点击刷新按钮或者按 F5：浏览器直接对本地的缓存文件过期，但是会带上If-Modifed-Since，If-None-Match，这就意味着服务器会对文件检查新鲜度，返回结果可能是 304，也有可能是 200。

- 用户按 Ctrl+F5（强制刷新）：浏览器不仅会对本地文件过期，而且不会带上 If-Modifed-Since，If-None-Match，相当于之前从来没有请求过，返回结果是 200。

- 地址栏回车： 浏览器发起请求，按照正常流程，本地检查是否过期，然后服务器检查新鲜度，最后返回内容。

#### 5.1 强缓存

使用强缓存策略时，如果缓存资源有效，则直接使用缓存资源，不必再向服务器发起请求。

强缓存策略可以通过两种方式来设置，分别是 http 头信息中的 Expires 属性和 Cache-Control 属性。

- 服务器通过在响应头中添加 Expires 属性，来指定资源的过期时间。在过期时间以内，该资源可以被缓存使用，不必再向服务器发送请求。这个时间是一个绝对时间，它是服务器的时间，因此可能存在这样的问题，就是客户端的时间和服务器端的时间不一致，或者用户可以对客户端时间进行修改的情况，这样就可能会影响缓存命中的结果。
- Expires 是 http1.0 中的方式，因为它的一些缺点，在 HTTP 1.1 中提出了一个新的头部属性就是 Cache-Control 属性，它提供了对资源的缓存的更精确的控制。
- 一般来说只需要设置其中一种方式就可以实现强缓存策略，当两种方式一起使用时，Cache-Control 的优先级要高于 Expires。

Cache-Control的值：

- `public`：设置了该字段值的资源表示可以被任何对象（包括：发送请求的客户端、代理服务器等等）缓存。这个字段值不常用，一般还是使用max-age=来精确控制；
- `private`：设置了该字段值的资源只能被用户浏览器缓存，不允许任何代理服务器缓存。在实际开发当中，对于一些含有用户信息的HTML，通常都要设置这个字段值，避免代理服务器(CDN)缓存；
- `no-cache`：设置了该字段需要先和服务端确认返回的资源是否发生了变化，如果资源未发生变化，则直接使用缓存好的资源；
- `no-store`：设置了该字段表示禁止任何缓存，每次都会向服务端发起新的请求，拉取最新的资源；
- `max-age=`：设置缓存的最大有效期，单位为秒；
- `s-maxage=`：优先级高于max-age=，仅适用于共享缓存(CDN)，优先级高于max-age或者Expires头；
- `max-stale[=]`：设置了该字段表明客户端愿意接收已经过期的资源，但是不能超过给定的时间限制。

**no-cache和no-store**的区别

- no-cache 是指先要和服务器确认是否有资源更新，在进行判断。也就是说没有强缓存，但是会有协商缓存；
- no-store 是指不使用任何缓存，每次请求都直接从服务器获取资源。

#### 5.2 协商缓存

强缓存过期了，如果设置了协商缓存就走协商缓存。

协商缓存的条件：

- `max-age=xxx` 过期了
- 值为`no-store`

使用协商缓存策略时，会先向服务器发送一个请求，如果资源没有发生修改，则返回一个 304 状态，让浏览器使用本地的缓存副本。如果资源发生了修改，则返回修改后的资源。

协商缓存也可以通过两种方式来设置，分别是 http 头信息中的**Etag** 和**Last-Modified**属性。

这样的缓存tag分为两种: `Last-Modified：最后修改时间` 和 `ETag：文件唯一标识/指纹`。这两者各有优劣，并不存在谁对谁有绝对的优势，跟上面强缓存的两个 tag 不一样。

设置`Last-Modified`，服务器传一个时间戳给浏览器进行缓存，浏览器再次请求服务器的时候带着这个时间戳，字段为`if-modified-since`，服务器进行对比，如果没有改变，则返回`304`，去缓存里面寻找。

`Last-modified`不是很靠谱，主要有以下三种情况。
1. 如果文件没有发生变化，但是进行操作了，比如修改后又恢复，这样修改时间就变了
2. 一秒内改变多次，`Last-modified`是无法监控到秒内修改的.
3. CDN分发，可能时间是不一致的。

这种情况可以使用`ETag`来做缓存处理。

`Etag` 文件指纹必定用md5，所以使用`crypto`这个模块

```js
import crypto from 'crypto';
let Etag = crypto.createHash('md5').update(fs.readFileSync(filePath)).digest('base64');
res.setHeader("Etag", Etag);
// 获取客户端请求携带的Etag指纹
let ifNoneMatch = req.headers['if-none-match'];
// 如果存在且相等，说明有缓存
if(ifNoneMatch && ifNoneMatch === Etag){
  return true;
}
```

### 6、浏览器缓存的位置

主要有以下几种：

1. 页面缓存：浏览器的本地存储，主要由`Cookie`,`WebStorage`,`IndexedDB`,其中`WebStorage`又可以分为`localStorage`和`sessionStorage`。
2. memory cache：它的效率最快，**但是内存缓存虽然读取高效，可是缓存持续性很短，会随着进程的释放而释放。**一旦我们关闭 Tab 页面，内存中的缓存也就被释放了。
3. disk cache：Disk Cache 也就是存储在硬盘中的缓存，读取速度慢点，但是什么都能存储到磁盘中，比之 Memory Cache 胜在容量和存储时效性上。
4. service worker：***Service Worker 运行在 JavaScript 主线程之外，虽然由于脱离了浏览器窗体无法直接访问 DOM，但是它可以完成离线缓存、消息推送、网络代理等功能。***

#### 6.1 Service Worker

Service Worker 是一个浏览器中的**进程**而不是浏览器内核下的线程，因此它在被注册安装之后，能够被在多个页面中使用，也不会因为页面的关闭而被销毁。***它可以完成离线缓存、消息推送、网络代理等功能***。它可以让我们自由控制**缓存哪些文件、如何匹配缓存、如何读取缓存，并且缓存是持续性的**。因此，Service Worker 很适合被用与多个页面需要使用的复杂数据的计算——购买一次，全家“收益”。

**但是不管是从 Memory Cache 中还是从网络请求中获取的数据，浏览器都会显示是从 Service Worker 中获取的内容。**

**Service Worker 只能被使用在 https 或者本地的 localhost 环境下**。

如果当前使用的浏览器支持 Service Worker ，则在 window.navigator 下会存在 serviceWorker 对象，我们可以使用这个对象的 register 方法来注册一个 Service Worker。

Service Worker 在使用的过程中存在大量的 Promise ，Service Worker 的注册方法返回的也是一个 Promise 。

```js
// index.js
if ('serviceWorker' in window.navigator) {
  navigator.serviceWorker.register('./sw.js', { scope: './' })
    .then(function (reg) {
      console.log('success', reg);
    })
    .catch(function (err) {
      console.log('fail', err);
    });
}
```

register 方法接受两个参数，第一个是 service worker 文件的路径，请注意：**这个文件路径是相对于 Origin \**，而不是当前 JS 文件的目录的；第二个参数是 Serivce Worker 的配置项，可选填，其中比较重要的是\** scope 属性**。按照文档上描述，它是 Service Worker 控制的内容的子目录，这个属性所表示的路径不能在 service worker 文件的路径之上，默认是 Serivce Worker 文件所在的目录

大概就这样，具体的操作可以看这篇文章`https://juejin.cn/post/6844903613270081543`

### 7、输入一个URL回车后发生了什么

详细的过程：

1. 输入的过程中，浏览器会实时捕捉输入的内容，会检查有没有非法字符，有的话进行转义
2. 检查缓存，检查缓存的过程
   1. 如果是HTTPS的话，可能会优先检查`Service Worker`
   2. 如果没有就找内存缓存`Memory Cache`
   3. 没有接着找硬盘缓存`Disk Cache`，强缓存和协商缓存都属于硬盘缓存
   4. 如果三种都没有找到并且是HTTP2的话，还可能会查找推送缓存`Push Cache`
3. 进行URL解析：把`协议、域名、端口、路径`提取出来，根据解析出来的URL进行DNS解析
4. DNS解析：会先查找浏览器缓存，然后查找本地hosts文件，然后查找本地DNS解析器缓存，然后查找本地DNS服务器，如果还没有就去DNS根域名服务器查询，这样就可以拿到URL对应的IP地址了
5. TCP连接：有了IP就可以进行TCP连接了，进行三次握手，确认双方都有接受以及发送文件的能力
6. HTTPS的TLS握手：检查秘钥信息是否一致
7. 建立连接之后，浏览器会把构建好的请求行、cookie等数据添加到请求头中，发送给服务器，服务器解析，如果没有就404，如果有就返回数据
8. 服务端返回数据，前端准备进行读取解析
9. 发送完成进行四次挥手，确认双方都已经发送完毕且没有其他问题了，就关闭链接。
10. 前端开始看响应的数据内容，该怎么处理怎么处理，然后渲染DOM树、CSS树，对页面进行重绘重排直到重新渲染完成。

