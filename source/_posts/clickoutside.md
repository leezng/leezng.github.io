---
title: Clickoutside
date: 2017-10-12 22:34:15
tags: JavaScript
---

## 一个常见问题

Question: 编写一个自定义的弹窗，当用户点击页面中弹窗的之外的其他地方，弹窗隐藏。

在 Stack Overflow 上存在类似问题：[How to detect a click outside an element?](https://stackoverflow.com/questions/152975/how-do-i-detect-a-click-outside-an-element)

```js
// stackoverflow 上该问题的推荐答案
$(window).click(function() {
// Hide the menus if visible
});

$('#menucontainer').click(function(event){
  event.stopPropagation();
});
```

<!-- more -->

>通过阻止冒泡，似乎功能一切都是正常，但这并不是一个很好的解决方案，我们忽略了页面其他代码可能需要监听这个事件的可能性: [The Dangers of Stopping Event Propagation](https://css-tricks.com/dangers-stopping-event-propagation/)

在下面这个例子中，我们在同一个页面中多次使用到弹窗(div1, div2)，使用上述的阻止事件冒泡的方法就造成了相互影响。

```html
<a id="a1">show div-1</a>
<a id="a2">show div-2</a>
<div id="div1">div-1</div>
<div id="div2">div-2</div>
```

```js
document.getElementById('a1').addEventListener('click', (e) => {
  e.stopPropagation();
  document.getElementById('div1').style.display = 'block';
});
document.getElementById('a2').addEventListener('click', (e) => {
  e.stopPropagation();
  document.getElementById('div2').style.display = 'block';
});
document.addEventListener('click', () => {
  // Hide div1, div2
});
```

## 一个更好的解决方案

如何解决单页面中存在多个需要执行 Clickoutside 元素的问题？

```js
// A solution for multiple items
$(document).click((event) => { 
  if(!$(event.target).closest('#menucontainer').length) {
    if($('#menucontainer').is(":visible")) {
      $('#menucontainer').hide();
    }
  }        
});
```

这种方案创建了一个封闭的逻辑，而第一种是将菜单的隐藏逻辑代码放置在2个地方。下面是 `Element-UI` 的 `v-clickside` 自定义指令，同样采取了监听 document 上触发的点击事件的方案。

```js
!Vue.prototype.$isServer && on(document, 'mousedown', e => (startClick = e));
!Vue.prototype.$isServer && on(document, 'mouseup', e => {
  nodeList.forEach(node => node[ctx].documentHandler(e, startClick));
});
export default {
  bind(el, binding, vnode) {
    const id = nodeList.push(el) - 1;
    const documentHandler = function(mouseup = {}, mousedown = {}) {
      if (!vnode.context ||
        !mouseup.target ||
        !mousedown.target ||
        el.contains(mouseup.target) ||
        el.contains(mousedown.target) ||
        el === mouseup.target ||
        (vnode.context.popperElm &&
        (vnode.context.popperElm.contains(mouseup.target) ||
        vnode.context.popperElm.contains(mousedown.target)))) return;
      // ...
    };
    // ...
  }
}
```

>If you're ever unsure about what to do, just ask yourself the following question: is it possible that some other code, either now or in the future, might want to know that this event happened?

无论是上述哪一种方案，都存在 *阻止事件传播的问题*，当一个工程越庞大的时候 `DOM` 事件就越不是一个孤立的存在，因为我们难以知道现在或以后在什么地方需要用到事件的传播。以我使用 `element-ui` 的下拉选择器为例，一个月前发现有时点击其他地方却无法关闭下拉选择列表的问题，当时我把它定义为偶发的现象，甚至怀疑是 `element-ui` 的 `bug`，直到今天，我才发现是由于有个地方阻止了 `mousedown` 事件的冒泡导致的。这种类型的 `bug` 往往也是难以跟踪的。

但是项目中使用阻止事件传播有时也是不可避免的，因此，如果在页面上绑定了类似 `clickoutside` 的全局事件，我们应当事先约定明确，形成项目开发规范，避免在开发过程上踩上地雷。
