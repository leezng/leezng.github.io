---
title: 左右固定宽度，中间自适应布局
date: 2017-07-07 21:20:00
tags: CSS
---

## 利用 float 实现

```css
.box-1 {
  overflow: hidden;
}
.box-1 .left {
  float: left;
  width: 100px;
}
.box-1 .center {
  margin: 0 100px;
}
.box-1 .right {
  float: right;
  width: 100px;
}
```

<!-- more -->

```html
<div class="box box-1">
    <div class="left"></div>
    <div class="right"></div>
    <div class="center"></div>
</div>
```

## 利用 float 实现 中间优先渲染 (圣杯布局)

```css
.box-2 {
  overflow: hidden;
}
.box-2 .center {
  float: left;
  width: 100%;
}
.box-2 .left {
  float: left;
  width: 100px;
  margin-left: -100%;
}
.box-2 .right {
  float: left;
  width: 100px;
  margin-left: -100px;
}
```

```html
<div class="box box-2">
    <div class="center"></div>
    <div class="left"></div>
    <div class="right"></div>
</div>
```

## 利用 absolute 实现

附：absolute做中间优先渲染也是很方便的

```css
.box-3 {
  position: relative;
  overflow: hidden;
}
.box-3 .left {
  position: absolute;
  top: 0;
  left: 0;
  width: 100px;
}
.box-3 .center {
  margin: 0 100px;
}
.box-3 .right {
  position: absolute;
  top: 0;
  right: 0;
  width: 100px;
}
```

```html
<div class="box box-3">
    <div class="left"></div>
    <div class="center"></div>
    <div class="right"></div>
</div>
```

## 利用 table 实现

```css
.box-4 {
  display: table;
  width: 100%;
  table-layout: fixed
}
.box-4 div { 
  display: table-cell;
}
.box-4 .left { 
  width: 100px;
}
.box-4 .right { 
  width: 100px; 
}
```

```html
<div class="box box-4">
    <div class="left"></div>
    <div class="center"></div>
    <div class="right"></div>
</div>
```

## 利用 flex 实现

```css
.box-5 {
  display: flex;
}
.box-5 .left {
  width: 100px;
}
.box-5 .center {
  flex: 1;
}
.box-5 .right {
  width: 100px;
}
```

```html
<div class="box box-5">
    <div class="left"></div>
    <div class="center"></div>
    <div class="right"></div>
</div>
```

```html
<style type="text/css">
    body { font-family: "Microsoft Yahei"; }
    pre { font-family: "Consolas"; }
    .box { margin: 30px 0; }
    .box .left, .box .right { background-color: lightblue; height: 100px; }
    .box .center { background-color: orange; height: 100px; }

    .box-1 { overflow: hidden; }
    .box-1 .left { width: 100px; float: left; }
    .box-1 .center{ margin: 0 100px; }
    .box-1 .right{ width: 100px; float: right; }

    .box-2 { overflow: hidden; }
    .box-2 .center { float: left; width: 100%; }
    .box-2 .left { float: left; width: 100px; margin-left: -100%; }
    .box-2 .right { float: left; width: 100px; margin-left: -100px; }

    .box-3 { position: relative; }
    .box-3 .left { position: absolute; top:0; left: 0; width: 100px; }
    .box-3 .center { margin: 0 100px; }
    .box-3 .right { position: absolute; top:0; right:0; width: 100px; }

    .box-4 { display: table; width: 100%; table-layout: fixed }
    .box-4 div { display: table-cell; }
    .box-4 .left { width: 100px; }
    .box-4 .right { width: 100px; }

    .box-5 { display: flex; }
    .box-5 .left { width: 100px; }
    .box-5 .center { flex: 1; }
    .box-5 .right { width: 100px; }
</style>
```