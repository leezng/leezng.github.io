---
title: HTML5 实现下载功能
date: 2017-06-12 22:31:00
tags: [HTML5, JavaScript]
---

## 在 `<a>` 标签上直接设置 href, download

```html
<a href="./README.md" download>download</a>
```

这种方法适合静态文件的下载，此外设置 `download="file.json"` 可以设置文件名

## 动态生成文件

1. 设置 `href` 属性
2. 设置 `download` 属性
3. 使用 `JavaScript` 触发 `<a>` 标签的 `click` 事件

```js
var downloadFile = (element, fileName, content) => {
  element.download = fileName
  element.href = "data:text/plain," + content
}

element.addEventListener('click', downloadFile(arguments))
```

`data:text/plain`：把内容直接放置在 HTML 中，适合数据量小的内容，相对于 `httpURL` ，节省了一个 HTTP 请求。

<!-- more -->

### 一些常见的 dataURL 形式

```
data:, <文本数据>
data:text/plain, <文本数据>
data:text/html, <HTML代码>
data:text/html;base64, <base64 编码的 HTML 代码>
data:text/css, <CSS代码>
data:text/css; base64, <base64 编码的 CSS 代码>
data:text/javascript, <Javascript 代码>
data:text/javascript; base64, <base64 编码的 Javascript 代码>
data:image/gif; base64, base64 编码的 gif 图片数据
data:image/png; base64, base64 编码的 png 图片数据
data:image/jpeg; base64, base64 编码的 jpeg 图片数据
data:image/x-icon; base64, base64 编码的 icon 图片数据
```

**类型设置太麻烦？ 再次点击时，`href` 与 `download` 属性值不易更改？**

## 进一步优化

创建隐藏 `<a>` 标签 ，并使用 `URL.createObjectURL` 让浏览器帮我们设定文件类型。

> URL.createObjectURL() 方法会创建一个 [DOMString](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString)，其中包含一个表示参数对象的 URL，可以用在 href 和 src 之类的属性上。

```js
var downloadFile = (fileName, content) => {
  var link = document.createElement('a')
  var blob = new Blob([content])
  link.download = fileName
  link.href = URL.createObjectURL(blob)
  link.click()
  URL.revokeObjectURL(link) // 这个 URL 的生命周期和创建它的窗口中的 document 绑定, 浏览器会在文档退出的时候自动释放它们，但为了获得最佳性能和内存使用，你应该在安全的时机主动释放掉它们
} 
```

只需调用 `downloadFile` 函数，就可以启动下载，这样将使我们非常方便操作下载内容，例如进行下载前的校验等。

### 限制

只适合小文件的下载，如果文件太大，构建 `blob` 对象用时就越长，因此，如果是要下载大文件的话，还是更适合直接创建一个 `<a>` 标签

> [不同浏览器对 blob 对象有不同的限制](https://github.com/eligrey/FileSaver.js#supported-browsers)

## 参考资料

* [http://stackoverflow.com/questions/19327749/javascript-blob-filename-without-link](http://stackoverflow.com/questions/19327749/javascript-blob-filename-without-link)
* [http://www.alloyteam.com/2014/01/use-js-file-download/](http://www.alloyteam.com/2014/01/use-js-file-download/)
* [http://javascript.ruanyifeng.com/htmlapi/file.html](http://javascript.ruanyifeng.com/htmlapi/file.html)
* [http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/](http://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/)
* [https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)
* [https://developer.mozilla.org/zh-CN/docs/Web/API/Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)