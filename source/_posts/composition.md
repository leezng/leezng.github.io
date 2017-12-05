---
title: 复合事件与中文输入法
date: 2017-12-03 14:52:15
tags: JavaScript
---

## 引言

复合事件是 DOM3 级事件中心添加的一类事件，用于处理 IME 的输入序列。

以下使用2个例子引入 `composition` 事件：

* 当前输入框设置了最大字符 `maxlength = 4` ，此时输入用户本意输入 `中文` 二字，但是当他切换中文输入法，输入 `zhon` 已被限制了。
* 当前输入框会根据用户输入内容进行实时搜索，此时用户本意是搜索 `中文` 二字相关的内容，而输入 `zhong'wen` 时其实无需进行搜索操作。

<!-- more -->

## compositionstart, compositionupdate, compositionend

* `compositionstart` 
中文输入法开始输入时触发，每次输入开始仅执行一次
* `compositonupdate` 
中文输入法在输入时触发，返回实时内容，例如zhong'wen，仅在 `compositionstart` 触发后触发，输入时实时触发多次
* `compositionend` 
复合系统关闭，返回正常键盘输入状态，中文输入法输入完成时触发，所得到的结果就是最终输入完成的结果，执行一次

## event.type

* compositionstart: `event.type = compositionstart`
* compositionupdate: `event.type = compositionupdate`
* compositionend: `event.type = compositionend`

## event.data

* compositionstart 访问 `event.data`: 正在编辑的文本, 例如选中了输入框中某块内容进行编辑
* compositionupdate 访问 `event.data`: 正插入的实时新字符, 例如zhong'wen
* compositionend 访问 `event.data`: 最终插入的字符, 例如中文

## 参考资料

* <https://developer.mozilla.org/zh-CN/docs/Web/Events/compositionstart>
* <https://segmentfault.com/a/1190000004326310>