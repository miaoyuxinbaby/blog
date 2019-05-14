---
title: web 小图标处理思考
date: 2019-03-11 21:34:15
categories: 孤独的观测者
---

之前一直是用 `iconfont`, 挺好用的，但发现所有上传的图标都能被搜索到，可能会被扣上**泄露公司机密**的高帽，遂放弃

<!--more-->

有试过 `fontello` 但不支持彩色图标，故放弃。

我想了 3 种比较好的方式， 建议直接第一种方式。第二种和第三种绕了一大圈的感觉。

第一种方式就是传统的图片引入。

1. css background-image: url(图片路径) svg 文件也能引入。
2. 用 webpack 将本地 svg 文件夹内的文件整体引入，封装成 svg 组件后 输入名称使用

两种方式，均可以修改 svg 大小和颜色（单色），具体操作 是 css 的 fill 属性。

1- demo

```css
 .a {
     background-image('图片地址');
     background-size: contain;
 }
```

2- demo

```js
 修改webpack配置，

增加 `svg-sprite-loader` 配置，和修改 `file-loader` 使其忽略对svg文件的处理。

接着封装一个 `icon` 组件，和一个 `init.js`, 在代码的入口引入 `init.js` 其中将 svg 文件们 import 进项目当做一个 webpack 模块

然后使用的时候，引入`icon`组件，传入对应的 svg 文件名
```

3- demo

`svg-inline-loader` 处理 import 的 svg 文件,

```js
import skip from '../assets/skip.svg'
import lrc from '../assets/lrc.svg'

const Icons = {
  skip: skip,
  lrc: lrc
}
```

这样可以用 `icons.lrc` 这种方式，使用 svg 文件
