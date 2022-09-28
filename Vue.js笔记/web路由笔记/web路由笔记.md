## 1. vue 路由简介和基础使用

### 1.0 什么是路由

1. 路由是什么呢?
   1. 路由是一种映射关系
2. Vue 中的路由是什么?
   1. 路径和组件的映射关系

### 1.1 为什么使用路由

> 目标: 在一个页面里, 切换业务场景

单页面应用(SPA): 所有功能在一个 html 页面上实现

前端路由作用: 实现业务场景切换

优点：

- 整体不刷新页面，用户体验更好

- 数据传递容易, 开发效率高

缺点：

- 开发成本高(需要学习专门知识)

- 首次加载会比较慢一点。不利于 seo

### 1.2 vue-router 介绍

> 目标: 如何在 Vue 项目中集成路由

官网: https://router.vuejs.org/zh/

vue-router 模块包

它和 Vue.js 深度集成

可以定义 - 视图表(映射规则)

模块化的

提供 2 个内置全局组件

声明式导航自动激活的 CSS class 的链接

……

### 1.3 路由 - 组件分类

> 目标: .vue 文件分 2 类, 一个是页面组件, 一个是复用组件

- 页面组件 - 页面展示 - 配合路由用
- 复用组件 - 展示数据/常用于复用

### 1.4 vue-router 使用

[vue-router 文档](https://router.vuejs.org/zh/)

- 安装

```bash
yarn add vue-router
```

- 导入路由

```js
import VueRouter from "vue-router";
```

- 使用路由插件

```jsx
// 在vue中，使用使用vue的插件，都需要调用Vue.use()
Vue.use(VueRouter);
```

- 创建路由规则数组

```js
const routes = [
  {
    path: "/find",
    component: Find,
  },
  {
    path: "/my",
    component: My,
  },
  {
    path: "/part",
    component: Part,
  },
];
```

- 创建路由对象 - 传入规则

```jsx
const router = new VueRouter({
  routes,
});
```

- 关联到 vue 实例

```jsx
new Vue({
  router,
});
```

- components 换成 router-view

```vue
<router-view></router-view>
```

> 总结 1: 下载路由模块, 编写对应规则注入到 vue 实例上, 使用 router-view 挂载点显示切换的路由

> 总结 2: 一切都围绕着 hash 值变化为准

## 2. vue 路由 - 声明式导航

### 2.0 声明式导航 - 基础使用

> 目标: 可用全局组件 router-link 来替代 a 标签

1.  vue-router 提供了一个全局组件 router-link
2.  router-link 实质上最终会渲染成 a 链接 to 属性等价于提供 href 属性(to 无需#)
3.  router-link 提供了声明式导航高亮的功能(自带类名)

```vue
<template>
  <div>
    <div class="footer_wrap">
      <router-link to="/find">发现音乐</router-link>
      <router-link to="/my">我的音乐</router-link>
      <router-link to="/part">朋友</router-link>
    </div>
    <div class="top">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style scoped>
/* 省略了 其他样式 */
.footer_wrap .router-link-active {
  color: white;
  background: black;
}
</style>
```

> 总结: 链接导航, 用 router-link 配合 to, 实现点击切换路由

### 2.1 声明式导航 - 跳转传参

> 目标: 在跳转路由时, 可以给路由对应的组件内传值

在 router-link 上的 to 属性传值, 语法格式如下

- /path?参数名=值

- /path/值 – 需要路由对象提前配置 path: “/path/参数名”

对应页面组件接收传递过来的值

- $route.query.参数名

- $route.params.参数名

1. 创建 components/Part.vue - 准备接收路由上传递的参数和值

   ```vue
   <template>
     <div>
       <p>关注明星</p>
       <p>发现精彩</p>
       <p>寻找伙伴</p>
       <p>加入我们</p>
       <p>人名: {{ $route.query.name }} -- {{ $route.params.username }}</p>
     </div>
   </template>
   ```

2. 路由定义

   ```js
   {
       path: "/part",
       component: Part
     },
     {
       path: "/part/:username", // 有:的路径代表要接收具体的值
       component: Part
     },
   ```

3. 导航跳转, 传值给 MyGoods.vue 组件

   ```vue
   <router-link to="/part?name=小传">朋友-小传</router-link>
   <router-link to="/part/小智">朋友-小智</router-link>
   ```

> 总结:

> ?key=value 用 '$route.query.key' 取值，
> 传值,提前在路由规则设置/path/:key，用'$route.params.key'取值

## 3. vue 路由 - 重定向和模式

### 3.0 路由 - 重定向

> 目标: 匹配 path 后, 强制切换到目标 path 上

- 网页打开 url 默认 hash 值是/路径
- redirect 是设置要重定向到哪个路由路径

例如: 网页默认打开, 匹配路由"/", 强制切换到"/find"上

```js
const routes = [
  {
    path: "/", // 默认hash值路径
    redirect: "/find", // 重定向到/find
    // 浏览器url中#后的路径被改变成/find-重新匹配数组规则
  },
];
```

> 总结: 强制重定向后, 还会重新来数组里匹配一次规则

### 3.1 路由 - 404 页面

> 目标: 如果路由 hash 值, 没有和数组里规则匹配

默认给一个 404 页面

语法: 路由最后, path 匹配\*(任意路径) – 前面不匹配就命中最后这个, 显示对应组件页面

1. 创建 NotFound 页面

   ```vue
   <template>
     <img src="../assets/404.png" alt="" />
   </template>

   <script>
   export default {};
   </script>

   <style scoped>
   img {
     width: 100%;
   }
   </style>
   ```

2. 在 main.js - 修改路由配置

   ```js
   import NotFound from "@/views/NotFound";

   const routes = [
     // ...省略了其他配置
     // 404在最后(规则是从前往后逐个比较path)
     {
       path: "*",
       component: NotFound,
     },
   ];
   ```

> 总结: 如果路由未命中任何规则, 给出一个兜底的 404 页面

### 3.2 路由 - 模式设置

> 目标: 修改路由在地址栏的模式

hash 路由例如: http://localhost:8080/#/home

history 路由例如: http://localhost:8080/home (以后上线需要服务器端支持, 否则找的是文件夹)

[模式文档](https://router.vuejs.org/zh/api/#mode)

router/index.js

```js
const router = new VueRouter({
  routes,
  mode: "history", // 打包上线后需要后台支持, // 默认不写是"hash"
});
```

## 4. vue 路由 - 编程式导航

> 用 JS 代码跳转, 声明式导航用 a 标签

### 4.0 编程式导航 - 基础使用

> 目标: 用 JS 代码来进行跳转

语法:

```js
this.$router.push({
  path: "路由路径", // 都去 router/index.js定义
  name: "路由名",
});
```

1. main.js - 路由数组里, 给路由起名字

```js
{
    path: "/find",
    name: "Find",
    component: Find
},
{
    path: "/my",
    name: "My",
    component: My
},
{
    path: "/part",
    name: "Part",
    component: Part
},
```

2. --App.vue

```vue
<template>
  <div>
    <div class="footer_wrap">
      <span @click="btn('/find', 'Find')">发现音乐</span>
      <span @click="btn('/my', 'My')">我的音乐</span>
      <span @click="btn('/part', 'Part')">朋友</span>
    </div>
    <div class="top">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
// 目标: 编程式导航 - js方式跳转路由
// 语法:
// this.$router.push({path: "路由路径"})
// this.$router.push({name: "路由名"})
// 注意:
// 虽然用name跳转, 但是url的hash值还是切换path路径值
// 场景:
// 方便修改: name路由名(在页面上看不见随便定义)
// path可以在url的hash值看到(尽量符合组内规范)
export default {
  methods: {
    btn(targetPath, targetName) {
      // 方式1: path跳转
      this.$router.push({
        // path: targetPath,

        // 方式2: name跳转
        name: targetName,
      });
    },
  },
};
</script>
```

### 4.1 编程式导航 - 跳转传参

> 目标: JS 跳转路由, 传参

语法 query / params 任选 一个

```js
this.$router.push({
    path: "路由路径"
    name: "路由名",
    query: {
    	"参数名": 值
    }
    params: {
		"参数名": 值
    }
})

// 对应路由接收   $route.params.参数名   取值
// 对应路由接收   $route.query.参数名    取值
```

==格外注意: 使用 path 会自动忽略 params==

App.vue

```vue
<template>
  <div>
    <div class="footer_wrap">
      <span @click="oneBtn">朋友-小传</span>
      <span @click="twoBtn">朋友-小智</span>
    </div>
    <div class="top">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
// 目标: 编程式导航 - 跳转路由传参
// 方式1:
// params => $route.params.参数名
// 方式2:
// query => $route.query.参数名
// 重要: path会自动忽略params
// 推荐: name+query方式传参
// 注意: 如果当前url上"hash值和?参数"与你要跳转到的"hash值和?参数"一致, 爆出冗余导航的问题, 不会跳转路由
export default {
  methods: {
    oneBtn() {
      this.$router.push({
        // 运用name跳转，query和params方式传参都可以
        name: "Part",
        params: {
          username: "小传",
        },
      });
    },
    twoBtn() {
      this.$router.push({
        name: "Part",
        query: {
          name: "小智",
        },
      });
    },
  },
};
</script>
```

> 总结: 传参 2 种方式：
> query 方式
> params 方式

## 5. vue 路由 - 嵌套和守卫

### 5.0 vue 路由 - 路由嵌套

> 目标: 在现有的一级路由下, 再嵌套二级路由

[二级路由示例-网易云音乐-发现音乐下](https://music.163.com/)

router-view 嵌套架构图

1. 创建需要用的所有组件

   src/views/Find.vue -- 发现音乐页

   src/views/My.vue -- 我的音乐页

   src/views/Second/Recommend.vue -- 发现音乐页 / 推荐页面

   src/views/Second/Ranking.vue -- 发现音乐页 / 排行榜页面

   src/views/Second/SongList.vue -- 发现音乐页 / 歌单页面

2. main.js– 继续配置 2 级路由

   一级路由 path 从/开始定义

   二级路由往后 path 直接写名字, 无需/开头

   嵌套路由在上级路由的 children 数组里编写路由信息对象

3. 说明：

   App.vue 的 router-view 负责发现音乐和我的音乐页面, 切换

   Find.vue 的的 router-view 负责发现音乐下的, 三个页面, 切换

![](./image/%E4%BA%8C%E7%BA%A7%E8%B7%AF%E7%94%B1.png)

1. 配置路由规则-二级路由展示

```js
const routes = [
  // ...省略其他
  {
    path: "/find",
    name: "Find",
    component: Find,
    children: [
      {
        path: "recommend",
        component: Recommend,
      },
      {
        path: "ranking",
        component: Ranking,
      },
      {
        path: "songlist",
        component: SongList,
      },
    ],
  },
  // ...省略其他
];
```

> 总结: 嵌套路由, 找准在哪个页面里写 router-view 和对应规则里写 children

### 5.1 声明导航 - 类名区别

> 目标: router-link 自带的 2 个类名的区别是什么

观察路由嵌套导航的样式

- router-link-exact-active (精确匹配) url 中 hash 值路径, 与 href 属性值完全相同, 设置此类名

- router-link-active (模糊匹配) url 中 hash 值, 包含 href 属性值这个路径

![](./image/%E4%B8%A4%E4%B8%AA%E5%86%85%E7%BD%AE%E7%B1%BB%E5%90%8D%E7%9A%84%E5%8C%BA%E5%88%AB.png)

### 5.2 全局前置守卫

> 目标: 路由跳转之前, 先执行一次前置守卫函数, 判断是否可以正常跳转

使用例子: 在跳转路由前, 判断用户登陆了才能去<我的音乐>页面, 未登录弹窗提示回到发现音乐页面

1. 在路由对象上使用固定方法 beforeEach

```js
// 目标: 路由守卫
// 场景: 当你要对路由权限判断时
// 语法: router.beforeEach((to, from, next)=>{//路由跳转"之前"先执行这里, 决定是否跳转})
// 参数1: 要跳转到的路由 (路由对象信息)    目标
// 参数2: 从哪里跳转的路由 (路由对象信息)  来源
// 参数3: 函数体 - next()才会让路由正常的跳转切换, next(false)在原地停留, next("强制修改到另一个路由路径上")
// 注意: 如果不调用next, 页面留在原地

// 例子: 判断用户是否登录, 是否决定去"我的音乐"/my
const isLogin = true; // 登录状态(未登录)
router.beforeEach((to, from, next) => {
  if (to.path === "/my" && isLogin === false) {
    alert("请登录");
    next(false); // 阻止路由跳转
  } else {
    next(); // 正常放行
  }
});
```

> 总结: next()放行, next(false)留在原地不跳转路由, next(path 路径)强制换成对应 path 路径跳转

- 路由跳转传参总结

  | 跳转方法                                                | 传参位置        | 路由规则               | 接收                                     |
  | ------------------------------------------------------- | --------------- | ---------------------- | ---------------------------------------- |
  | <router-link to="/path?key=value"></router-link>        | /path?key=value | 无特殊                 | $route.query.key                         |
  | <router-link to="/path/值"></router-link>               | /path/值        | /path/:key             | $route.params.key                        |
  | this.$router.push({path: "/path", query: {key: value}}) | query 的对象    | 无特殊                 | $route.query.key                         |
  | this.$router.push({name: "com", params: {key: value})   | params 的对象   | 路由规则需要 name 属性 | $route.params.key(注意,这种在内存中保存) |

  ==无论哪种格式, 声明式和编程式都是通用的, 保证路径和参数格式正确就 ok==

## 补充知识

## 1. vant 组件库

### 1.0 vant 组件库-介绍

> 目标: vant 是一个轻量、可靠的移动端 Vue 组件库, 开箱即用

[vant 官网](https://vant-contrib.gitee.io/vant/#/zh-CN/)

特点:

- 提供 60 多个高质量组件，覆盖移动端各类场景
- 性能极佳，组件平均体积不到 1kb
- 完善的中英文文档和示例
- 支持 Vue 2 & Vue 3
- 支持按需引入和主题定制

### 1.1 全部引入

1.全部引入, 快速开始: [https://vant-contrib.gitee.io/vant/#/zh-CN/quickstart](https://vant-contrib.gitee.io/vant/)

2.下载 Vant 组件库到当前项目中

3.在 main.js 中全局导入所有组件,

4.使用按钮组件 – 作为示范的例子

![](./image/%E5%AF%BC%E5%85%A5%E6%89%80%E6%9C%89%E7%BB%84%E4%BB%B6.png)

1. 下载 vant 组件库到当前项目中

   ```bash
   yarn add vant -D
   ```

2. 导入所有组件, 在 main.js 中

   ```js
   import Vue from "vue";
   import Vant from "vant";
   import "vant/lib/index.css";

   Vue.use(Vant);
   ```

3. 使用按钮组件

   https://vant-contrib.gitee.io/vant/#/zh-CN/button

   ```vue
   <van-button type="primary">主要按钮</van-button>
   <van-button type="info">信息按钮</van-button>
   <van-button type="default">默认按钮</van-button>
   <van-button type="warning">警告按钮</van-button>
   <van-button type="danger">危险按钮</van-button>
   ```

### 1.2 手动按需引入

> 目标: 只引入使用的组件

![](./image/%E6%89%8B%E5%8A%A8%E6%8C%89%E9%9C%80%E5%BC%95%E5%85%A5.png)

```js
// 方式2: 手动 按需引入
import Button from "vant/lib/button"; // button组件
import "vant/lib/button/style"; // button样式
```

2. 注册

```js
 components: { // 手动注册组件名
  // VanButton: Button
   // 等价的
  [Button.name]: Button
 }
```

3. 使用

```vue
<van-button type="primary">主要按钮</van-button>
<van-button type="info">信息按钮</van-button>
<van-button type="default">默认按钮</van-button>
<van-button type="warning">警告按钮</van-button>
<van-button type="danger">危险按钮</van-button>
```

### 1.3 自动按需引入（主推）

> 目标: 按需加载组件

[babel-plugin-import](https://github.com/ant-design/babel-plugin-import) 是一款 babel 插件，它会在编译过程中将 import 的写法自动转换为按需引入的方式。

1. 安装插件

   ```bash
   yarn add babel-plugin-import -D
   ```

2. 在 babel 配置文件里 (babel.config.js)

   ```js
   module.exports = {
     plugins: [
       [
         "import",
         {
           libraryName: "vant",
           libraryDirectory: "es",
           style: true,
         },
         "vant",
       ],
     ],
   };
   ```

3. 全局注册 - 会自动按需引入

   ```js
   // 方式1: 全局 - 自动按需引入vant组件
   // (1): 下载 babel-plugin-import
   // (2): babel.config.js - 添加官网说的配置 (一定要重启服务器)
   // (3): main.js 按需引入某个组件, Vue.use全局注册 - 某个.vue文件中直接使用vant组件
   import { Button } from "vant";
   Vue.use(Button); // Button组件全局注册, 真正注册的组件名VanButton
   ```

### 1.4 弹出框使用

> 目标: 使用弹出框组件

```vue
<template>
  <div>
    <van-button type="primary" @click="btn">主要按钮</van-button>
    <van-button type="info">信息按钮</van-button>
    <van-button type="default">默认按钮</van-button>
    <van-button type="warning">警告按钮</van-button>
    <van-button type="danger">危险按钮</van-button>
  </div>
</template>

<script>
// 1. 找到vant文档
// 2. 引入
// 3. 在恰当时机, 调用此函数 (还可以用组件的用法)
import { Dialog } from "vant";
export default {
  methods: {
    btn() {
      Dialog({ message: "提示", showCancelButton: true }); // 调用执行时, 页面就会出弹出框
    },
  },
};
</script>
```

## 面试题

### 1. 路由之间是怎么跳转的？有哪些方式

1、<router-link to="需要跳转到页面的路径">

2、this.$router.push()跳转到指定的 url，并在 history 中添加记录，点击回退返回到上一个页面

3、this.$router.replace()跳转到指定的 url，但是 history 中不会添加记录，点击回退到上上个页面

4、this.$touter.go(n)向前或者后跳转 n 个页面，n 可以是正数也可以是负数

### 2. vue-router 怎么配置路由

在 vue 中配置路由分为 5 个步骤，分别是：

1. 引入 vue-router.js
2. 配置路由 path 和组件, 和生成路由对象
3. 把路由对象配置到 new Vue 中 router 选项下
4. 页面使用<router-view></router-view> 承载路由
5. <router-link to="要跳转的路径"></router-link> 设置路由导航(声明式导航方式/编程式跳转)

### 3. vue-router 的钩子函数都有哪些

关于 vue-router 中的钩子函数主要分为 3 类

全局钩子函数要包含 beforeEach

beforeEach 函数有三个参数,分别是:

​ to:router 即将进入的路由对象
​ from:当前导航即将离开的路由
​ next:function,进行管道中的一个钩子，如果执行完了,则导航的状态就是 confirmed （确认的）否则为 false,终止导航。

单独路由独享组件

​ beforeEnter,

组件内钩子

beforeRouterEnter，
beforeRouterUpdate,
beforeRouterLeave

### 4. 路由传值的方式有哪几种

Vue-router 传参可以分为两大类，分别是编程式的导航 router.push 和声明式的导航

router.push

字符串：直接传递路由地址，但是不能传递参数

​ this.$router.push("home")

​ 对象：

​ 命名路由 这种方式传递参数，目标页面刷新会报错 - name+params

​ this.$router.push({name:"news",params:{userId:123})

​ 查询参数 和 path 配对的是 query

​ this.$router.push({path:"/news',query:{uersId:123})

​ 接收参数 this.$route.query

声明式导航

​ 字符串 <router-link to:"news"></router-link>

​ 命名路由 <router-link :to:"{name:'news',params:{userid:1111}}"></route-link>

​ 还可以 to="/path/值" - 需要提前在路由 规则里值 /path/:key

​ 查询参数 <router-link :to="{path:'/news',query:{userId:1111}}"></router-link>

​ 还可以 to="/path?key=value

### 5. 怎么定义 vue-router 的动态路由?怎么获取传过来的动态参数?

动态路由指的就是 path 路径上传智, 前提需要路由规则了提前配置/path/:key 名, 可以写多个用/隔开, 获取使用$route.params.key 名来提取对应用路径传过来的值

### 6. Vue 的路由实现模式：hash 模式和 history 模式（必会）

hash 模式：在浏览器中符号“#”，#以及#后面的字符称之为 hash，用 window.location.hash 读取。特点：hash 虽然在 URL 中，但不被包括在 HTTP 请求中；用来指导浏览器动作，对服务端安全无用，hash 不会重加载页面。

history 模式：history 采用 HTML5 的新特性；且提供了两个新方法： pushState()， replaceState()可以对浏览器历史记录栈进行修改，以及 popState 事件的监听到状态变更

### 7. 请说出路由配置项常用的属性及作用（必会）

​ 路由配置参数：

​ path : 跳转路径
​ component : 路径相对于的组件
​ name:命名路由
​ children:子路由的配置参数(路由嵌套)
​ props:路由解耦
​ redirect : 重定向路由

### 8. 编程式导航使用的方法以及常用的方法（必会）

​ 路由跳转 ： this.$router.push()
​    路由替换 : this.$router.replace()
​ 后退： this.$router.back()
​    前进 ：this.$router.forward()

### 9. Vue 如何去除 URL 中的#（必会）

​ vue-router 默认使用 hash 模式，所以在路由加载的时候，项目中的 URL 会自带 “#”。如果不想使用 “#”， 可以使用 vue-router 的另一种模式 history：new Router ({ mode : 'history', routes: [ ]})

​ 需要注意的是，当我们启用 history 模式的时候，由于我们的项目是一个单页面应用，所以在路由跳转的时候，就会出现访问不到静态资源而出现 “404” 的情况，这时候就需要服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 “index.html” 页面。

### 10. 说一下你在 vue 中踩过的坑（必会）

​ 1、第一个是给对象添加属性的时候，直接通过给 data 里面的对象添加属性然后赋值，新添加的属性不是响应式的

​ 【解决办法】通过 Vue.set(对象，属性，值)这种方式就可以达到，对象新添加的属性是响应式的

2、 在 created 操作 dom 的时候，是报错的，获取不到 dom，这个时候实例 vue 实例没有挂载

​ 【解决办法】通过：Vue.nextTick(回调函数进行获取)

### 11. **$route和$router 的区别？**

$route是路由信息对象，包括‘path，hash，query，fullPath，matched，name’等路由信息参数；
$router 是路由实例对象，包括了路由的跳转方法，实例对象等
