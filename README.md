<div align="center">
  <a href="https://dom-to-code.netlify.app/">
    <img src="https://raw.githubusercontent.com/better-tcy/dom-to-code/master/packages/doc/.vuepress/public/images/logo-bg.png" width="50%">
  </a>
  <div align="center">
    <h1>DOM TO CODE</h1>
    <p>简体中文 | <a href="./README_en.md">English</a></p>
    <p>开发效率神器，点击 dom 直接跳到编辑器对应代码。</p>
    <p>支持 vite/webpack + vue2/vue3/react + vscode/webstorm </p>
  </div>
  <p>
    <a href="https://www.npmjs.com/package/dom-to-code"><img src="https://img.shields.io/npm/v/dom-to-code.svg" alt="npm package"></a>
    <a href="#badge"><img src="https://img.shields.io/github/languages/top/better-tcy/dom-to-code" alt="language"></a>
    <a href="https://img.badgesize.io/https:/unpkg.com/dom-to-code/dist/?label=gzip%20size&compression=gzip"><img src="https://img.badgesize.io/https:/unpkg.com/dom-to-code/dist/?label=gzip%20size&compression=gzip" alt="gzip"></a>
    <a href="#badge"><img src="https://img.shields.io/librariesio/github/better-tcy/dom-to-code" alt="librariesio"></a>
    <a href="https://github.com/better-tcy/dom-to-code/blob/master/LICENSE"><img src="https://img.shields.io/github/license/better-tcy/dom-to-code" alt="LICENSE"></a>
    <img src="https://img.shields.io/github/stars/better-tcy/dom-to-code?style=social" alt="stars">
  </p>
</div>

## ✨ 介绍

接手一个项目开发网页时要修改某部分，要么靠搜索，要么靠记忆找到对应代码，过程极为浪费时间。

在项目里用了 dom-to-code 插件后，对准想要修改的 dom 部分 ctrl + 按下鼠标滚轮，就会在编辑器打开对应的 dom 元素源码。

(使用 mac 触摸板的用户可以ctrl + 触摸板右键)

支持 vite/webpack + vue2/vue3/react

支持 vscode/webstorm

别人搜索你直接跳，别人加班你摸鱼。

<img src="https://raw.githubusercontent.com/better-tcy/dom-to-code/master/packages/doc/.vuepress/public/images/dom-to-code-example.gif">

## 📦 安装

```bash
npm i dom-to-code
```

## 🔨 使用

#### 配置选项

```ts
interface Options {
  /**
   * 插件模式，默认是 vue
   */
  mode: 'vue' | 'react'

  /**
   * 引入的文件规则，vue 默认是 jsx、tsx、vue 文件，react 默认是 jsx、tsx 文件
   */
  include?: string | RegExp | string[] | RegExp[] | null

  /**
   * 排除的文件规则，默认是 node_modules 文件
   */
  exclude?: string | RegExp | string[] | RegExp[] | null
}

```

#### 第一步

首先在项目入口文件（比如 `index` 文件或 `main` 文件）中引入插件初始化

```ts
import { initDomToCode } from 'dom-to-code'

// 初始化 dom-to-code
// initDomToCode()

// 推荐：只在非生产环境初始化
process.env.NODE_ENV !== 'production' && initDomToCode()
```

#### 第二步

配置打包器，`dom-to-code` 支持 `vite` 和 `webpack` 打包器，以下是 `vite` 、`vue-cli`、`webpack` 里的示例(推荐只在非生产环境配置)

<details>
<summary>Vite</summary><br>

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import vue3 from '@vitejs/plugin-vue'
import { domToCodePlugin } from 'dom-to-code/vite'

export default defineConfig({
  plugins: [
    vue3(),
    process.env.NODE_ENV !== 'production'
      ? domToCodePlugin({
          mode: 'vue',
        })
      : undefined,
  ].filter((f) => !!f)
})
```

Example: [`playgrounds/vite-vue3`](./playgrounds/vite-vue3/)

<br></details>

<details>
<summary>Webpack</summary><br>

```ts
// webpack.config.js
const { domToCodePlugin } = require('dom-to-code/webpack')
module.exports = {
  /* ... */
  plugins: [
    domToCodePlugin({
      mode: 'vue'
    })
  ]
}
```

<br></details>

<details>
<summary>Vue CLI</summary><br>

```ts
// vue.config.js
const { domToCodePlugin, domToCodeDevServerV4, domToCodeDevServerV5 } = require('dom-to-code/webpack')

module.exports = {
  devServer: {
    // 如果你的 package.json 里的 @vue/cli-service 版本 <= 4.x.x，则使用 domToCodeDevServerV4
    // ...domToCodeDevServerV4,

    // 如果你的 package.json 里的 @vue/cli-service 版本 >= 5.x.x，则使用 domToCodeDevServerV5
    ...domToCodeDevServerV5
  },
  configureWebpack: {
    plugins: [
      domToCodePlugin({
        mode: 'vue'
      })
    ]
  }
  // 如果你使用的是chainWebpack
  // chainWebpack: (config) => {
  //   config
  //     .plugin('domToCodePlugin')
  //     .use(domToCodePlugin())
  // }
}
```

Example: [`playgrounds/webpack5-vue2`](./playgrounds/webpack5-vue2/)

<br></details>

<details>
<summary>Create-react-app + react-app-rewired</summary><br>

```ts
// config-overrides.js
const { domToCodePlugin, domToCodeDevServerV4, domToCodeDevServerV5 } = require('dom-to-code/webpack')

module.exports = {
  webpack(config) {
    config.plugins.push(domToCodePlugin({
      mode: 'react',
    }))
    return config
  },
  devServer(configFunction) {
    return function (proxy, allowedHost) {
      const config = configFunction(proxy, allowedHost)

      // 如果你的 package.json 里的 react-scripts 版本 <= 4.x.x，则使用 domToCodeDevServerV4
      // Object.assign(config, domToCodeDevServerV4)

      // 如果你的 package.json 里的 react-scripts 版本 >= 5.x.x，则使用 domToCodeDevServerV5
      Object.assign(config, domToCodeDevServerV5)

      return config
    }
  },
}
```

Example: [`playgrounds/webpack5-react`](./playgrounds/webpack5-react/)

<br></details>

## 📚 文档

查看 [文档指南 📒](https://dom-to-code.netlify.app/)(即将上线...)

## 💡 注意

如果无法跳转编辑器，请确保你的编辑器已经添加到环境变量，比如 vscode，添加成功后在命令终端输入

```bash
code -v
```

可以看到 vscode 版本信息意味着成功。

## 🤖️ Contributing

Learn about contribution [here](https://github.com/better-tcy/dom-to-code/blob/master/CONTRIBUTING.md).

This project exists thanks to all the people who contribute:

<a href="https://github.com/better-tcy/dom-to-code/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=better-tcy/dom-to-code" />
</a>

## 📄 License

[MIT](https://github.com/better-tcy/dom-to-code/blob/master/LICENSE) License © 2022-PRESENT [tuocangyu](https://github.com/better-tcy)


