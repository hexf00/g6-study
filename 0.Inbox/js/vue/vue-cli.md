# vue-cli

- [文档](https://cli.vuejs.org/zh/guide/)
- [vue-cli 环境变量](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F)
  - 在项目根目录定义不同文件来定义环境变量
- [vue-lint](https://eslint.vuejs.org/rules/) 可能是代码报错的重要原因

  - lint 一定要配置编辑器的 eslint 插件一起使用，否则编写过程中不能发现错误

- vue-cli npm 中有两个版本
  - `npm install -g vue-cli` 2.X
  - `npm install -g @vue/cli` 4.X

本文默认是基于 新版本

## 路由

- 使用锚链接则路由参数也需要在锚链接中
- 使用 history 模式则服务器需要配置 rewrite，路由参数在 query，重写避免入口文件不存在导致的 404
- [HTML5 History 模式 后端配置例子](https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90)

## 安装

- `vue ui` 启动可视化界面管理项目
  - 创建管理项目
  - vue-cli 插件管理，搜索、新增
  - npm 命令运行和配置
- @vue/cli-service 安装了一个名为 `vue-cli-service` 的命令
  - vue-cli 插件也会注入额外的命令
  - `npx vue-cli-service help [command]` 查看所有注入命令或单个命令
- Babel+Ts+Router+CssPreProcessors+linter/formatter +配置文件
  - **重要** [Class-Style Vue Components](https://cn.vuejs.org/v2/guide/typescript.html) TS 类风格式声明组件，由 vue 官方维护
    - 主要是提供一个`@Component`的装饰器
    - [vue-class-component](https://github.com/vuejs/vue-class-component) 详细文档
  - linter/formatter 往往是一体的，分别是格式检查和格式美化两个功能
- > Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)?
  > It will output ES2015 and delegate the rest to Babel for auto polyfill based on browser targets.

  - 大意是说 Babel+TSC 一起使用是有必要的

- babel 与 tsc
  - babel 和 tsc 都可以把高级的代码转义为旧的代码
  - tsc 不提供 polyfill，也没有 babel 那么强的定制性，可以针对具体的浏览器进行编译
  - babel 不支持类型检查
  - 两者可以配合使用，配合使用是最好的方式
  - [为什么 Babel 要支持编译 TypeScript @tank0317](https://juejin.im/post/5e034b9ee51d455820602e6a)
- sass/scss

  - sass 是指的编译器
  - scss 指语法
  - 对于[sass-loader](https://github.com/webpack-contrib/sass-loader)来说，编译器只是后端
  - sass/scss 有多个实现
    - [dart-sass](https://github.com/sass/dart-sass) 即 sass，dart 语言的标准实现，是主要版本
      - ng 使用但是这个版本
    - [node-sass](https://github.com/sass/node-sass) node.js 实现

- vue 官方在文档力推荐在 vsc 使用 vetur 插件

  - 但 tsx 是更方便的工具

- [vue 测试](https://vue-test-utils.vuejs.org/guides/)

## 旧版
