[![Build Status](https://travis-ci.org/think2011/localResizeIMG4.svg?branch=master)](https://travis-ci.org/think2011/localResizeIMG4)
[![npm version](https://img.shields.io/npm/v/lrz.svg)](https://www.npmjs.com/package/lrz)
[![npm](https://img.shields.io/npm/l/express.svg)]()

# 演示一下

![](http://think2011.github.io/localResizeIMG4/test/demo.gif)

# 自己试试

![](http://think2011.github.io/localResizeIMG4/test/qrcode.png)


[点我直接进入演示页面](http://think2011.github.io/localResizeIMG4/test/)


# 介绍说明
在前端压缩好要上传的图片可以更快的发送给后端，因此也特别适合在移动设备上使用。

* 兼容IOS&Android，修复了IOS和Android某些版本已知的BUG（`这也是插件最有意义的存在价值`）。

* 按需加载文件，仅需引入`lrz.bundle.js`文件，例如当检测是IOS7的用户，那么会自动异步再载入修复BUG的文件，而无需担心浪费宝贵的带宽开销。

* 原生JS编写，不依赖例如`jquery`等第三方库。

# 如何获取

通过以下方式都可以下载：

1. 执行`npm i lrz`（推荐）
2. 执行`bower install lrz`
3. 或者 `git clone`本库
4. 要么 在页面右侧点击`download zip`

接着在页面中引入
```html
<script src="./dist/lrz.bundle.js"></script>
```
`请绝对不要删除【dist】目录下的任何文件，那些是某些情况自动按需载入的文件。`

# 如何使用

### 方式1:

如果您的图片来自用户拍摄或者上传的，您需要一个`input file`来获取图片。

```html
<input onchange="upload()" type="file" capture="camera" />
```

接着通过change事件可以得到用户选择的图片
```js
function upload () {
    lrz(this.files[0])
        .then(function (rst) {
            // 处理成功会执行
        })
        .catch(function (err) {
            // 处理失败会执行
        })
        .always(function () {
            // 不管是成功失败，都会执行
        });
});
```

### 方式2：

如果您的图片不是来自用户上传的，那么也可以直接传入图片路径。

```js
lrz('./xxx/xx/x.png')
        .then(function (rst) {
            // 处理成功会执行
        })
        .catch(function (err){
            // 处理失败会执行
        })
        .always(function () {
            // 不管是成功失败，都会执行
        });
```

# 后端处理

* 后端最终会收到前端发送的`base64`字符串，接着处理字符串为图片即可。

* 具体请使用关键字`base64 转 image 开发语言`进行谷歌|百度。

* 前端生成的结果中有一个`base64Len`，这是字符串的长度，后端应该核对以确认是否提交完整。


```
//php示例：
$img = base64_decode($_POST['img']);
$img = imagecreatefromstring($img);
```

# API

## 参数
```js
lrz(file, [options]);
```

* `file` 通过 `input:file` 得到的文件，或者直接传入图片路径

* `[options]` 这个参数允许忽略
  * `options.width {Number}` 图片最大不超过的宽度，默认为原图宽度，高度不设时会适应宽度。
  * `options.height {Number}` 同上
  * `options.quality {Number}` 图片压缩质量，取值 0 - 1，默认为0.7

```js
lrz.version
```

* 显示当前版本号

## 返回结果
返回值是一个`promise`对象

* `then(rst)`
  * `rst.origin` 也就是file对象，里面存了一些图片文件的信息，例如大小，日期等。
  * `rst.base64` 生成后的图片base64，后端可以处理此字符串为图片
  * `rst.base64Len` 生成后的图片的大小，后端可以通过此值来校验是否传输完整

* `catch(err)`

* `always()`

## 一个例子
```js
document.querySelector('input').addEventListener('change', function () {
    // this.files[0] 是用户选择的文件
    lrz(this.files[0], {width: 1024})
        .then(function (rst) {
            // 把处理的好的图片给用户看看呗
            var img = new Image();
            img.src = rst.base64;

            img.onload = function () {
                document.body.appendChild(img);
            };

            return rst;
        })
        .then(function (rst) {
            // 这里该上传给后端啦
            // 伪代码：ajax(rst.base64)..

            return rst;
        })
        .then(function (rst) {
            // 如果您需要，一直then下去都行
            // 因为是Promise对象，可以很方便组织代码 \(^o^)/~
        })
        .catch(function (err) {
            // 万一出错了，这里可以捕捉到错误信息
            // 而且以上的then都不会执行

            alert(err);
        })
        .always(function () {
            // 不管是成功失败，这里都会执行
        });
});
```

[具体参数说明请查看WIKI。](https://github.com/think2011/localResizeIMG4/wiki)

# 兼容性

IE9以上及大部分非IE浏览器（chrome、微信什么的）

# FAQ

[有疑问请直接在 issues 中提问 🙈](https://github.com/think2011/localResizeIMG4/issues)，顺便附上以下内容吧：

```
平台：微信..
设备：iPhone5 IOS7..
问题：问题描述呗..
```

* Q：有时拍摄完照片后，页面自动刷新或闪退了。
* A：虽然已作了优化处理，但内存似乎还是爆掉了，常见于低配android手机。

* Q: 怎么批量上传图片?
* A: 您可以自己写个循环来传入用户多选的图片，但在移动端上请勿这样做，原因同上。

* Q: 直接传入图片路径的无法生成图片
* A: 可能是跨域的问题，具体请看[CORS_enabled_image](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_enabled_image)

* Q: 想要商用可以吗？
* A: 没问题，但请留意issue里已知的问题。

## lrz的历史
* lrz1，基于jquery。
* lrz2，基于原生js，却是用coffeescript写的 Orz, 有UI，存在已知BUG。
* lrz3，基于原生js，提供pc & mobile版本，修复BUG和疑问，再一次重构了代码，移除了UI，仅作为纯粹的工具，方便二次开发。
* lrz4，基于原生js，已升级到稳定版本，推荐使用。


# 感谢

* @dwandw
* @yourlin
* @wxt2005

以上在之前的版本帮忙参与维护的朋友，以及提出问题的朋友们，真的真的很感谢你们。：D
