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


# 字体
## 字体族
### List

- serif：衬线字体
- sans-serif：非衬线字体
- monospace：等宽字体

### Tip

- 关键字不需要加引号
- 使用字体族的话浏览器会自动选择其中的字体

## 属性
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
- top和bottom的位置就是对应属性的img标签的顶部或者底部（或者选中时高亮的顶、底）
- middle：baseline之上1/2ex
- 百分比是相对于line-height
- 作用于表格元素时只有baseline、top、bottom、middle有效

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

### word-break

- 指定单词折行

### direction

- rtl

### unicode-bidi

...

# 基本视觉可视化
## 块级布局

### 正常流：normal flow（而不是文档流）

- 其中的元素从上往下，从左往右布局

### 替换、非替换元素
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
### 术语

- 匿名文本：空格也是匿名文本的一部分
- em框（字符框）：和font-size大小一致，实际字形可能比em框更高或更矮- （一般是更矮）
- 内容区：em框串在一起构成的框，或者由元素字形构成的框（字形可能会超出- em框，一般是采用前者）
- 行间距：line-height - font-size
  - 只应用于非替换元素
- 行内框：由内容区加上行间距构成
  - 非替换元素刚好是line-height
  - 替换元素刚好是内容区（是margin-box）
- 行框：包含该行的行内框最高点和最低点的最小框（当然左右也是）
  - 块级元素的高度应该是行框累加?

### 计算行框高度

- 子元素的负 marin 是不计算在内的

### 行内格式化

- line-height只能给行内元素，如果给到块级元素的话其实是加在其中的内联- 内容上
- 给块级元素设置line-height，会为其设置最小行框高度

### tip

- 非替换行内元素的border、padding、margin都不会影响这个元素在垂直方向- 上的摆放（高度）
  - 高度只和vertical-align以及line-height有关
- 替换行内元素的会影响 ↑
- 行框中只有一个替换元素行内框时，如果是baseline对齐，下方会有空格（因- 为空白幽灵节点），消除方法：
  - 设置替换元素block;
  - vertical-align改成baseline之外的（只要不是基于baseline，应该就不会- 被baseline上的幽灵节点撑大吧）

# 颜色与背景
## 颜色
### hsla（色相、饱和度、明度）

- r=0(360), g=120, b=240, s=0-100%, l=0-100%

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

- 触发父元素BFC（Block Format Context）
  - position: absolute;
  - display: table-cell;
  - overflow: hidden;
  - float: left;
  - display: flow-root;

## 定位

# More
## 重绘和回流

- http://www.css88.com/archives/4996





