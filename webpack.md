#### 简介

**webpack** 是一个用于现代 JavaScript 应用程序的 静态模块 打包工具。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 *bundle*。

#### 功能

- 代码转换
- 文件优化
- 代码分割
- 模块合并
- 自动刷新
- 代码校验
- 自动发布

#### 打包过程

https://juejin.cn/post/6854573217336541192#heading-5

- 获取模块内容
- 分析模块
- 收集依赖
- ES6转成ES5（AST）
- 递归获取所有依赖
- 处理两个关键字（import、exports）
- 输出打包结果代码

#### 配置项

##### devtool

此选项控制是否生成，以及如何生成 Source Map。

source map模式详细说明：https://blog.csdn.net/zwkkkk1/article/details/88758726

**开发环境**

- eval-source-map：每个模块使用eval()执行，而SourceMap作为DataUrl添加到eval()中。最初它是缓慢的，但是它提供快速的重建速度和产生真实的文件。行号被正确映射，因为它被映射到原始代码。它产生了最优质的开发资源。

**生产环境**

- （none）：省略配置devtool（默认），不生成 source map。

- nosources-source-map：创建的 source map 不包含 `sourcesContent(源代码内容)`。它可以用来映射客户端上的堆栈跟踪（暴露代码的行），而无须暴露所有的源代码。

  > 这仍然会暴露反编译后的文件名和结构，但它不会暴露原始代码。
  >
  > 不暴露原始代码

##### externals 选项（外部扩展）

将不需要打包的静态资源从构建逻辑中剔除出去，而使用 `CDN` 的方式，去引用它们

**webpack.config.js / vue.config.js**

```js
module.exports = {
  //...
  externals: {
    jquery: 'jQuery'
  }
};
```

#### loader和plugin

- loader：webpack 只能理解 JavaScript 和 JSON 文件。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 [模块](https://webpack.docschina.org/concepts/modules)
- plugin：plugins扩展了webpack的功能，在webpack运行时会广播很多事件，plugin可以监听这些事件，然后通过webpack提供的API来改变输出结果。

#### 常用 loader

https://juejin.cn/post/6844904031240863758#heading-9

https://webpack.docschina.org/loaders/

##### CSS

###### postcss-loader autoprefixer

```sh
npm i -D postcss-loader autoprefixer  
```

配置如下

```js
// webpack.config.js
module.exports = {
    module:{
        rules:[
            {
                test:/\.less$/,
                use:['style-loader','css-loader','postcss-loader','less-loader'] // 从右向左解析原则
           }
        ]
    }
} 
```

对于 Vue、React 的脚手架，内部均使用了 PostCSS，默认开启 autoprefixer 添加浏览器前缀

你可以根据 [Browserslist 规范](https://github.com/browserslist/browserslist#readme) 调整 `package.json` 中的 `browserslist` 来自定义目标支持浏览器。

###### 其余

- [`style-loader`](https://webpack.docschina.org/loaders/style-loader) 将模块导出的内容作为样式并添加到 DOM 中
- [`css-loader`](https://webpack.docschina.org/loaders/css-loader) 加载 CSS 文件并解析 import 的 CSS 文件，最终返回 CSS 代码
- [`less-loader`](https://webpack.docschina.org/loaders/less-loader) 加载并编译 LESS 文件
- [`sass-loader`](https://webpack.docschina.org/loaders/sass-loader) 加载并编译 SASS/SCSS 文件
- [`postcss-loader`](https://webpack.docschina.org/loaders/postcss-loader) 使用 [PostCSS](http://postcss.org/) 加载并转换 CSS/SSS 文件
- [`stylus-loader`](https://webpack.docschina.org/loaders/stylus-loader/) 加载并编译 Stylus 文件

对于 Sass 等 CSS 预处理语言的 loader，在使用 Vue、React 的脚手架创建项目时根据你选择的CSS预处理语言已经配置好。

##### 语法

- [`babel-loader`](https://webpack.docschina.org/loaders/babel-loader) 使用 [Babel](https://babeljs.io/) 加载 ES2015+ 代码并将其转换为 ES5
- [`ts-loader`](https://github.com/TypeStrong/ts-loader) 像加载 JavaScript 一样加载 [TypeScript](https://www.typescriptlang.org/) 2.0+

- `eslint-loader`：通过 ESLint 检查 JavaScript 、TypeScript代码

##### 图片和字体

- `image-loader`：加载并且压缩图片文件

- `file-loader`：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件 (处理图片和字体)

- `url-loader`：与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)

  > create react app 已经设置了 导入小于 10,000 字节的图片将返回 [data URI](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) 而不是路径，svg除外。

#### 常用插件

- `terser-webpack-plugin`: 支持压缩 ES6 (Webpack4)

- `webpack-parallel-uglify-plugin`: 多进程执行代码压缩，提升构建速度

- ```javascript
  new webpack.optimize.CommonsChunkPlugin(options); // 抽取公共代码
  ```

- `mini-css-extract-plugin`：将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件，并且支持 CSS 和 SourceMaps 的按需加载。

  > 抽离 css 样式,防止将样式打包在 js 中文件过大和因为文件大网络请求超时的情况

- `optimize-css-assets-webpack-plugin` ：压缩css

- `clean-webpack-plugin`: build之前，将打包目录清理

- `compression-webpack-analyzer`：gzip压缩

- `webpack-bundle-analyzer`: 可视化分析包大小 (业务组件、依赖第三方模块)

  > vue cli 已经集成了该功能

#### 优化打包体积

##### webpack-bundle-analyzer

```sh
npm i -D webpack-bundle-analyzer
```

##### externals 选项（外部扩展）

将不需要打包的静态资源从构建逻辑中剔除出去，而使用 `CDN` 的方式，去引用它们

**webpack.config.js / vue.config.js**

```js
module.exports = {
  //...
  externals: {
    jquery: 'jQuery'
  }
};
```

##### 开启 gzip 压缩

**webpack.config.js**

```js
module.exports = {
    plugins: [
      // 打压缩包
      new CompressionWebpackPlugin({
        algorithm: 'gzip',
        test: new RegExp(
          '\\.(' +
          ['js', 'css'].join('|') +
          ')$'
        ),
        threshold: 1024,
        minRatio: 0.8
      }), 
    ]
};
```

##### Tree Shaking

Tree Shaking是一个术语，通常用于描述移除 JavaScript 上下文中的未引用代码(dead-code)。

但是要想使其生效，生成的代码必须是ES6模块。不能使用其它类型的模块如`CommonJS`之流。如果使用`Babel`的话，这里有一个小问题，因为`Babel`的预案（preset）默认会将任何模块类型都转译成`CommonJS`类型，这样会导致`tree-shaking`失效。修正这个问题也很简单，在`.babelrc`文件或在`webpack.config.js`文件中设置`modules： false`就好了

```js
// .babelrc
{
  "presets": [
    ["@babel/preset-env",
      {
        "modules": false
      }
    ]
  ]
}
```

或者

```js
// webpack.config.js

module: {
    rules: [
        {
            test: /\.js$/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: ['@babel/preset-env', { modules: false }]
                }
            }，
            exclude: /(node_modules)/
        }
    ]
}

```

#### webpack 和 gulp 区别

gulp强调的是前端开发的工作流程，我们可以通过配置一系列的task，定义task处理的事务（例如文件压缩合并、雪碧图、启动server、版本控制等），然后定义执行顺序，来让gulp执行这些task，从而构建项目的整个前端开发流程。

webpack是一个前端模块化方案，更侧重模块打包，我们可以把开发中的所有资源（图片、js文件、css文件等）都看成模块，通过loader（加载器）和plugins（插件）对资源进行处理，打包成符合生产环境部署的前端资源。