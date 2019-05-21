# weapp-component-rich-view

[![NPM version][npm-image]][npm-url] [![changelog][changelog-image]][changelog-url] [![license][license-image]][license-url]

[npm-image]: https://img.shields.io/npm/v/weapp-component-rich-view.svg?style=flat-square
[npm-url]: https://npmjs.org/package/weapp-component-rich-view
[license-image]: https://img.shields.io/github/license/ufologist/weapp-component-rich-view.svg
[license-url]: https://github.com/ufologist/weapp-component-rich-view/blob/master/LICENSE
[changelog-image]: https://img.shields.io/badge/CHANGE-LOG-blue.svg?style=flat-square
[changelog-url]: https://github.com/ufologist/weapp-component-rich-view/blob/master/CHANGELOG.md

微信小程序富文本(HTML)渲染组件
* 适用于公众号文章类似的图文内容, 让运营人员可以灵活编辑
* 可自定义按钮的行为, 例如触发分享, 跳转页面等等

## 组件属性

| 属性               | 类型                   | 默认值                                         | 描述                 |
|--------------------|------------------------|------------------------------------------------|----------------------|
| `contents`           | `Array<Content>`          |                                                | 内容                 |
| `loadHtmlContent`    | `Function(src, Content):Promise<data>` |                                                | 加载 HTML 内容的方法, 当配置了 `content.src` 时需设置 |
| `defaultHtmlContent` | `String`                 | `<p style="text-align:center">正在加载中...</p>` | 默认的 HTML 内容     |
| `emptyHtmlContent`   | `String`                 | `<p style="text-align:center">暂无内容</p>`      | 空内容提示           |
| `errorHtmlContent`   | `String`                 | `<p style="text-align:center">加载失败:(</p>`    | 加载失败的提示       |

### Content 对象

Content 基础属性, 即内容的公共属性
* `type`: 内容类型 - `{string}`
  * 目前支持 `html` 和 `button`
* `content`: 内容 - `{string}`
* `style`: CSS 样式 - `{object}`
* `hidden`: 是否隐藏 - `{boolean}`

`html` 类型的内容的特有属性
* `content`: 可以直接使用 `content` 属性来放置 HTML 的内容, 会优先使用
* `src`: 或者引用远程 HTML 文件的路径 - `{string}`
* `optimizeImgTag`: 是否优化 HTML 内容中的 `img` 标签, 默认为 `true` - `{boolean}`
* `optimizeSectionTag`: 是否优化 HTML 内容中的 `section` 标签, 默认为 `true` - `{boolean}`

`button` 类型的内容的特有属性
* `behavior`: 按钮的行为, 需要结合 `openType` 来使用 - `{string}`
  * 取值为 `weapp` 时使用开放能力的按钮行为
  * 取值为 `custom` 时使用自定义的按钮行为
* `openType`: 按钮的动作 - `{string}`
  * 对应原生的 `open-type` 值
  * 或者直接调用 `wx.xxx` 的 API, 例如 `wx.navigateTo`
  * 或者其他自定义内容
  * 同时会抛出 `clickbutton` 事件(`event.detail=button`)
* `options`: 额外的参数 - `{object}`
  * 在调用 `wx.xxx` 方法时可能需要的额外参数, 例如: 配置 `openType` 为 `wx.navigateTo` 时, 需要配置 `options: {url: '/pages/path/to?param=1'}`, 具体的选项与微信小程序的 API 文档保持一致
  * 或抛出事件时外部可能需要的额外参数

## 使用方法

PS: 目前适用于 [Min](https://github.com/meili/min-cli) 来构建的小程序项目接入, 对于原生小程序项目或者其他构建方式, 请手工 copy 代码 :(

* 安装组件

  ```
  npm install weapp-component-rich-view --save
  ```

* 引用组件(在页面中引用自定义组件)

  ```json
  "usingComponents": {
      "weapp-component-rich-view": "weapp-component-rich-view"
  }
  ```

* 使用组件(在页面中使用自定义组件)

  ```html
  <weapp-component-rich-view></weapp-component-rich-view>
  ```

## 示例

```html
<template>
<view>
    <weapp-component-rich-view contents="{{contents}}" loadHtmlContent="{{loadHtmlContent}}" bindclickbutton="handleClickButton"></weapp-component-rich-view>
</view>
</template>

<script>
export default {
    config: {
        usingComponents: {
            'weapp-component-rich-view': 'weapp-component-rich-view'
        }
    },
    data: {
        contents: [{
            "type": "html",
            "content": "<p style='color:red'>test</p>" // 直接放置 HTML 内容
        }, {
            "type": "html",
            "src": "https://domain.com/test.html"      // 引用远程 HTML 文件的路径
        }, {
            "type": "button",
            "content": "打开客服",
            "behavior": "weapp",                       // 开放能力的按钮
            "openType": "contact",                     // 开发能力的类型
            "style": {
                "position": "absolute",
                "top": "848rpx",
                "left": "235rpx",
                "width": "260rpx",
                "height": "50rpx",
                "line-height": "50rpx",
                "background-color": "#FF9800",
                "color": "#fff",
                "font-size": "24rpx"
            }
        }, {
            "type": "button",
            "content": "立即开始",
            "behavior": "custom",                      // 自定义的按钮
            "openType": "test",                        // 自定义按钮的类型
            "style": {
                "position": "absolute",
                "top": "548rpx",
                "left": "235rpx",
                "width": "260rpx",
                "height": "50rpx",
                "line-height": "50rpx",
                "background-color": "#FF9800",
                "color": "#fff",
                "font-size": "24rpx"
            }
        }, {
            "type": "button",
            "content": "跳转页面",
            "behavior": "custom",                      // 自定义的按钮
            "openType": "wx.navigateTo",               // 调用 wx.xxx API
            "options": {                               // 调用的额外参数
                "url": "/pages/path/to?param=1"
            },
            "style": {
                "position": "absolute",
                "top": "448rpx",
                "left": "235rpx",
                "width": "260rpx",
                "height": "50rpx",
                "line-height": "50rpx",
                "background-color": "#FF9800",
                "color": "#fff",
                "font-size": "24rpx"
            }
        }],
        loadHtmlContent: function(src, content) {
            return new Promise(function(resolve, reject) {
                wx.request({
                    url: src,
                    success: function(requestResult) {
                        resolve(requestResult.data);
                    }
                });
            });
        },
    },
    handleClickButton: function(event) {
        var button = event.detail;
        if (button.behavior === 'custom' && button.openType === 'test') {
            console.log('test');
        }
    }
};
</script>
<style lang="less"></style>
```

![demo](https://user-images.githubusercontent.com/167221/58071011-bebbc680-7bcd-11e9-9906-7bd103fddab4.png)

## 原理

* 通过 `rich-text` 来显示 `HTML` 的内容(传入 HTML 字符串)
  * **亲测 rich-text 的内容在域名白名单体制之外(不受白名单机制的控制), 连 HTTP 都可以!**
* 通过 `button` 来实现自定义按钮

## 其他方案

* https://github.com/jin-yufeng/Parser 解析 HTML 内容, 支持表格、图片、视频等多种类型
* https://github.com/TooBug/wemark Markdown渲染库
* https://github.com/qwqoffice/html2wxml HTML和Markdown格式的富文本渲染组件
* https://github.com/icindy/wxParse 富文本解析自定义组件，支持HTML及markdown解析
* https://github.com/pacochan/wxParser HTML 富文本解析
* https://github.com/zhanziyang/wxmlify 显示富文本编辑器生成的HTML
* https://github.com/sbfkcel/towxml 将HTML、Markdown转为微信小程序WXML(WeiXin Markup Language)的渲染库
* https://github.com/tans/we-rich Parse HTML into Weapp rich-text Nodes
* https://github.com/andrejewski/himalaya Parse HTML into JSON