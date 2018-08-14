# 结构和层叠
## 特殊性

- 0, 1, 0, 0：ID属性值
- 0, 0, 1, 0：类属性值、属性选择（[href]）、伪类
- 0, 0, 0, 1：元素、伪元素
- 0, 0, 0, 0：结合符、通配选择器

## 继承

- 有待补充

# 值与单位
## 值
### content: attr(x)

- 获取元素的 x 属性值 -> 也可以是自定义属性

### content: counter(name)

```css
body {
  couter-reset: name;
}
h3::before {
  counter-increment: name
  content: "Section" counter(name) ": ";
}
```

- 另外支持嵌套

## 单位
### vmax / vmin

- vw 或者 vh 中最大（小）者

### ch

- 字符的宽度，几乎没什么用

### vw

- 视口宽度（包括滚动条）

## 百分数
### 高度百分比

- 在父元素高度为 auto 时无效（父元素高度依赖子元素）
- 在父元素有确定值的时候有效（绝对定位时只要定位祖先元素具有高度即可）
- 一般情况下（常规流、浮动、relative）基于父元素的 content-box ，绝对定位时基于最近定位祖先元素的 padding-box

# 字体和文本
## 字体族
### List

- serif：衬线字体
- sans-serif：非衬线字体
- monospace：等宽字体

### Tip

- 关键字不需要加引号
- 使用字体族的话浏览器会自动选择其中的字体

## 字体/文本属性
### font-weight

- lighter
- bolder

### font-size

- xx-small
- x-small
- larger
- smaller

### font-variant

- small-caps：小写字母变成小号的大写字母

### text-transform

- uppercase
- lowercase
- capitalize：每个单词的首字母大写（中折号再议）

### text-indent

- 常用于隐藏文字，百分比相对于父元素

### word-spacing

- 控制单词间间隔

- normal：+0
- 数字px：空间间隙 + 数字px

### letter-spacing

- 控制字母间的间隔（汉字）

### vertical-align

- 适用于内联元素
- top 和 bottom 的位置就是对应属性的 img 标签的顶部或者底部（或者选中时高亮的顶、底）
- middle 的对齐位置是元素的行内框垂直中点与该行匿名文本的基线上方 0.5ex 处（x 的交叉处）
- 百分比是相对于 line-height
- 作用于表格元素时只有 baseline、top、bottom、middle 有效

### line-height

- 百分比时相对于font-size，数值也是相对于font-size，但是两者稍微有所区别

### text-decoration

- underline
- overline
- line-through：删除线

### text-shadow

- 可以通过多重 shadow ，只用一个圆点实现 loading 动画

### white-space

- 控制空格、换行符、制表符的合并和折行
- normal：合并换行符，合并空白符，文字换行
- nowrap：合并换行符，合并空白符，文字不换行
- pre：保留换行符，保留空白符，文字不换行
- pre-line：保留换行符，保留空白符，文字换行
- pre-wrap：保留换行符，合并空白符，文字换行

### word-wrap

- 长单词和 url 地址自动换行
- normal：只在半角空格和连字符的地方换行
- break-word：将内容在边界内换行

### word-break

- 决定自动换行的处理方法
- normal：中文到边界上的汉字换行，英文从整个单词换行（url）
- break-all：强行截断单词，词内换行
- keep-all：中文遇到空格或者标点符号才会换行，英文从整个单词换行

### direction

- rtl

### unicode-bidi

...

# 基本视觉可视化
## 盒模型

- 元素在文档中会以矩形盒子显示，盒子中包含多个框，这些框由内-外分别是 content, padding, border, margin
- 盒子的宽高默认为 content-box 的宽高，但是可以通过 -ox-sizing 属性修改为 border-box
- 盒子的背景到 border-box 为止，但是可以通过 background-clip 属性修改为其他

## 常规流和包含块
### 常规流

- 元素在其中从左往右、从上向下显示的文本布局，浮动、定位、flex 元素不在其中

### 包含块

- 一个元素的包含块即是其布局上下文，大多数时候，一个元素的布局仅跟其自身及其包含块有关
- 各自的包含块
  - 浮动/常规流/relative：最近的块级祖先
  - fixed：视口
  - absolute：最近的定位祖先

## 块级布局
### 正常流：normal flow（而不是文档流）

- 其中的元素从上往下，从左往右布局

### 替换、非替换元素

- 有待补充

### 垂直外边距合并

- margin之间不存在border或者padding就会合并
- 负margin取绝对值最大者
- margin从父元素中溢出

### 负margin

- 下方负margin会让后面的元素上移

### tip

- 如果某块级元素只有块级子元素（并且border和padding），那父元素auto时高度就是子元素的上下边框之间的距离（border-box），所以margin会溢出（父元素触发BFC就不会溢出）
- 高度百分比只有在父元素的高度不是由此元素直接或间接撑起来时有效（有top: 0; bottom: 0;的父元素就可以使用百分比，即使没有设定高度值）

## 行内布局
### 框

- 匿名文本
  - 空格也是匿名文本的一部分
- em框（字符框）
  - 和font-size大小一致，由字符构成
  - 实际字形可能比em框更高或更矮（一般是更矮）
- 内容区
  - em框串在一起构成的框，或者由元素字形构成的框（字形可能会超出 em框，一般是采用前者）
  - 是 inline 元素的背景区域，或者鼠标选中能变高亮的部分
- 行间距
  - line-height - font-size
  - 只应用于非替换元素
- 行内框
  - 由内容区加上行间距构成
  - 非替换元素刚好是line-height，替换元素刚好是内容区（是margin-box）
- 行框
  - 包含该行的行内框最高点和最低点的最小框（当然左右也是）
- 基线
  - 行内元素
    - 文本的基线
  - 行内替换元素
    - margin-box 下边缘
  - 行内块元素
    - 无内容：margin-box 下边缘
    - 有内容：最后一行内容的基线

### 计算行框高度

- 首先把所有行内框的基线对齐
- 之后根据各元素文档 vertical-align 调整位置
- 子元素的负 marin 是不计算在内的

### 行内格式化

- line-height只能给行内元素，如果给到块级元素的话其实是加在其中的内联- 内容上
- 给块级元素设置line-height，会为其设置最小行框高度

### tip

- 非替换行内元素的border、padding、margin都不会影响这个元素在垂直方向上的摆放（高度）
  - 高度只和vertical-align以及line-height有关
- 替换行内元素的会影响 ↑
- 行框中只有一个替换元素行内框时，如果是baseline对齐，下方会有空格（因为空白幽灵节点），消除方法：
  - 设置替换元素block;
  - vertical-align改成baseline之外的（只要不是基于baseline，应该就不会- 被baseline上的幽灵节点撑大吧）

# 颜色与背景
## 颜色
### hsla（色相、饱和度、明度）

- r=0(360), g=120, b=240, s=0-100%, l=0-100%

### currentColor

- 代表当前 color 的计算值

### tip

- border, shadow, outline 都默认取 color 的值

## 背景
### backgrond-attachment

- fixed
  - 只在元素范围内可见
  - 此时position和size相对于视口
- scroll
  - 会和元素一起滚动

### background-clip

- 平铺以后再裁剪
- text

### background-image

- 多背景

# 浮动与定位
## 浮动
### 清除浮动

- 使用 clear 属性让元素不与浮动元素相邻
- 只能应用于 block 元素

### 闭合浮动

- 使浮动元素的包含块高度增加以包含其内的所有浮动元素
- 触发父元素BFC（Block Format Context）
  - float: left;  
  - overflow: hidden;
  - position: absolute / fixed;
  - display: flow-root;
  - display: table-cell;
  - display: inline-block;
- clear: both;
  - clearfix
  - 给 br 元素设置 clear: both;

### BFC

- 独立的布局单元（不影响其他位置）
- 想象该元素在单独的 iframe 里渲染
- 触发了 BFC 的元素不会跟其他元素产生重叠（主要是浮动元素）
  - 如果是的话会变窄或下移以避开浮动元素
-  触发了 BFC 的元素也会包裹其所有后代元素（定位元素除外）

## 定位
### 定位位置

- 根据包含块来判断
  - 绝对定位时相对于定位祖先的 padding-box
  - 其他情况相对于父元素的 content-box
  - 元素自身的位置是基于 margin-box

# More
## 重绘和回流

- http://www.css88.com/archives/4996

### 重绘（repaint）

### 回流（reflow）

- 当改变一个元素的布局相关属性导致其布局改变时，浏览器会重新计算页面的布局

### 对策

- 通过定位或者 transform 实现动画

## IE hack & vendor prefix

## Media Query





