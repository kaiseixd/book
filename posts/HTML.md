# Base
## 标准模式和怪异模式
[https://www.ibm.com/developerworks/cn/web/1310_shatao_quirks/index.html]

目前浏览器的排版引擎有三种模式：怪异模式（Quirks mode）、接近标准模式（Almost standards mode）、以及标准模式（Standards mode）。在怪异模式下，排版会模拟 Navigator 4 与 Internet Explorer 5 的非标准行为（浏览器按自己的理解解析执行代码）。在接近标准模式下，只有少数的怪异行为被实现。在标准模式下，行为即（但愿如此）由 HTML 与 CSS 的规范描述的行为。

产生这两种模式是由于历史原因，在 W3C 标准发布之前，各个浏览器对页面渲染没有统一规范，大家都是按照自己的理解来处理网页。

判断模式：`document.compatMode`

### DOCTYPE

 Document Type Declaration（DTD，文档类型声明），用来决定浏览器的渲染模式

### 决定模式

- Standards Mode：`<!DOCTYPE html>`、html4.01严格型、xhtml1.0严格型
- Almost Standards Mode：`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">`
- Quirks Mode：doctype 缺失时

### 历史原因

在发布 IE5（HTML4.0）的时候，为了兼容旧网页，而让使用新标准的页面可以在头部加上 doctype 来激活新标准模式 ，不添加则依旧使用旧模式

### 差异

- 盒模型：Quirks Mode 使用的是 border-box
- 行内元素垂直对齐：Quirks Mode 对齐至底部（正常应该至基线）
- 接近标准模式：只是表单元格内部图片的布局采用和 Quirks Mode 相同的方式被处理

## XHTML

W3C 在 2000 年底发布了 XHTML 1.0，这是一种在 HTML 4.0 基础上优化和改进的的新语言，目的是基于 XML 应用

- eXtensible
- 由于过于严苛，并且必须使用 XML，不被各浏览器厂商接受，最后组建了 WHATWG 并制定了 HTML5 将其替代

## head

[https://gethead.info/#meta]

### meta

```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
<!-- 指定当前页的内容安全策略：允许加载何处的资源 -->
<meta http-equiv="Content-Security-Policy" content="default-src 'self'">
<!-- Stores a cookie on the client web browser for identification purposes -->
<meta http-equiv="set-cookie" content="name=value; expires=date; path=url">
<meta http-equiv="refresh" content="5">
<meta name="description" content="A description of the page">
<!-- Control the behavior of search engine crawling and indexing -->
<meta name="robots" content="index,follow"><!-- All Search Engines -->
<meta name="googlebot" content="index,follow"><!-- Google Specific -->
```

#### viewport

定义一个虚拟的视口，用于解决手机上页面的布局问题，做到网页在移动端屏幕中适配

```html
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />

// width    设置viewport宽度，为一个正整数，或字符串‘device-width’
// device-width  设备宽度
// height   设置viewport高度，一般设置了宽度，会自动解析出高度，可以不用设置
// initial-scale    默认缩放比例（初始缩放比例），为一个数字，可以带小数
// minimum-scale    允许用户最小缩放比例，为一个数字，可以带小数
// maximum-scale    允许用户最大缩放比例，为一个数字，可以带小数
// user-scalable    是否允许手动缩放
```

### link

指定了外部资源与当前文档的关系

- crossorigin：指定在加载相关图片时是否必须使用 CORS. 启用 CORS 的图片 可以在 <canvas> 元素中使用, 并避免其被污染
- href：指定被链接资源的 URL
- integrity：用浏览器获取资源文件的哈希值（?）
- referrerpolicy ：...
- media：指定了连接资源提供的媒体类型，它的值一定得是一个媒体查询
- sizes：定义了包含相应资源的可视化媒体中的icons的大小
- rel：表明了链接的文档对于当前文档的关系，由空格分隔
- type：定义链接的内容的类型

## script 和 css 的放置位置及原因

- css：head 中，如果在 /body 前的话 dom 树构建完后才会渲染树，会对整个页面重新渲染，而在 head 中就能边构建边渲染
- js：/body 前，js 的加载会阻塞 dom 树的构建
- 具体还是看 how browser works

## HTML 实体

- HTML 本身的预留字符不能以明文形式出现，需要使用 HTML 实体来转义，用符号序列代替原有字符
  - `<`：`&lt;`
  - `>`：`&gt;`
  - `空格`：`&nbsp;`
  - `&`：`&amp;`
  - `""`：`&quot;`

## ASCII

- 由 1 个字节（8 位二进制）表示，但实际上只涉及了 128 个字符

## Unicode

- 是一种标准字符集，对世界上大部分的文字系统（语言、符号）进行了整理、编码。每一个符号都有对应的独一无二的编码
- 由 8 个字节（64 位）表示 1 个字符
- 基本汉字范围：4E00-9FA5
- 表示
  - `&#x6211;` html
    - 或者以十进制的方式：`&#25105;`
  - `\6211` css
  - `\u6211` js

### UTF-8

- 是一种变长编码，可根据算法转换减少字节占用
- 汉字通常是 3 个字节，也有 4 个字节
- 编码规则
  - 对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的
  - 对于n字节的符号（n>1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10，剩下的没有提及的二进制位，全部为这个符号的unicode 二进制码

```js
'喵'.charCodeAt().toString(16) // 55b5 -> Unicode
'喵'.charCodeAt().toString(2) // 0 + 101010110110101
// 1110xxxx 10xxxxxx 10xxxxxx -> 1110 0101 1001 0110 1011 0101
parseInt('111001011001011010110101', 2).toString(16) // e596b5
```

## 常见的图片格式

- jpg：最常见，有损，不支持透明，色彩还原度比较好
- png：无损，支持透明
- gif：有损，仅支持半透明，支持动画
- bmp：无损，不支持透明，图片格式较大

## data-*

用于自定义数据属性，并且避免与未来可能新增的 html 属性发生冲突，或者存储不需要显示的额外信息

### 访问

- js：可以通过 `HTMLElement.dataset` 访问
- css：使用 attr() 属性来获得 data- 中的内容；还可以作为属性选择器来根据 data- 改变样式

```
article::before {
  content: attr(data-parent);
}

article[data-columns='3'] {
  width: 400px;
}
```

## 常见的替换元素

- img
- input
- object
- video
- iframe
- canvas
- textarea

# Advanced
## 渐进式渲染

加快首屏加载速度，先渲染首页的一部分

## MIME Type

- 互联网媒体类型，用于分类互联网上的传输内容
- 种类
  - type/subtype
  - image/png
  - video/webm
  - text/*

## 浏览器内核

- IE：Trident
- Firefox：Gecko
- Chrome：Webkit -> Blink
- safari：Webkit
- opera：Presto -> Webkit -> Blink

## BOM 头

- Byte Order Mark
- （只）在utf-8编码文件头部，占用三个字节，用来标示该文件属于utf-8编码

# HTML5
## 新特性
### 通信

- Web Sockets
- Server-sent events
- WebRTC

### 离线和存储

- localStorage
- indexedDB

### 多媒体

- audio, video
- camera

### 图像

- canvas

### 性能 & 集成

- history
- Web Workers
- 拖放
- 全屏 API

### 设备访问

- camera
- 获取地理位置

### 语义化

- 用户体验：例如title、alt用于解释名词或解释图片信息、label标签的活用
- 有利于SEO：和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息
- 方便其他设备解析
- 便于团队开发和维护

```
 <section>, <article>, <nav>, <header>, <footer>, <aside>, <mark>, <figure>, <figcaption>, <data>, <time>, <output>, <progress>, <meter>, <main>
```

## Canvas
### 渲染上下文

dom.getContext('2d')

### 绘制图形

- fillRect(x, y, width, height)
- strokeRect(x, y, width, height)
- clearRect(x, y, width, height)
- beginPath()
- moveTo(x, y)
- lineTo(x, y)
- closePath()
- stroke()
- fill()
- arc()

### 绘制文本

- fillText(text, x, y [, maxWidth])
- strokeText(text, x, y [, maxWidth])

### 动画

- window.requestAnimationFrame()

### 性能优化

- 不要重复渲染一个大背景，不变的背景可以通过 div 显示
- 一个元素变化很多的话，可以分离出来，单独做一个 canvas
- 离屏 canvas（用于预加载）
- 不同尺寸的图不要用缩放，而是分别加载各大小图片与离屏 canvas

## WebGL

- WebGL (Web图形库) 是一种JavaScript API，用于在任何兼容的Web浏览器中呈现交互式3D和2D图形（在canvas中使用）

## SVG

- 可缩放矢量图形（Scalable Vector Graphics，SVG)，是一种用来描述二维矢量图形的XML 标记语言。 简单地说，SVG面向图形，HTML面向文本
- 与Flash类似