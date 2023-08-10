# 同源策略

## 这是一个安全策略

在协议、域名、端口相同时，浏览器才可以对它们相互访问资源和操作 DOM，这是基于安全考虑制定的规则。这个策略的影响如下：

1. DOM 层面，限制来自不同源的 JS 脚本对当前页面中 DOM 对象的读写。
2. 数据层面，限制不同源的站点读取当前站点的 Cookie、IndexDB、LocalStorage 等。
3. 网络层面，限制通过 XMLHttpRequest 将当前站点的数据发送给不同源的站点。

## 为了便利性的权衡

让不同源绝对隔离，会带来很多不便，浏览器做出了一些安全让步。

1. 页面可以嵌入第三方资源。hacker 可以将恶意脚本插入 HTML 文件，可以通过 XSS 将 Cookie、IndexDB、LocalStorage 发送给自己的服务器。所以，**引用第三方资源时要使用 CSP 策略。**
2. 跨域资源共享，**跨域请求需要采用 CORS** 传输数据。[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)
3. 跨文档消息机制，通过`window.postMessage`可以和不同源的 DOM 通信。**操作 DOM 使用跨文档机制。**

某些 HTML 标签是不受同源策略限制的：`<script>, <img>, <iframe>, <link>`，HTML5 还增加了：`<video>, <audio>`，这些标签可以跨域加载资源，这些带有`src`属性的标签，每次加载时都是由浏览器发起了一次`GET`请求，但是限制了 JavaScript 的权限，无法读、写返回的内容。
