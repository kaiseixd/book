# Base
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

- 用于自定义数据属性，并且避免与未来可能新增的 html 属性发生冲突，可以通过 `HTMLElement.dataset` 访问

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
