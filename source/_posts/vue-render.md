---
title: Vue Render
date: 2017-10-12 22:34:15
tags: JavaScript
---

## 从 Vue template 开始

```html
<template>
  <div>
    <el-input v-if="condition === 1"><i></i></el-input>
    <el-radio v-else-if="condition === 2"><i></i></el-radio>
    <el-select v-else-if="condition === 3"><i></i></el-select>
    <el-checkbox v-else><i></i></el-checkbox>
  </div>
</template>
```

在这个渲染表单的场景中，不断使用 `v-if` 语句造成了代码的冗长，如果每一个 `v-if` 中还带有相同的代码片段，template 就会更显得臃肿，下面使用 `render` 函数进行重构：

```js
h('el-' + formType,
  [
    h('i')
  ]
)
// Render 的具体参数设置可见 Vue 官网
```

>将 `h` 作为 `createElement` 的别名是 Vue 生态系统中的一个通用惯例

## 进一步使用 Render

```js
// Vue 官方例子
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```

在上述例子中，使用了 `map` 函数实现 `v-for` 功能，即只需保证结果是一个符合参数规范的数组。但是，如果我们需要实现的是不同条件下渲染不同的子元素，而不是统一渲染相同的元素，下例使用了自执行函数保证返回虚拟 dom，任何其他有意义的返回值都是不被允许的。

```js
  h('el-' + formType,
    [
      (() => {
        if (condition1) {
          // 条件1 符合时
          h('i')
        } else {
          h('span')
        }
      })()
    ]
  )
```

## JSX

>如果你写了很多 `render` 函数，可能会觉得痛苦

```js
h('div',
  {
    'class': {
      classNameA: true
    }
  },
  'hello'
)
```

在上面的例子中，只是为了渲染 `<div class="classNameA">hello</div>`，很显然使用 `Render` 函数是比较麻烦的，因此，我们通过引入 `JSX` 解决这个问题(相关Babel插件配置见官方)

```jsx
render (h) {
  // h 必须保证是 createElement
  return <div class="classNameA">hello</div>
}
```

通过 `JSX` 使得 `Render` 回到接近 `template` 的语法，可以降低很多书写要求，同时，我们可以将二者进行结合，简单的 `html` 模板直接使用 `JSX` 语法进行实现，同时在 `Vue Render` 语句中嵌入 `js` 代码，以适配复杂的场景：

```jsx
render (h) {
  if (condition === 'in') {
    return <div class="icon-bar">
      {
        iconLists.map(icon => {
          return h('el-button', {
            props: {
              type: icon.action === 'remove' ? 'danger' : 'primary',
              size: 'mini'
            },
            on: {
              click: () => {
              // click
              }
            }
          }, [
            <i class={ icon.className }></i>
          ])
        })
      }
    </div>
  }
}
```
