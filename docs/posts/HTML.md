# Base
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

```
 <section>, <article>, <nav>, <header>, <footer>, <aside>, <mark>, <figure>, <figcaption>, <data>, <time>, <output>, <progress>, <meter>, <main>
```

## 标准模式和怪异模式
[https://www.ibm.com/developerworks/cn/web/1310_shatao_quirks/index.html]

目前浏览器的排版引擎有三种模式：怪异模式（Quirks mode）、接近标准模式（Almost standards mode）、以及标准模式（Standards mode）。在怪异模式下，排版会模拟 Navigator 4 与 Internet Explorer 5 的非标准行为（浏览器按自己的理解解析执行代码）。在接近标准模式下，只有少数的怪异行为被实现。在标准模式下，行为即（但愿如此）由 HTML 与 CSS 的规范描述的行为。

产生这两种模式是由于历史原因，在 W3C 标准发布之前，各个浏览器对页面渲染没有统一规范，大家都是按照自己的理解来处理网页。

判断模式：`document.compatMode`

### 决定模式

- Standards Mode：`<!DOCTYPE html>`
- Almost Standards Mode：`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">`
- Quirks Mode：doctype 缺失时

### 差异

- 盒模型：Quirks Mode 使用的是 border-box

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

## script 和 css 的放置位置及原因

- css：head 中，如果在 /body 前的话 dom 树构建完后才会渲染树，会对整个页面重新渲染，而在 head 中就能边构建边渲染
- js：/body 前，js 的加载会阻塞 dom 树的构建
- 具体还是看 how browser works

## 渐进式渲染



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

## 常见的替换元素

- img
- input
- object
- video
- iframe
- canvas
- textarea
