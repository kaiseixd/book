# Browser
## 网络部分

[1](https://segmentfault.com/a/1190000014348854#articleHeader13)
[2](https://zhuanlan.zhihu.com/p/28946087)

### DNS 域名解析

输入的 url 由于是域名，TCP/IP 使用 IP 地址来唯一确定一台主机到因特网的连接，需要使用 DNS 来完成域名到 IP 地址的映射工作。

流程：

- 缓存
  - 浏览器缓存 -> 本地host -> 路由器缓存：找到即可
- 服务器查询
  - 本地 DNS 服务器：由 DNS 解析器向本地 DNS 服务器发起 UDP 请求，服务器首先查询是否在本地区域文件或者 DNS 缓存中，再根据是否设置转发器决定是向上一级 DNS 服务器还是根 DNS 服务器发送**代理**请求，最后将应答返回（本机到 DNS 服务器的查询过程称为递归：一路查下去中间不返回，直到得到最终结果）
  - DNS 服务器：根服务器收到请求后，并不解析地址，而是将顶级域 DNS 服务器的 IP 返回给本地 DNS 服务器，让它重新发送请求。同理，顶级域 DNS 服务器在查询完缓存后会将二级域名服务器的地址返回。（DNS 服务器到 DNS 服务器的查询过程称为迭代：查到一个可能查到一个可能直到的服务器地址就将此地址返回，重新发送解析请求）

### 建立 TCP 连接

连接步骤：

0. 服务端打开 socket 开始监听连接（被动打开）
  - 进入 LISTEN 状态
1. 客户端向服务端发送一个 SYN 请求报文来主动打开连接
  - 进入 SYN_SEND 状态
  - 序号为随机设定的 A
  - SYN 报文段不带数据，但是会消耗一个序号
2. 服务端收到报文后，如果同意建立连接则返回一个 SYN/ACK 确认报文
  - 进入 SYN_RCVD 状态
  - 序号为随机设定的 B，确认序号为 A+1
3. 客户端收到报文后，返回一个 ACK 确认报文
  - 进入 ESTABLISHED 状态
  - 序号为 A+1，确认序号为 B+1（因为被 SYN 消耗了一个序号所以最终都加了 1）
  - 这个时候一般会携带真正需要传输的数据，如果服务器也收到该数据报文就会同样进入 ESTABLISHED 状态，此时 TCP 连接建立完成

确定超时总共需要：1 + 2 + 4 + 8 + 16 + 32 = 63s

### TLS 握手

HTTPS 在 HTTP 的基础上加了一层 SSL/TLS，增加了身份验证和加密信息。

SSL：Secure Sockets Layer，安全套接层

TLS：Transport Layer Security， 传输层安全协议

过程：

1. TCP 连接建立后，客户端发送一个随机值，需要的协议和加密方式
2. 服务端收到客户端的随机值，自己也产生一个随机值，并根据客户端需求的协议和加密方式来使用对应的方式，发送自己的证书（如果需要验证客户端证书需要说明）
3. 客户端收到服务端的证书并验证（通过证书关系链从根 CA 验证）是否有效，验证通过会再生成一个随机值，通过服务端证书的公钥去加密这个随机值并发送给服务端，如果服务端需要验证客户端证书的话会附带证书
4. 服务端收到加密过的随机值并使用私钥解密获得第三个随机值，这时候两端都拥有了三个随机值，可以通过这三个随机值按照之前约定的加密方式生成密钥，接下来的通信就可以通过该密钥来加密解密

在 TLS 握手阶段，两端使用非对称加密的方式来通信，但是因为非对称加密损耗的性能比对称加密大，所以在正式传输数据时，两端使用对称加密的方式通信。

### 获得页面响应

重定向响应：如果服务器返回了跳转重定向（非缓存重定向），那么浏览器端就会向新的URL地址重新走一遍DNS解析和建立连接。

200：浏览器开始解析页面，进入准备渲染的阶段。

### 页面资源加载

浏览器在解析页面的过程中会去请求页面中诸如js、css、img等外联资源，这些请求也是要建立连接的。

优化：启用长连接，同一客户端 socket 针对同一 socket 后续请求都会复用一个TCP连接进行传输；针对SSL/TLS握手有会话恢复机制，验证通过后，可以直接使用之前的对话密钥，减少握手往返。

## 工作流程

[render process](https://coolshell.cn/wp-content/uploads/2013/05/Render-Process-768x250.jpg)

主流程：

1. 解析（parse、construct）
  - HTML/SVG/XHTML：构建 DOM tree
  - CSS：构建 CSS Rule Tree
  - JS：通过 api 来操作 DOM Tree 和 CSS Rule Tree 
2. 呈现（render）：通过 DOM Tree 和 CSS Rule Tree 来构建 Rendering Tree
  - 包含多个带有视觉属性的矩形
  - Rendering Tree 并不等同于 DOM Tree
  - Rendering Tree 上的每个节点会被附加 CSS Rule
3. 布局（layout/reflow）：计算每个节点的位置
4. 绘制（paint）：遍历呈现树，并由后端层将每个节点绘制出来

阻塞关系：

- js 在执行的时候会阻塞 DOM 解析（但是还是可以下载其他资源）
- 没有 defer async 的 js 标签在下载的时候也会阻塞 DOM 解析
- CSS 的解析不会阻塞 DOM 的解析，但是会阻塞 DOM 的渲染（因为需要合并成 Rendering Tree 之后渲染）
- 但是 CSS 的解析会阻塞 js 执行（而 js 又会阻止其后的 DOM 解析）

### DOM 解析

[构建对象模型](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model?hl=zh-cn)

将文档解析成代表文档结构的节点树（解析树或者语法树）

Bytes → characters → tokens → nodes → object model

### CSS 解析

通过与 DOM 解析相同的步骤获得 CSSOM

由于构建 CSSOM 树是一个十分消耗性能的过程，所以应该尽量保证层级扁平，减少过度层叠，越是具体的 CSS 选择器，执行速度越慢。

### render -> layout -> paint

[render tree construction](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=zh-cn)

1. 构建 Rendering Tree
  - 遍历 DOM 树的每个可见节点
  - 为其找到适配的 CSSOM 规则并应用它们
  - 发射可见节点，连同其内容和样式
2. 布局
  - 从渲染树的根节点开始遍历
  - 输出多个盒模型（精确地捕获每个元素在视口内的确切位置和尺寸：几何信息）
3. 绘制（栅格化）
  - 根据节点的计算样式和几何信息将其转换成屏幕上的实际像素

### 渲染层合并 composite

对页面中 DOM 元素的绘制是在多个层上进行的。在每个层上完成绘制过程之后，浏览器会将所有层按照合理的顺序合并成一个图层，然后显示在屏幕上

## 重绘和回流
### 重绘

有待补充

### 回流

有待补充

### 减少重绘和回流

有待补充

# 性能优化

[1](https://github.com/zwwill/blog/issues/1)

## 去抖
### 防止重复发送Ajax请求

1. 用户点击之后按钮设置 disabled
2. 函数节流
3. abort掉上一个请求
  - XMLHttpRequest.abort() （需要已经 send ）

### 函数节流

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

### 函数防抖

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

## 预加载
### 图片预加载

在需要加载大量图片的网站实现图片提前加载，从而提升用户体验，通常有两种实现

#### CSS 预加载

将图片隐藏在 css 的 background 的 url 属性里面

```css
#preload-01 { background: url(http://damonare.cn/image-01.png) no-repeat -9999px -9999px; }  
#preload-02 { background: url(http://damonare.cn/image-02.png) no-repeat -9999px -9999px; }  
#preload-03 { background: url(http://damonare.cn/image-03.png) no-repeat -9999px -9999px; }
```

#### JS 预加载

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
### 资源预加载

```html
<!-- DNS 预解析 prefetch -->
<!-- 预先完成 DNS 解析 -->
<meta http-equiv="x-dns-prefetch-control" content="on" />
<link rel="dns-prefetch" href="//yuchengkai.cn">

<!-- 预连接 preconnect -->
<!-- 不仅完成 DNS 预解析，同时还将进行 TCP 握手和建立传输层协议 -->
<link rel="preconnect" href="http://example.com">

<!-- 预加载 prefetching -->
<!-- 尽早加载请求的资源并放入浏览器缓存中，不会阻塞 onload 事件（可以将一些不影响首屏但是重要的文件延后加载），并且没有同源策略的限制 -->
<!-- 适合加载字体文件，原本字体文件必须等到 DOM 和 CSS 构建完成之后才开始下载 -->
<link rel="prefetch" href="image.png">

<!-- Subresources -->
<!-- 另一个预获取方式，这种方式指定的预获取资源具有最高的优先级，在所有 prefetch 项之前进行 -->
<link rel="subresource" href="styles.css">

<!-- 预渲染 prerender -->
<!-- 可以预先加载文档的所有资源 -->
<link rel="prerender" href="http://example.com">

<!-- preload -->
<!-- 与 prefetch 的差别就是这个资源必定会被加载 -->
<link rel="preload" href="image.png">
```

## 优化渲染
### 懒执行

该技术可以用于首屏优化，对于某些耗时逻辑并不需要在首屏就使用的，就可以使用懒执行。一般通过定时器或者事件来唤醒。

自动唤醒：使用 setInterval 来轮询，当满足条件时执行回调并 clearInterval 跳出轮询。

手动唤醒：通过监听用户在页面内的 click、hover、scroll 等基本交互行为来触发回调。

### 懒加载

将不关键的资源延后加载，只加载自定义（可视）区域内的东西。

图片懒加载的原理就是暂时不设置图片的`src`属性（设置为占位图），将图片的`url`隐藏起来，比如先写在`data-src`里面，等某些事件触发的时候(比如滚动到底部，点击加载图片)再将图片真实的`url`放进`src`属性里面，从而实现图片的延迟加载

[https://zhuanlan.zhihu.com/p/25455672]

#### 常规方法（通过滚动事件触发）

当页面滚动到图片位置时再替换 src

#### IntersectionObserver API

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

## 文件优化
### 图片

- 不用图片。很多时候会使用到很多修饰类图片，其实这类修饰图片完全可以用 CSS 去代替。
- 对于移动端来说，屏幕宽度就那么点，完全没有必要去加载原图浪费带宽。一般图片都用 CDN 加载，可以计算- 出适配屏幕的宽度，然后去请求相应裁剪好的图片。
- 小图使用 base64 格式
- 将多个图标文件整合到一张图片中（雪碧图）
- 选择正确的图片格式：
  - 对于能够显示 WebP 格式的浏览器尽量使用 WebP 格式。因为 WebP 格式具有更好的图像数据压缩算法，能带来更小的图片体积，而且拥有肉眼识别无差异的图像质量，缺点就是兼容性并不好
  - 小图使用 PNG，其实对于大部分图标这类图片，完全可以使用 SVG 代替
  - 照片使用 JPEG

### 其他文件

- CSS 文件放在 head 中
- 服务端开启文件压缩功能
- 将 script 标签放在 body 底部，因为 JS 文件执行会阻塞渲染。当然也可以把 script 标签放在任意位置然后加上 defer ，表示该文件会并行下载，但是会放到 HTML 解析完成后顺序执行。对于没有任何依赖的 JS 文件可以加上 async ，表示加载和渲染后续文档元素的过程将和 JS 文件的加载与执行并行无序进行。
- 执行 JS 代码过长会卡住渲染，对于需要很多时间计算的代码可以考虑使用 Webworker。Webworker 可以让我们另开一个线程执行脚本而不影响渲染。

### CDN

静态资源尽量使用 CDN 加载，由于浏览器对于单个域名有并发请求上限，可以考虑使用多个 CDN 域名。对于 CDN 加载静态资源需要注意 CDN 域名要与主站不同，否则每次请求都会带上主站的 Cookie。

### Webpack

- 对于 Webpack4，打包项目使用 production 模式，这样会自动开启代码压缩
- 使用 ES6 模块来开启 tree shaking，这个技术可以移除没有使用的代码
- 优化图片，对于小图可以使用 base64 的方式写入文件中
- 按照路由拆分代码，实现按需加载
- 给打包出来的文件名添加哈希，实现浏览器缓存文件

## SEO

1. 合理的title、description、keywords：搜索对着三项的权重逐个减小，title值强调重点即可，重要关键词出现不要超过2次，而且要靠前，不同页面title要有所不同；description把页面内容高度概括，长度合适，不可过分堆砌关键词，不同页面description有所不同；keywords列举出重要关键词即可
2. 语义化的HTML代码，符合W3C规范：语义化代码让搜索引擎容易理解网页
3. 重要内容HTML代码放在最前：搜索引擎抓取HTML顺序是从上到下，有的搜索引擎对抓取长度有限制，保证重要内容一定会被抓取
4. 重要内容不要用js输出：爬虫不会执行js获取内容
5. 少用iframe：搜索引擎不会抓取iframe中的内容
6. 非装饰性图片必须加alt
7. 提高网站速度：网站速度是搜索引擎排序的一个重要指标

### 单页应用的 SEO 优化
#### 单页应用

- 优点
  - 可以模拟原生应用体验，多端兼容，效果好（动画），前端负责显示后端负责数据存储和计算，减轻服务器压力
- 缺点
  - 由于页面的逻辑执行和组装时在浏览器上通过 js 完成和呈现的，页面内容不可抓取
  - 由于使用 # 来实现不跳转更新页面，url 也不可抓取（会无视 # 后面的内容）
  - 书签，需要程序来提供支持
  - 初次加载比较耗时
  - 需要代码实现前进、后退

#### history api

在不刷新页面的情况下，改变浏览器地址栏显示的 URL，用户使用 AJAX 来获取对应 URL 的内容，蜘蛛则直接根据 URL 抓取内容

服务端需要返回如下结构的网页，并把要让搜索引擎收录的内容放在 noscript 中

```html
<html>
  <body>
    <section id='container'></section>
    <noscript>
      ... ...
    </noscript>
  </body>
</html>
```

#### 以高效的方式实现两套页面

当访问为SEO所需页面的时候，数据传输到了SEO 服务器完成渲染和组装然后吐给浏览器和蜘蛛，那么蜘蛛拿到的即是完全可见且融合了SPA的页面——landing页都是蜘蛛可见的，接下去用户的点击都是SPA的页面

#### prerender?

有待补充

phantomjs 预渲染? preorder.io?

#### 服务端渲染? 是不是就是第二种? SSR

有待补充

## 其他
### 缓存

Expires, Cache-Control, Last-Modified, If-Modified-Since, ETag, If-None-Match

### 使用 HTTP/2.0

在 HTTP / 2.0 中引入了多路复用，能够让多个请求使用同一个 TCP 链接，极大的加快了网页的加载速度。并且还支持 Header 压缩，进一步的减少了请求的数据大小。

# 缓存机制

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

分为强缓存和协商缓存两种。

强缓存：Expires、Cache-Control，在缓存期间不需要请求，返回 200。

协商缓存：如果缓存过期了就需要协商缓存，需要请求，如果缓存有效返回 304，否则返回新的资源（200）。

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
  - 对于频繁更新的资源，设置一个尽量长（低于更新间隔）的 max-age 即可
  - 对于不频繁更新资源，可以使用加速资源技术（Steve Sounders）
  - 在资源的 URL 后面（通常是文件名后面）加上版本号，并设置一年及更长的过期时间，当资源更新时只用在高频变动的资源文件（html）里做入口的改动

```
// index.html
<script src='script-8953jief32.js'></script>
```

### 总结

Expires跟max-age是在負責看這個快取是不是「新鮮」，Last-Modified, If-Modified-Since, Etag, If-None-Match是負責詢問這個快取能不能「繼續使用」，而no-cache與no-store則是代表到底要不要使用快取，以及應該如何使用

# 跨域
## 解决方法

- iframe 嵌套，通过 hash 传参
- 服务器代理
- postMessage
- jsonp
- document.domain：需要父域和子域都设置
- CORS

### JSONP

主要利用了`script`的开放策略：通过script标签引入一个js或者是一个其他后缀形式（如php，jsp等）的文件，此文件返回一个js函数的调用。缺点在于只支持get请求而且存在安全问题，可能会导致CSRF，因为请求的数据来源于其他网站，因为恶意攻击者可以利用这段代码进行请求，获取数据，有可能会泄露用户密码等重要信息。

```js
function jsonp (url, callback, success) {
  let script = document.createElement('script')
  script.src = url
  script.async = true
  script.type = 'text/javascript'
  window[callback] = function (data) {
    success && success(data)
  }
  document.body.appendChild(script)
}
```

> 服务端返回一段 js：callback(data)

### CORS

浏览器一旦发现 AJAX 请求跨域，就会自动添加一些附加的头信息，只要服务器也实现了 CORS 接口，就可以实现跨域通信

对于简单请求，浏览器直接发出 CORS 请求，只在头信息中添加一个 Origin 字段，表示请求的源（协议 + 域名 + 端口）

如果不是简单请求就会发送预检请求，包含 Origin, Access-Control-Request-Method, Access-Control-Request-Headers 字段

### CORS vs JSONP

- JSONP的主要优势在于对浏览器的支持较好；虽然目前主流浏览器支持CORS，但IE10以下不支持CORS。
- JSONP只能用于获取资源（即只读，类似于GET请求）；CORS支持所有类型的HTTP请求，功能完善。
- JSONP的错误处理机制并不完善，我们没办法进行错误处理；而CORS可以通过onerror事件监听错误，并且浏览器控制台会看到报错信息，利于排查。

## 同源策略

同源策略限制了从同一个源加载的文档或脚本发起的跨源（处于不同域或端口） HTTP 请求。这是一个用于隔离潜在恶意文件的重要安全机制（防止 CSRF）。

> 也可能是正常发起跨站请求但是结果被浏览器拦截，还有一些浏览器在 http 和 https 之间也算跨域

### 预检请求

对于那些可能对服务器数据产生副作用的 HTTP 请求方法，浏览器必须首先使用 OPTIONS 方法发起预检请求，从而获知服务器是否允许该跨域请求。在预检请求的返回中，服务端也可以通知客户端，是否需要携带身份凭证。

不会触发预检请求请求：

- 简单请求：GET, HEAD, POST
- 安全首部字段：Accept, Accept-Language, Content-Language, Content-Type, DPR, DownLink, Save-Data, Viewport-Width, Width
- 安全 Content-Type：text/plain, multipart/form-data, application/x-www-form-urlencoded
- ...

# 安全
## XSS & CSRF

XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任

### XSS

XSS（跨站脚本攻击），属于代码注入的一种，攻击者通过拼接 javascript 将代码注入网页中，对网页进行篡改，其他用户访问时就会受到这串代码的影响（比如请求外部服务器来暴露用户隐私数据，或者 CSRF）。

#### XSS 防御方法

1. 对一些关键字和特殊字符进行过滤(<, >, script等)，或对用户输入内容进行URL编码(encodeURIComponent);
2. Cookie不要存放用户名和密码，对cookie信息进行MD5等算法散列存放，必要时可以将IP和cookie绑定;
3. 不要使用 innerHTML
4. 在设置Cookie时，加上HttpOnly参数
5. 通过 Content-Security-Policy Header 开启 CSP，规定了浏览器只能够执行特定来源的代码

### CSRF

CSRF（跨站请求伪造），借助用户的 cookie 骗取服务器的信任，让用户发起请求，完成一些违背用户行为的请求（预先构造请求参数，然后让用户提交这个构造好的请求，或者直接伪造身份提交请求），一般通过 XSS 来实现或者直接通过命令行模式伪造请求（通过合法验证即可），或者直接在用户访问黑客编写的网站时提交请求。

> 实际上攻击者什么数据都不能获取，只能悄悄地让用户给服务器发送请求

CSRF防御方法举例:

1. 验证 HTTP Referer 字段：Referer 表示该 HTTP 请求的来源地址，但是由于用户可以在浏览器中设置不提供 Referer，会影响部分用户的正常使用，并且低版本的浏览器中 Referer 可以被伪造。
2. 在请求地址中添加 token 并验证：token 可以在用户登录后产生并放于 session 中，每次请求时把 token 从 session 中拿出，与请求中的 token 对比。问题是每次请求都需要附带 token，并且如果是论坛类的网站，在其中访问到黑客的网站，也会在地址中添加 token。
3. 在 HTTP 头中自定义属性并验证：在 XMLHttpRequest 中添加 X-CSRFToken Header。

# 框架
## MVVM

- Model：数据模型（可以理解为数据库持久化存储）
- View： 界面（前端视图，html）
- ViewModel：作为桥梁负责沟通 View 和 Model

ViewModel 负责数据（view 和 model 同步）和业务的处理，对于前端来说，就是将 DOM 和数据同步更新，并且这个过程是自动的，不需要手动编写特殊用例，这样就能将 Model 和 View 分离，而 View 只负责处理数据显示界面。

Vue 中的 ViewModel 就是整个数据绑定所实现的?

### MVC

- Controller：连接 Model 和 View  之间的业务逻辑控制器
  - M -> C -> V, V -> C -> M

# 设计模式
## 发布订阅模式

发布订阅模式是最常用的一种观察者模式的实现

## 观察者模式

## 单例模式

## 工厂模式
