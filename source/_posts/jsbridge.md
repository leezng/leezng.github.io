---
title: JSBridge
date: 2018-04-08 17:04:55
tags: JavaScript
---

## 一些前端通信场景

- 组件通信 eventEmitter, ...
- 页面之间的通信 postMessage, ...
- 前后端之间的通信 xhr, websocket, ...
- web 与客户端等通信

## 一些前端通信方案

- CMD命令
- 本地开启web服务器
- JSBridge

## 理解 JSBridge

![JSBridge](https://pic1.zhimg.com/v2-f6fa3298c938316197c4feff62042c46_1200x500.jpg)

>JSBridge 是连接 Native 与 JavaScript 的桥梁, 通过 JSBridge 实现双向通信。

<!-- more -->

![Mind](https://dailc.github.io/staticResource/blog/hybrid/jsbridge/img_hybrid_base_jsbridgePrinciple_1.png)

web 通过触发 URL Scheme，Native 捕获 URL, 解析并执行进一步操作
web 通过 JSBridge 注册本地方法, Native 执行调用 web 的方法

## 约定格式的 Scheme 协议

`[customscheme:][methodName][?param1=data1&param2=data2]`

- customscheme: 自定义需要拦截的 scheme
- methodName: 需要调用的 native 的方法
- params: 传递给 native 的参数

一些简单的例子:

`jsb://hideTitle` 隐藏标题

`jsb://goTo?page=abc` 跳转到页面 abc

`jsb://goTo?page=abc&callback=function(){}` 跳转到页面 abc, 并执行回调

## 如何发出 Scheme 请求

>通常的做法是创建一个隐藏的 iframe

```js
function createJsbridge (src) {
  var iframe = document.createElement('iframe')
  iframe.style.display = 'none'
  iframe.src = src
  document.documentElement.appendChild(iframe)
  setTimeout(function () {
    document.documentElement.removeChild(iframe)
  }, 0)
}
```

## 设计一个简单的 JSBridge

- `registerHandler(methodName, [callback])` web 在此注册本地 JS 方法, 注册后供 Native 可通过 JSBridge 进行调用。
- `callHandler(methodName, [optins], [callback])` 供 web 调用原生开放的 api。(本质是通过 url scheme 触发)。
- `handleMessageFromNative(methodName)` 供 Native 调用 web 页面注册的方法, 或者通知 web 页面执行回调方法。

### callHandler 函数内部实现过程

- 若存在回调, 则收集
- 通过特定方法, 将 `methodName`, `optins` 等拼接成一个URL Scheme
- 发出 Scheme 请求

<https://github.com/marcuswestin/WebViewJavascriptBridge>
<https://github.com/lzyzsd/JsBridge>

## 参考链接

- <https://dailc.github.io/2016/10/04/hybridBase04JSbridgePrinciple.html>
- <http://travelchu.com/2016/03/14/%E7%90%86%E8%A7%A3JSBridge/>
- <https://zhuanlan.zhihu.com/p/32899522>
- <https://juejin.im/entry/58ef1f5b5c497d006c72b04c>
- <https://github.com/rainjay/blog/issues/4>
