# Vue工程初始化

# 安装

### 环境准备
1. 安装Node.js，同时安装NPM
2. 由于 npm 安装速度慢，需要使用了淘宝的镜像及其命令 cnpm。进入cmd，利用npm安装淘宝镜像的cnpm
```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```
3. 安装Vue
```
$ cnpm install vue
```
4. 全局安装 vue-cli
```
$ cnpm install --global vue-cli
```
### 创建一个基于 webpack 模板的新项目
> 注意按提示填写和选择相关信息
- Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：嵌套的路由/视图表、模块化的、基于组件的路由配置、路由参数、查询、通配符、基于 Vue.js 过渡系统的视图过渡效果、细粒度的导航控制、带有自动激活的 CSS class 的链接、HTML5 历史模式或 hash 模式，在 IE9 中自动降级、自定义的滚动条行为
- ESLint：代码检查工具
- unit tests：单元测试
- e2e tests：端到端测试（功能测试）
- Should we run `npm install`...: 选no，创建完工程后自己npm install

```
$ vue init webpack my-project

? Project name my-project
? Project description A Vue.js project
? Author
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests Yes
? Pick a test runner jest
? Setup e2e tests with Nightwatch? n
? Should we run `npm install` for you after the project has been created? (recommended) no

   vue-cli · Generated "my-project".

# Project initialization finished!
# ========================

To get started:

  cd my-project
  npm install (or if using yarn: yarn)
  npm run lint -- --fix (or for yarn: yarn run lint --fix)
  npm run dev

Documentation can be found at https://vuejs-templates.github.io/webpack
```

## 启动和访问
```
$ cd my-project
$ cnpm install
$ cnpm run dev
```
浏览器访问：http://localhost:8080/

## VS Code代码格式化问题处理
> VS Code 的自动格式化和Eslint冲突报错，需要进行一些设置
1. 方法名和括号之间需要空格
```
问题：error: Missing space before function parentheses (space-before-function-paren)
解决：找到.eslintrc.js，在rules里面加上'space-before-function-paren': 0
```
2. 自动格式化后字符串被打上双引号，报错，Eslint中规定用单引号
```
解决：在项目根目录下创建.prettierrc文件，添加以下内容
{
  "singleQuote": true // 单引号
}
```
3. 自动格式化后花括号后面追加了分号，报错
```
解决：在项目根目录下创建.prettierrc文件，添加以下内容
{
  "semi": false // 分号
}
但是导致function中的代码行都没分号了，也可以直接改rules，不检查分号
找到.eslintrc.js，在rules里面加上'semi': 0。
目前还没更好的办法技能去除花括号后面的分号，又能保留代码行的分号
```

> 除了上述办法外，也可以修改vetur插件的格式化配置为vscode-typescript，这样的后果是格式化后，不会修改引号，不会添加/删除分号，有利有弊吧。
```
"vetur.format.defaultFormatter.js": "vscode-typescript"
```
