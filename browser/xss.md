# XSS 攻击

因为同源策略下，浏览器支持页面引用第三方资源，通过 CORS 支持跨域请求，XSS 可以利用这两点攻击页面。

1. 窃取 Cookie 信息。恶意脚本通过`document.cookie`获取，利用 CORS 通过 XMLHttpRequest 将数据发送给恶意服务器。
2. 监听用户行为。恶意脚本使用`addEventListener`可以监听键盘事件，获取用户的输入信息。
3. 修改 DOM 伪造假页面。
4. 在页面内生成浮窗广告。

## XSS 的注入方式

### 存储型 XSS

- hacker 将恶意 JS 代码提交到网站数据库中
- 用户向网站请求包含了恶意 JS 脚本的页面
- 用户浏览页面时，恶意脚本可以获取用户 Cookie 等信息

![img](assets/2ed3d8b93035df3c2bcfcc223dc47914.png)

### 反射型 XSS

恶意 JS 脚本包含在了用户请求中，服务器收到请求会把恶意代码反射给浏览器，但不会存储在服务器中。

比如，页面会显示 URL 参数 xss 的值，如果在 URL 的参数中输入`http://localhost:3000/?xss=<script>alert("已被xss攻击")</script>`，那么这段恶意代码就会被插入页面，反射到用户页面。

### 基于 DOM 的 XSS

这种 XSS 与服务器无关，hacker 将恶意脚本注入页面，修改 HTML 页面内容。

## 如何阻止 XSS 攻击

需要阻止**恶意脚本的注入**和**恶意消息的发送**。

1. 服务器对输入脚本进行过滤或转码
2. 利用 CSP，[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)文档。服务器响应头配置`Content-Security-Policy`，或者在 HTML 文件的`meta`标签中添加，指定浏览器可执行的有效来源，让浏览器只执行白名单域中的脚本文件。
   1. 限制浏览器加载其他域的资源文件
   2. 禁止向第三方域提交数据
   3. 禁止执行内联脚本和未授权的脚本
   4. 提供了上报机制
3. 使用 HttpOnly 属性。在响应头的 set-cookie 里添加 HttpOnly 字段，那么站点的 Cookie 只能在 HTTP 请求中使用，JS 无法读取这段 Cookie
