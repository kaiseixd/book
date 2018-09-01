# Web 性能优化
## 防止重复发送Ajax请求

1. 用户点击之后按钮设置 disabled
2. 函数节流
3. abort掉上一个请求
  - XMLHttpRequest.abort() （需要已经 send ）

## 函数节流

Throttling enforces a maximum number of times a function can be called over time.

限制了函数可以在一段时间内可以最多调用多少次（就是每 delay time 调用一次）。

```js
function throttle (fn, delay, ...rest) {
  let last = 0
  return ()  => {
    let cur = +new Date()
    if (cur - start > delay) {
      fn(rest)
      last = cur
    }
  }
}
```

## 函数防抖

让一个函数无法在很短的时间间隔内连续调用，只有当上一次函数执行后过了你规定的时间间隔，才能进行下一次该函数的调用。

```js
function debounce (fn, delay, ...rest) {
  let timer = null
  return () => {
    clearTimeout(timer)
    timer = setTimeout(() => fn(rest), delay)
  }
}
```

## 懒加载

图片懒加载的原理就是暂时不设置图片的`src`属性，而是将图片的`url`隐藏起来，比如先写在`data-src`里面，等某些事件触发的时候(比如滚动到底部，点击加载图片)再将图片真实的`url`放进`src`属性里面，从而实现图片的延迟加载

[https://zhuanlan.zhihu.com/p/25455672]

### 常规方法（通过滚动事件触发）

当页面滚动到图片位置时再替换 src

### IntersectionObserver API

可以自动"观察"元素是否可见

```js
// 当元素可见或者消失时会自动在 io 数组中添加或删除，并执行回调
var io = new IntersectionObserver(callback, option)

// 通过 observe 方法添加需要观察的项
io.observe(item)

// else
io.unobserve(element)
io.disconnect()
```

## 预加载

在需要加载大量图片的网站实现图片提前加载，从而提升用户体验，通常有两种实现

### CSS 预加载

将图片隐藏在 css 的 background 的 url 属性里面

```css
#preload-01 { background: url(http://damonare.cn/image-01.png) no-repeat -9999px -9999px; }  
#preload-02 { background: url(http://damonare.cn/image-02.png) no-repeat -9999px -9999px; }  
#preload-03 { background: url(http://damonare.cn/image-03.png) no-repeat -9999px -9999px; }
```

### JS 预加载

通过 Image 对象设置实例对象的 src 属性

```js
  function preloadImg (url) {
      var img = new Image();
      img.src = url;
      if (img.complete) {
          //接下来可以使用图片了
          //do something here
      } else {
          img.onload = function () {
              //接下来可以使用图片了
              //do something here
          };
      }
  }
```

## SEO

1. 合理的title、description、keywords：搜索对着三项的权重逐个减小，title值强调重点即可，重要关键词出现不要超过2次，而且要靠前，不同页面title要有所不同；description把页面内容高度概括，长度合适，不可过分堆砌关键词，不同页面description有所不同；keywords列举出重要关键词即可
2. 语义化的HTML代码，符合W3C规范：语义化代码让搜索引擎容易理解网页
3. 重要内容HTML代码放在最前：搜索引擎抓取HTML顺序是从上到下，有的搜索引擎对抓取长度有限制，保证重要内容一定会被抓取
4. 重要内容不要用js输出：爬虫不会执行js获取内容
5. 少用iframe：搜索引擎不会抓取iframe中的内容
6. 非装饰性图片必须加alt
7. 提高网站速度：网站速度是搜索引擎排序的一个重要指标

# Web 缓存机制

缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载

[http://www.alloyteam.com/2012/03/web-cache-1-web-cache-overview/]

## 缓存类型
### 数据库缓存

主要是将查询后的数据放到内存中进行缓存

### 服务器缓存

#### 代理服务器缓存

浏览器先向中间服务器发起 Web 请求，经过处理后（比如权限验证，缓存匹配等），再将请求转发到源服务器

#### CDN 缓存（网关缓存、反向代理缓存）

一般是由网站管理员自己部署

浏览器先向 CDN 网关发起 Web 请求，网关服务器后面对应着一台或多台负载均衡源服务器，会根据它们的负载请求，动态将请求转发到合适的源服务器上

从浏览器角度来看，整个 CDN 就是一个源服务器

### 浏览器端缓存

根据一套与服务器约定的规则进行工作，在同一个会话过程中会检查一次并确定缓存的副本足够新。如果你浏览过程中，比如前进或后退，访问到同一个图片，这些图片可以从浏览器缓存中调出而即时显现

### Web 应用层缓存

指从代码层面上，通过代码逻辑和缓存策略，实现对数据，页面，图片等资源的缓存，可以根据实际情况选择将数据存在文件系统或者内存中，减少数据库查询或者读写瓶颈，提高响应效率

## 浏览器缓存机制

[https://blog.techbridge.cc/2017/06/17/cache-introduction/]

### Expires

HTTP 响应头字段，用于告诉浏览器在过期时间前可以直接从浏览器缓存中获取数据（HTTP 1.0）

```
Expires: Wed, 21 Oct 2017 07:28:00 GMT
```

### Cache-Control

与 Expires 类似，都是指明当前资源的有效期，但是功能会更细致一些，优先级高于 Expires（HTTP 1.1）

### Last-Modified / If-Modified-Since

配合 Cache-Control 一起使用

`Last-Modified Header`：Server 在返回响应的时候，可以加上，表示资源上一次更改的时间

`If-Modified-Since`：当请求的资源快过期（max-age）时，如果 response 里有 Last-Modified ，再次发送请求的时候可以带上 If-Modified-Since ；之后由服务器比对两者的时间，若最后修改时间更新则说明资源有改动，则响应整个资源内容（200）；更旧则继续使用缓存，并响应 304

```
/* Response */
Last-Modified: 2017-01-01 13:00:00
Cache-Control: max-age=31536000

/* Request */
GET /logo.png
If-Modified-Since: 2017-01-01 13:00:00
```

### ETag / If-None-Match

ETag 的出现解决了几个 Last-Modified 的问题（HTTP 1.1）:

- Last-Modified标注的最后修改只能精确到秒级，如果某些文件在1秒钟以内，被修改多次的话，它将不能准确标注文件的修改时间
- 如果某些文件会被定期生成，但有时内容并没有任何变化（或因为其他操作只修改了编辑时间，没有修改内容）， Last-Modified 却会改变，导致文件没法使用缓存
- 有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形

Etag 是服务器自动生成或者由开发者生成的对应资源在服务器端的唯一标识符，表示文档内容是否改动（类似 hash?），Server 在 response 里带上 Etag ，优先级会高于 Last-Modified

而浏览器则会根据 Etag 在再次发送请求的时候带上 If-None-Match 询问是否有新资料（传的就是 Etag），没有的话返回 304

### 私有缓存

表示该响应是专用于某单个用户的，中间人不能缓存此响应，该响应只能应用于浏览器私有缓存中

```
Cache-Control: private
```

### Vary 响应头

对于后续（缓存之后）的请求头，只有当前的请求和原始（缓存）的请求头跟缓存的响应头里的 Vary 都匹配，才能使用缓存

```
Vary: User-Agent
```

### case

1. 不缓存：
  - 指定 max-age=0 或者 Cache-Control: no-store（每次访问都请求资源）

2. 如果是网站首页的情况，既要用到缓存又要让更新的时候能马上反应给用户：
  - 设置 max-age=0 以及添加上述的 Last-Modified 或 Etag 头应该就可以，另一种方案是使用 Cache-Control: no-cache （与 no-store 有点不同，每次访问都需要发送请求确定是否有新资源，没更新依旧返回 304）

3. 如果需要不发送请求（或者尽量少发），但依旧能使用缓存，并在更新时立马获取新资源：
  - 对于频繁更新的资源，设置一个尽量长的的 max-age 即可
  - 对于不频繁更新资源，可以使用加速资源技术（Steve Sounders）
  - 在资源的 URL 后面（通常是文件名后面）加上版本号，并设置一年及更长的过期时间，当资源更新时只用在高频变动的资源文件（html）里做入口的改动

```
// index.html
<script src='script-8953jief32.js'></script>
```

### 总结

Expires跟max-age是在負責看這個快取是不是「新鮮」，Last-Modified, If-Modified-Since, Etag, If-None-Match是負責詢問這個快取能不能「繼續使用」，而no-cache與no-store則是代表到底要不要使用快取，以及應該如何使用

# HTTP
## 状态码

200 & OK: 请求成功；

204 & No Content: 请求处理成功，但没有资源可以返回；

206 & Partial Content: 对资源某一部分进行请求(比如对于只加载了一般的图片剩余部分的请求)；

301 & Move Permanently: 永久性重定向；

302 & Found： 临时性重定向；

303 & See Other: 请求资源存在另一个URI，应使用get方法请求；

304 & Not Modified: 服务器判断本地缓存未更新，可以直接使用本地的缓存；

307 & Temporary Redirect: 临时重定向；

400 & Bad Request: 请求报文存在语法错误；

401 & Unauthorized: 请求需要通过HTTP认证；

403 & Forbidden: 请求资源被服务器拒绝，访问权限的问题；

404 & Not Found: 服务器上没有请求的资源；

500 & Internal Server Error: 服务器执行请求时出现错误；

502 & Bad Gateway: 错误的网关；

503 & Service Unavailable: 服务器超载或正在维护，无法处理请求；

504 & Gateway timeout: 网关超时；

## Cookie & Session & Token

[https://www.zhihu.com/question/19786827/answer/28752144]

### Cookie

存于浏览器中，4kb，每次请求都会携带 cookie ，不安全（document.cookie）

HttpOnly

设置：Set-Cookie

### Session

用于保存会话数据，存于服务端中

第一次请求时，服务端会创建一个 session 对象，并将键（cookie）返回给浏览器，下一次访问的时候携带 cookie 就可以找到对应的 session（值）

### Token


## GET & POST

- GET后退按钮/刷新无害，POST数据会被重新提交（浏览器应该告知用户数据会被重新提交）。
- GET书签可收藏，POST为书签不可收藏。
- GET能被缓存，POST不能缓存 。
- GET编码类型application/x-www-form-url，POST编码类型encodedapplication/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码。
- GET历史参数保留在浏览器历史中。POST参数不会保存在浏览器历史中。
- GET对数据长度有限制，当发送数据时，GET 方法向 URL 添加数据；URL 的长度是受限制的（URL 的最大长度是 2048 个字符）。POST无限制。
- GET只允许 ASCII 字符。POST没有限制。也允许二进制数据。
- 与 POST 相比，GET 的安全性较差，因为所发送的数据是 URL 的一部分。在发送密码或其他敏感信息时绝不要使用 GET ！POST 比 GET 更安全，因为参数不会被保存在浏览器历史或 web 服务器日志中。
- GET的数据在 URL 中对所有人都是可见的。POST的数据不会显示在 URL 中。

# 跨域
## jsonp

主要利用了`script`的开放策略：通过script标签引入一个js或者是一个其他后缀形式（如php，jsp等）的文件，此文件返回一个js函数的调用。缺点在于只支持get请求而且存在安全问题，可能会导致CSRF，因为请求的数据来源于其他网站，因为恶意攻击者可以利用这段代码进行请求，获取数据，有可能会泄露用户密码等重要信息。

## 同源策略

Cookie，iframe，AJAX同源

## CORS

关键在于服务器，如果服务器实现了CORS跨域的接口，那么就可以使用ajax(请求路径为绝对路径)进行跨域请求。CORS请求分为两种，一种是简单请求，一种是非简单请求。简单请求是指请求方法在`HEAD`,`GET`,`POST`三者之间并且请求头信息局限在

- Accept
- Accept-Language
- Content-Language


- Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

非简单请求请求头：

**（1）Access-Control-Request-Method**

该字段是必须的，用来列出浏览器的CORS请求会用到哪些HTTP方法

**（2）Access-Control-Request-Headers**

该字段是一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段

执行简单请求的时候，浏览器会在请求头信息增加`origin`字段，服务器据此来判断请求域名是否在许可范围之内，来决定是否返回`Access-Control-Allow-Origin`字段。响应头有以下几种：

**（1）Access-Control-Allow-Origin**

该字段是必须的。它的值要么是请求时`Origin`字段的值，要么是一个`*`，表示接受任意域名的请求。

**（2）Access-Control-Allow-Credentials**

该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。设为`true`，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值也只能设为`true`，如果服务器不要浏览器发送Cookie，删除该字段即可。

**（3）Access-Control-Expose-Headers**

该字段可选。CORS请求时，`XMLHttpRequest`对象的`getResponseHeader()`方法只能拿到6个基本字段：`Cache-Control`、`Content-Language`、`Content-Type`、`Expires`、`Last-Modified`、`Pragma`。如果想拿到其他字段，就必须在`Access-Control-Expose-Headers`里面指定。

  **(4)Access-Control-Max-Age**

`Access-Control-Max-Age` 首部字段指明了预检请求的响应的有效时间。

  **(5)Access-Control-Allow-Methods**

`Access-Control-Allow-Methods` 首部字段用于预检请求的响应。其指明了实际请求所允许使用的 HTTP 方法。

  **(6)Access-Control-Allow-Headers**

`Access-Control-Allow-Headers`首部字段用于预检请求的响应。其指明了实际请求中允许携带的首部字段。

## 其他方法

`document.domin`(IE6,7配合iframe;IE6,7发送POST跨域请求),html5的`postMessage`,`window.name`等

# 安全
## XSS & CSRF

XSS（跨站脚本攻击），属于代码注入的一种，攻击者通过拼接 javascript 将代码注入网页中（，其他用户访问时就会受到这串代码的影响（比如请求外部服务器）

CSRF（跨站请求伪造），冒充用户发起请求（不知情），完成一些违背用户行为的请求（预先构造请求参数，然后让用户提交这个构造好的请求，或者直接伪造身份提交请求），一般通过 XSS 来实现或者直接通过命令行模式伪造请求（通过合法验证即可）

XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任

XSS 防御方法举例:

1. 对一些关键字和特殊字符进行过滤(<>,,script等)，或对用户输入内容进行URL编码(encodeURIComponent);
2. Cookie不要存放用户名和密码，对cookie信息进行MD5等算法散列存放，必要时可以将IP和cookie绑定;
3. 不要使用 innerHTML
4. 在设置Cookie时，加上HttpOnly参数

CSRF防御方法举例:

1. 检查Referer字段（IE或低版本的浏览器中，Referer参数可以被伪造）
2. 添加校验token

# 兼容
## loading...