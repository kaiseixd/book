# Base
## CSS
### 让一个元素可被 focus

- 给元素设置 tabindex 属性（值为负数的话不会被 tab 到）

### 在伪元素中插入换行符

```css
div::after {
  content: '\a';
  white-space: pre;
}
```

### 垂直居中

```html
<style>
  .parent {
    width: 200px;
    height: 200px;
    background: aqua;
  }
  .child {
    width: 100px;
    height: 100px;
    background: deeppink;
  }
</style>
<div class="parent">
  <div class="child">Lorem ipsum dolor sit amet.</div>
</div>
```

```css
/* vertical-align: middle */
.parent {
  line-height: 200px;
  text-align: center;
}
.child {
  display: inline-block;
  vertical-align: middle;
  line-height: normal;
  text-align: left;
}
/* vertical-align: middle || version 2 */
.parent {
  text-align: center;
  font-size: 0;
}
.parent::after {
  content: '';
  display: inline-block;
  height: 50%;
}
.child {
  display: inline-block;
  vertical-align: middle;
  text-align: left;
  font-size: 16px;
}
/* display: table-cell */
.parent {
  display: table-cell;
  vertical-align: middle;
}
.child {
  margin: auto;
}
/* position: absolute */
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  margin: auto;
}
/* transform: translate(-50%, -50%) */
/* 可用负 margin 代替  */
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
/* display: flex */
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

### 消除标签之间的空白符

- 父元素设置 font-size: 0;
- margin 负值
- 父元素设置 letter-spacing: -3px;
- 父元素设置 word-spacing: -6px;

### 表格布局中各层的层次

  * cells > rows > row groups > columns > column groups > table

### 表格布局中边框合并的原则

- border: hidden 优先级最高，border: none 最低
- 看粗细
- 看样式（double solid dashed）
- 看来源
- 取左方或上方来源的边框

