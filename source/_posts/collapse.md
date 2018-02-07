---
title: Collapse折叠面板
date: 2018-01-13 17:25:37
tags: [JavaScript, CSS]
---

## JS

JS 的实现方式并无太大难处，以下仅是一个简单例子。

```html
<div id="container">
  <div class="collapse-item">
    <label>A</label>
    <div class="content" style="display: none;">AAA</div>
  </div>
  <div class="collapse-item">
    <label>B</label>
    <div class="content" style="display: none;">BBB</div>
  </div>
</div>
```

<!-- more -->

```js
let domArr = document.querySelectorAll('#container .collapse-item')
let arr = Array.prototype.slice.call(domArr)
arr.forEach(item => {
  item.addEventListener('click', () => {
    toggle(arr, item)
  })
})
function toggle (list, activeItem) {
  list.forEach(item => (item.querySelector('.content').style.display = item === activeItem ? 'block' : 'none'))
}
```

## CSS

### input[type = "radio"]

原理：`name` 值相同的单选按钮，在同一时间只能有一个被选中。`label` 使用 `for` 属性关联 `input` 隐藏的单选按钮，此时点击 `label` 将选中被绑定的 `input`，再利用 `:checked` 伪类即可实现显示或隐藏的控制效果。

```html
<div class="item">
  <label for="item1">Collapsible Group Item #1</label>
  <input type="radio" name="item" id="item1">
  <div class="detail">
    Anim pariatur cliche reprehenderit, enim eiusmod high life accusamus terry richardson ad squid. 3 wolf moon officia aute, non cupidatat skateboard dolor brunch. Food truck quinoa nesciunt laborum eiusmod. Brunch 3 wolf moon tempor, sunt aliqua put a bird on it squid single-origin coffee nulla assumenda shoreditch et.
  </div>
</div>
<div class="item">
  <label for="item2">Collapsible Group Item #2</label>
  <input type="radio" name="item" id="item2">
  <div class="detail">...</div>
</div>
```

```css
.item label + input[type = "radio"] {
  display: none; 
}
.item .detail {
  height: 0;
  overflow: hidden;
  transition: height .3s ease;
}
.item input[type = "radio"]:checked + .detail {
  height: 100px;
}
```

### :target

原理：通过 `:target` 选中当前 html 锚点对应的目标元素。

```html
<div class="menuItem">
  <h2><a href="#item1">Label</a></h2>
  <p id="item1">Anim pariatur cliche reprehenderit, enim eiusmod high life accusamus terry richardson ad squid. 3 wolf moon officia aute, non cupidatat skateboard dolor brunch.</p>
</div>
<div class="menuItem">...</div>
<div class="menuItem">...</div>
```

```css
.menuItem p {
  height: 0;
  overflow: hidden;
  transition: height .3s ease;
}
.menuItem p:target {
  height: 120px;
}
```

