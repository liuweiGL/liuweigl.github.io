---
title: Electron 常见问题 (FAQ)
tags:
  - 前端
  - electron
date: 2019-04-18 17:38:35
---

## Not allowed to load local resource 错误

问题描述：

在使用 webpack-dev-server 提供开发环境时，electron 控制台显示 `Not allowed to load local resource` 无法加载 index.html。

原因：

webpack-dev-server 只会把资源存在内存中，mainWindow.loadFile 方法加载的路径上并没有真实资源。

解决方案：

在 webpack.config.js 中加入：

```js
module.exports = {
  devServer: {
    writeToDisk: true
  },
  ...
}
```

资料：   

<https://github.com/electron/electron/issues/5107#issuecomment-281200144>
