#### webpack

webpack 本质是, 一个第三方模块包, 用于分析, 并打包代码
⚫ 支持所有类型文件的打包
⚫ 支持 less/sass => css
⚫ 支持 ES6/7/8 => ES5
⚫ 压缩代码, 提高加载速度

## 2. webpack 的使用步骤

### 2.0_webpack 基础使用

> 目标: 把 src 下的 2 个 js 文件, 打包到 1 个 js 中, 并输出到默认 dist 目录下

默认入口: ./src/index.js

默认出口: ./dist/main.js

==注意:路径上, 文件夹, 文件名不能叫 webpack/其他已知的模块名==

1. 初始化包环境

   ```bash
   yarn init
   ```

2. 安装依赖包

   ```bash
   yarn add webpack webpack-cli -D
   ```

3. 配置 scripts(自定义命令)

   ```bash
   scripts: {
   	"build": "webpack"
   }
   ```

4. 新建目录 src

5. 新建 src/add/add.js - 定义求和函数导出

   ```js
   export const addFn = (a, b) => a + b;
   ```

6. 新建 src/index.js 导入使用

   ```js
   import { addFn } from "./add/add";

   console.log(addFn(10, 20));
   ```

7. 运行打包命令

   ```bash
   yarn build
   #或者 npm run build
   ```

> 总结: src 并列处, 生成默认 dist 目录和打包后默认 main.js 文件

### 2.1_webpack 更新打包

> 目标: 以后代码变更, 如何重新打包呢

1. 新建 src/tool/tool.js - 定义导出数组求和方法

   ```js
   export const getArrSum = (arr) => arr.reduce((sum, val) => (sum += val), 0);
   ```

2. src/index.js - 导入使用

   ```js
   import { addFn } from "./add/add";
   import { getArrSum } from "./tool/tool";

   console.log(addFn(10, 20));
   console.log(getArrSum([1, 2, 3]));
   ```

3. 重新打包

   ```bash
   yarn build
   ```

> 总结 1: src 下开发环境, dist 是打包后, 分别独立
>
> 总结 2: 打包后格式压缩, 变量压缩等

## 3. webpack 的配置

### 3.1_webpack-入口和出口

> 目标: 告诉 webpack 从哪开始打包, 打包后输出到哪里

默认入口: ./src/index.js

默认出口: ./dist/main.js

webpack 配置 - webpack.config.js(默认)

1. 新建 src 并列处, webpack.config.js
2. 填入配置项

```js
const path = require("path");

module.exports = {
  entry: "./src/main.js", // 入口
  output: {
    path: path.join(__dirname, "dist"), // 出口路径
    filename: "bundle.js", // 出口文件名
  },
};
```

3. 修改 package.json, 自定义打包命令 - 让 webpack 使用配置文件

```json
"scripts": {
    "build": "webpack"
},
```

4. 打包观察效果

   ```bash
   yarn build
   ```

### 3.2\_打包流程图

![](./image/%E6%89%93%E5%8C%85%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

==重点: 所有要被打包的资源都要跟入口产生直接/间接的引用关系==

### 3.3\_插件-自动生成 html 文件

> 目标: html-webpack-plugin 插件, 让 webpack 打包后生成 html 文件并自动引入打包后的 js

1. 下载插件

   ```
   yarn add html-webpack-plugin  -D
   ```

2. webpack.config.js 配置

   ```js
   // 引入自动生成 html 的插件
   const HtmlWebpackPlugin = require("html-webpack-plugin");

   module.exports = {
     // ...省略其他代码
     plugins: [
       new HtmlWebpackPlugin({
         template: "./public/index.html", // 以此为基准生成打包后html文件
       }),
     ],
   };
   ```

3. 重新打包后观察 dist 下是否多出 html 并运行看效果

   ```bash
   yarn build
   ```

   ==打包后的 index.html 自动引入打包后的 js 文件==

> 总结: webpack 就像一个人, webpack.config.js 是人物属性, 给它穿什么装备它就干什么活

### 3.4\_加载器 - 处理 css 文件

> 目标: loaders 加载器, 可让 webpack 处理其他类型的文件, 打包到 js 中

原因: webpack 默认只认识 js 文件和 json 文件

1. 安装依赖

   ```
   yarn add style-loader css-loader -D
   ```

2. webpack.config.js 配置

   ```js
   const HtmlWebpackPlugin = require("html-webpack-plugin");

   module.exports = {
     // ...其他代码
     module: {
       rules: [
         // loader的规则
         {
           test: /\.css$/, // 匹配所有的css文件
           // use数组里从右向左运行
           // 先用 css-loader 让webpack能够识别 css 文件的内容并打包
           // 再用 style-loader 将样式, 把css插入到dom中
           use: ["style-loader", "css-loader"],
         },
       ],
     },
   };
   ```

3. 新建 src/css/li.css - 去掉 li 默认样式

   ```css
   ul,
   li {
     list-style: none;
   }
   ```

4. 引入到 main.js (因为这里是入口需要产生关系, 才会被 webpack 找到打包起来)

   ```js
   import "./css/index.css";
   ```

5. 运行打包后 dist/index.html 观察效果和 css 引入情况

   ```bash
   yarn build
   ```

> 总结: 万物皆模块, 引到入口, 才会被 webpack 打包, css 打包进 js 中, 然后被嵌入在 style 标签插入 dom 上

### 3.5\_加载器 - 处理 less 文件

> 目标: less-loader 让 webpack 处理 less 文件, less 模块翻译 less 代码

1. 下载依赖包

   ```bash
   yarn add less less-loader -D
   ```

2. webpack.config.js 配置

   ```js
   module: {
     rules: [
       // loader的规则
       // ...省略其他
       {
         test: /\.less$/,
         // 使用less-loader, 让webpack处理less文件, 内置还会用less翻译less代码成css内容
         use: ["style-loader", "css-loader", "less-loader"],
       },
     ];
   }
   ```

3. src/less/index.less - 设置 li 字体大小 24px

   ```less
   @size: 24px;

   ul,
   li {
     font-size: @size;
   }
   ```

4. 引入到 main.js 中

   ```js
   import "./less/index.less";
   ```

5. 打包运行 dist/index.html 观察效果

   ```bash
   yarn build
   ```

> 总结: 只要找到对应的 loader 加载器, 就能让 webpack 处理不同类型文件

### 3.6\_加载器 - 处理图片文件

如果使用的是 webpack5 版本的, 直接配置在 webpack.config.js - 的 rules 里即可

```js
 { // 图片文件的配置(仅适用于webpack5版本)
        test: /\.(gif|png|jpg|jpeg)/,
        type: 'asset' // 匹配上面的文件后, webpack会把他们当做静态资源处理打包
        // 如果你设置的是asset模式
        // 以8KB大小区分图片文件
        // 小于8KB的, 把图片文件转base64, 打包进js中
        // 大于8KB的, 直接把图片文件输出到dist下
      }
```

1. 下载依赖包

   ```bash
   yarn add url-loader file-loader -D
   ```

2. webpack.config.js 配置

webpack4 及以前使用下面的配置

```js
{
  test: /\.(png|jpg|gif|jpeg)$/i,
  use: [
    {
      loader: 'url-loader', // 匹配文件, 尝试转base64字符串打包到js中
      // 配置limit, 超过8k, 不转, file-loader复制, 随机名, 输出文件
      options: {
        limit: 8 * 1024,
      },
    },
  ],
}
```

图片转成 base64 字符串

- 好处就是浏览器不用发请求了，直接可以读取
- 坏处就是如果图片太大，再转`base64`就会让图片的体积增大 30% 左右

3. src/assets/准备 2 个图文件

4. 在 css/less/index.less - 把小图片用做背景图

   ```less
   body {
     background: url(../assets/logo_small.png) no-repeat center;
   }
   ```

5. 在 src/main.js - 把大图插入到创建的 img 标签上, 添加 body 上显示

   ```js
   // 引入图片-使用
   // webpack眼里万物皆模块
   import imgUrl from "./assets/1.gif";
   const theImg = document.createElement("img");
   theImg.src = imgUrl;
   document.body.appendChild(theImg);
   ```

6. 打包运行 dist/index.html 观察 2 个图片区别

> 总结: url-loader 把文件转 base64 打包进 js 中, 会有 30%的增大, file-loader 把文件直接复制输出

### 3.8_webpack 加载文件优缺点

图片转成 base64 字符串

- 好处就是浏览器不用发请求了，直接可以读取
- 坏处就是如果图片太大，再转`base64`就会让图片的体积增大 30% 左右

### 3.9\_加载器 - 处理字体文件

> 目标: 用 asset module 技术, asset/resource 直接输出到 dist 目录下

webpack5 使用这个配置

```js
{ // webpack5默认内部不认识这些文件, 所以当做静态资源直接输出即可
  test: /\.(eot|svg|ttf|woff|woff2)$/,
  type: 'asset/resource', // 所有的字体图标文件, 都输出到dist下
  generator: { // 生成文件名字 - 定义规则
   filename: 'fonts/[name].[hash:6][ext]' // [ext]会替换成.eot/.woff
        }
      }
```

webpack4 及以前使用下面的配置

1. webpack.config.js - 准备配置

   ```js
    { // 处理字体图标的解析
        test: /\.(eot|svg|ttf|woff|woff2)$/,
            use: [
                {
                    loader: 'url-loader',
                    options: {
                        limit: 2 * 1024,
                        // 配置输出的文件名
                        name: '[name].[ext]',
                        // 配置输出的文件目录
                        outputPath: "fonts/"
                    }
                }
            ]
    }
   ```

2. src/assets/ - 放入字体库 fonts 文件夹

3. 在 main.js 引入 iconfont.css

   ```js
   // 引入字体图标文件
   import "./assets/fonts/iconfont.css";
   ```

4. 在 public/index.html 使用字体图标样式

   ```html
   <i class="iconfont icon-weixin"></i>
   ```

5. 执行打包命令-观察打包后网页效果

> 总结: url-loader 和 file-loader 可以打包静态资源文件

### 3.10\_加载器 - 处理高版本 js 语法

> 目标: 让 webpack 对高版本 的 js 代码, 降级处理后打包

写代码演示: 高版本的 js 代码(箭头函数), 打包后, 直接原封不动打入了 js 文件中, 遇到一些低版本的浏览器就会报错

原因: **webpack 默认仅内置了 模块化的 兼容性处理** `import export`

babel 的介绍 => 用于处理高版本 js 语法 的兼容性 [babel 官网](https://www.babeljs.cn/)

解决: 让 webpack 配合 babel-loader 对 js 语法做处理

[babel-loader 文档](https://webpack.docschina.org/loaders/babel-loader/)

1. 安装包

   ```bash
   yarn add -D babel-loader @babel/core @babel/preset-env
   ```

2. 配置规则

   ```js
   module: {
     rules: [
       {
         test: /\.m?js$/,
         exclude: /(node_modules|bower_components)/, // 不去匹配这些文件夹下的文件
         use: {
           loader: "babel-loader", // 使用这个loader处理js文件
           options: {
             // 加载器选项
             presets: ["@babel/preset-env"], // 预设: @babel/preset-env 降级规则-按照这里的规则降级我们的js语法
           },
         },
       },
     ];
   }
   ```

3. 在 main.js 中使用箭头函数(高版本 js)

   ```js
   // 高级语法
   const fn = () => {
     console.log("你好babel");
   };
   console.log(fn); // 这里必须打印不能调用/不使用, 不然webpack会精简成一句打印不要函数了/不会编译未使用的代码
   // 没有babel集成时, 原样直接打包进lib/bundle.js
   // 有babel集成时, 会翻译成普通函数打包进lib/bundle.js
   ```

4. 打包后观察 lib/bundle.js - 被转成成普通函数使用了 - 这就是 babel 降级翻译的功能

> 总结: babel-loader 可以让 webpack 对高版本 js 语法做降级处理后打包

## 4. webpack 开发服务器

### 4.0_webpack 开发服务器-为何学?

文档地址: https://webpack.docschina.org/configuration/dev-server/

抛出问题: 每次修改代码, 都需要重新 yarn build 打包, 才能看到最新的效果, 实际工作中, 打包 yarn build 非常费时 (30s - 60s) 之间

为什么费时?

1. 构建依赖
2. 磁盘读取对应的文件到内存, 才能加载
3. 用对应的 loader 进行处理
4. 将处理完的内容, 输出到磁盘指定目录

解决问题: 起一个开发服务器, 在电脑内存中打包, 缓存一些已经打包过的内容, 只重新打包修改的文件, 最终运行加载在内存中给浏览器使用

### ==4.1_webpack-dev-server 自动刷新==

> 目标: 启动本地服务, 可实时更新修改的代码, 打包**变化代码**到内存中, 然后直接提供端口和网页访问

1. 下载包

   ```bash
   yarn add webpack-dev-server -D
   ```

2. 配置自定义命令

   ```js
   scripts: {
   	"build": "webpack",
   	"serve": "webpack serve"
   }
   ```

3. 运行命令-启动 webpack 开发服务器

   ```bash
   yarn serve
   #或者 npm run serve
   ```

> 总结: 以后改了 src 下的资源代码, 就会直接更新到内存打包, 然后反馈到浏览器上了

### 4.2_webpack-dev-server 配置

1. 在 webpack.config.js 中添加服务器配置

   更多配置参考这里: https://webpack.docschina.org/configuration/dev-server/#devserverafter

   ```js
   module.exports = {
       // ...其他配置
       devServer: {
         port: 3000 // 自定义端口号
         open:true//自动打开浏览器
       }
   }
   ```

#### yarn 常用命令

```bash
# 1. 初始化, 得到package.json文件(终端路径所在文件夹下)
yarn init

# 2. 添加依赖(下包)
# 命令: yarn add [package]
# 命令: yarn add [package]@[version]
yarn add jquery
yarn add jquery@3.5.1

# 3. 移除包
# 命令: yarn remove [package]
yarn remove jquery

# 4. 安装项目全部依赖(一般拿到别人的项目时, 缺少node_modules)
yarn
# 会根据当前项目package.json记录的包名和版本, 全部下载到当前工程中

# 5. 全局
# 安装: yarn global add [package]
# 卸载: yarn global remove [package]
# 注意: global一定在add左边
yarn global add @vue/cli
```

## 面试题

### 1、什么是 webpack（必会）

​ webpack 是一个打包模块化 javascript 的工具，在 webpack 里一切文件皆模块，通过 loader 转换文件，通过 plugin 注入钩子，最后输出由多个模块组合成的文件，webpack 专注构建模块化项目

### 2、Webpack 的优点是什么？（必会）

1. 专注于处理模块化的项目，能做到开箱即用，一步到位
2. 通过 plugin 扩展，完整好用又不失灵活
3. 通过 loaders 扩展, 可以让 webpack 把所有类型的文件都解析打包
4. 区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展

### 3、webpack 的构建流程是什么?从读取配置到输出文件这个过程尽量说全（必会）

​ Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

​ 1. 初始化参数：从配置文件读取与合并参数，得出最终的参数

2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，开始执行编译
3. 确定入口：根据配置中的 entry 找出所有的入口文件
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
5. 完成模块编译：在经过第 4 步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果

### 4、说一下 Webpack 的热更新原理(必会)

​ webpack 的热更新又称热替换（Hot Module Replacement），缩写为 HMR。这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

​ HMR 的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个 Websocket，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发起 Ajax 请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 jsonp 请求获取该 chunk 的增量更新。

​ 后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 HotModulePlugin 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像 react-hot-loader 和 vue-loader 都是借助这些 API 实现 HMR。

### 5、webpack 与 grunt、gulp 的不同？（必会）

​ **1)** **三者之间的区别**

​ 三者都是前端构建工具，grunt 和 gulp 在早期比较流行，现在 webpack 相对来说比较主流，不过一些轻量化的任务还是会用 gulp 来处理，比如单独打包 CSS 文件等。

​ grunt 和 gulp 是基于任务和流（Task、Stream）的。类似 jQuery，找到一个（或一类）文件，对其做一系列链式操作，更新流上的数据， 整条链式操作构成了一个任务，多个任务就构成了整个 web 的构建流程。

​ webpack 是基于入口的。webpack 会自动地递归解析入口所需要加载的所有资源文件，然后用不同的 Loader 来处理不同的文件，用 Plugin 来扩展 webpack 功能。

​ **2)** **从构建思路来说**

​ gulp 和 grunt 需要开发者将整个前端构建过程拆分成多个`Task`，并合理控制所有`Task`的调用关系 webpack 需要开发者找到入口，并需要清楚对于不同的资源应该使用什么 Loader 做何种解析和加工

​ **3)** **对于知识背景来说**

​ gulp 更像后端开发者的思路，需要对于整个流程了如指掌 webpack 更倾向于前端开发者的思路

### 6、有哪些常见的 Loader？他们是解决什么问题的？（必会）

1、 file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件

2、 url-loader：和 file-loader 类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去

3、 source-map-loader：加载额外的 Source Map 文件，以方便断点调试

4、 image-loader：加载并且压缩图片文件

5、 babel-loader：把 ES6 转换成 ES5

6、 css-loader：加载 CSS，支持模块化、压缩、文件导入等特性

7、 style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS。

8、 eslint-loader：通过 ESLint 检查 JavaScript 代码

### 7、Loader 和 Plugin 的不同？（必会）

​ **1)** **不同的作用**

​ Loader 直译为"加载器"。Webpack 将一切文件视为模块，但是 webpack 原生是只能解析 js 文件，如果想将其他文件也打包的话，就会用到 loader。 所以 Loader 的作用是让 webpack 拥有了加载和解析非 JavaScript 文件的能力。

​ Plugin 直译为"插件"。Plugin 可以扩展 webpack 的功能，让 webpack 具有更多的灵活性。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

**2)** **不同的用法**

​ Loader 在 module.rules 中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个 Object，里面描述了对于什么类型的文件（test），使用什么加载(loader)和使用的参数（options）

​ Plugin 在 plugins 中单独配置。 类型为数组，每一项是一个 plugin 的实例，参数都通过构造函数传入。

#### 知识拓展

1. 变量是一个容器, 表达式原地都有返回结果

   ```js
   var a = 10;
   console.log(a); // a就是变量, 运行后使用变量里的值再原地打印
   console.log(10 + 50); // 10 + 50 就是表达式
   console.log(a > 9); // 这叫判断表达式, 原地结果是true
   ```

2. new 类名() - 原地得到一个实例对象 - 对象身上有 key(或叫属性, 叫键都行), 对应的值是我们要使用的

3. 实例化对象就是 new 类名() 创造出来的对象, 身上包含属性(key, 键) 对应的 值

4. 什么是属性和方法(固定格式)

   ```js
   let obj = {
     // 属性指的是a, b, c, d, e这些名字
     a: 10,
     b: [1, 2, 3],
     c: function () {},
     d() {},
     e: () => {}, // 值是冒号:右边的值
   };
   // 这个格式是固定的
   ```

5. 对象的复制和取值(固定格式)

   有=(赋值运算符) 就是赋值, 没有就是取值

   ```js
   let obj = {
     a: 10,
     b: 20,
   };
   console.log(obj.a); // 从obj对象的a上取值, 原地打印10
   obj.b = 100; // 有=, 固定把右侧的值赋予给左侧的键, 再打印obj这个对象, b的值是100了
   ```

6. this 指向口诀

   在 function 函数中, this 默认指向当前函数的调用者 调用者.函数名()

   在箭头函数中, this 指向外层"函数"作用域 this 的值
