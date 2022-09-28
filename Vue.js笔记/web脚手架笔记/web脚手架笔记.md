### 1.1_Vue 是什么

==渐进式==javacript==框架==, 一套拥有自己规则的语法

官网地址: https://cn.vuejs.org/ (作者: 尤雨溪)

> ### 什么是渐进式

渐进式: 逐渐进步, 想用什么就用什么, 不必全都使用

Vue 渐进式: Vue 从基础开始, 会循序渐进向前学习, 如下知识点可能你现在不明白, 但是学完整个 vue 回过头来看, 会很有帮助

![](./image/%E6%B8%90%E8%BF%9B%E5%BC%8F.png)

> 补充概念

什么是库和框架

库: 封装的属性或方法 (例 jquery.js)

框架: 拥有自己的规则和元素, 比库强大的多 (例 vue.js)

## 2. @vue/cli 脚手架

> ### @vue/cli 的好处

- 开箱即用

  0 配置 webpack

  babel 支持

  css, less 支持

  开发服务器支持

### 2.1\_@vue/cli 安装

> 目标: 把@vue/cli 模块包按到全局, 电脑拥有 vue 命令, 才能创建脚手架工程

- 全局安装命令

```bash
yarn global add @vue/cli
# OR
npm install -g @vue/cli
```

注意: 如果半天没动静(95%都是网速问题), 可以 ctrl c

1. 停止重新来

2. 换一个网继续重来

- 查看`vue`脚手架版本

```bash
vue -V
```

> 总结: 如果出现版本号就安装成功, 否则失败

### 2.2\_@vue/cli 创建项目启动服务

> 目标: 使用 vue 命令, 创建脚手架项目

==注意: 项目名不能带大写字母, 中文和特殊符号==

1. 创建项目

```bash
# vue和create是命令, vuecli-demo是文件夹名
vue create vuecli-demo
```

2. 选择模板--是 vue2 还是 vue3

   ==可以上下箭头选择, 弄错了 ctrl+c 重来==

3. 选择用 yarn 还是 npm 方式下载脚手架项目需要的依赖包!

4. 一路回车等待生成项目文件夹+文件+下载必须的第三方包们

5. 进入脚手架项目下, 启动内置的热更新本地服务器

```bash
cd vuecil-demo

npm run serve
# 或
yarn serve
```

只要看到绿色的 - 啊. 你成功了(底层 node+webpack 热更新服务)

打开浏览器输入 local 那一行的地址

> 总结: vue 命令创建工程目录, 项目内置 webpack 本地热更新服务器, 帮我们打包项目预览项目

### 2.3 @vue/cli 目录和代码分析

> 目标: 讲解重点文件夹, 文件的作用, 以及文件里代码的意思

```bash
 vuecil-demo        # 项目目录
    ├── node_modules # 项目依赖的第三方包
    ├── public       # 静态文件目录
      ├── favicon.ico# 浏览器小图标
      └── index.html # 单页面的html文件(网页浏览的是它)
    ├── src          # 业务文件夹
      ├── assets     # 静态资源
        └── logo.png # vue的logo图片
      ├── components # 组件目录
        └── HelloWorld.vue # 欢迎页面vue代码文件
      ├── App.vue    # 整个应用的根组件
      └── main.js    # 入口js文件
    ├── .gitignore   # git提交忽略配置
    ├── babel.config.js  # babel配置
    ├── package.json  # 依赖包列表
    ├── README.md    # 项目说明
	└── yarn.lock    # 项目包版本锁定和缓存地址
```

主要文件及含义

```js
node_modules下都是下载的第三方包
public/index.html – 浏览器运行的网页
src/main.js – webpack打包的入口文件
src/App.vue – vue项目入口页面
package.json – 依赖包列表文件
```

### 2.4\_@vue/cli 项目架构了解

> 目标: 知道项目入口, 以及代码执行顺序和引入关系

![](./image/vuecli%E9%A1%B9%E7%9B%AE%E6%9E%B6%E6%9E%84.png)

### 2.5\_@vue/cli 自定义配置

> 在 vue.config.js 中自定义配置

```jsx
/* 覆盖webpack的配置 */
module.exports = {
  devServer: {
    // 自定义服务配置
    open: true, // 自动打开浏览器
    port: 3000, //端口号
  },
};
```

### 2.6_eslint 了解

> 它是一个==代码检查工具==

例子: 先在 main.js 随便声明个变量, 但是不要使用

观察发现, 终端和页面都报错了

==记住以后见到这样子的错误, 证明代码不严谨==

暂时关闭 eslint

```jsx
module.exports = {
  // ...其他配置
  lintOnSave: false, // 关闭eslint检查
};
```

在 vue.config.js 中配置后重启服务，发现不再报错。

### 2.7\_@vue/cli 单 vue 文件讲解

> 目标: 单 vue 文件好处, 独立作用域互不影响

Vue 推荐采用.vue 文件来开发项目

template 里只能有一个根标签

vue 文件-独立模块-作用域互不影响

style 配合 scoped 属性, 保证样式只针对当前 template 内标签生效

vue 文件配合 webpack, 把他们打包起来插入到 index.html

```vue
<!-- template必须, 只能有一个根标签, 影响渲染到页面的标签结构 -->
<template>
  <div>欢迎使用vue</div>
</template>

<!-- js相关 -->
<script>
export default {
  name: "App",
};
</script>

<!-- 当前组件的样式, 设置scoped, 可以保证样式只对当前页面有效 -->
<style scoped></style>
```

最终: Vue 文件配合 webpack, 把他们打包起来插入到 index.html, 然后在浏览器运行

### 2.8\_@vue/cli 欢迎界面清理

> 目标: 我们开始写我们自己的代码, 无需欢迎页面

- src/App.vue 默认有很多内容, 可以全部删除留下框
- assets 和 components 文件夹下的一切都删除掉 (不要默认的欢迎页面)

## ==3. Vue 指令==

### 3.0_vue 基础-插值表达式

> 目的: 在 dom 标签中, 直接插入内容

又叫: 声明式渲染/文本插值

语法: {{ 表达式 }}

```jsx
<template>
  <div>
    <h1>{{ msg }}</h1>
    <h2>{{ obj.name }}</h2>
    <h3>{{ obj.age > 18 ? '成年' : '未成年' }}</h3>
  </div>
</template>

<script>
export default {
  data() { // 格式固定, 定义vue数据之处
    return {  // key相当于变量名
      msg: "hello, vue",
      obj: {
        name: "小vue",
        age: 5
      }
    }
  }
}
</script>

<style>
</style>

```

> 总结: dom 中插值表达式赋值, vue 的变量必须在 data 里声明

### 3.1_vue 基础-MVVM 设计模式

> 设计模式:
> 是一套被反复使用的、多数人知晓的、经过分类编目的、代码设计经验的总结。

- MVVM，一种软件架构模式，决定了写代码的思想和层次
  - M： model 数据模型 (data 里定义)
  - V： view 视图 （html 页面）
  - VM： ViewModel 视图模型 (vue.js 源码)

* MVVM 通过`数据双向绑定`让数据自动地双向同步 **不再需要操作 DOM**
  - V（修改视图） -> M（数据自动同步）
  - M（修改数据） -> V（视图自动同步）

![](./image/MVVM.png)

**1. 在 vue 中，不推荐直接手动操作 DOM！！！**

**2. 在 vue 中，通过数据驱动视图，不要在想着怎么操作 DOM，而是想着如何操作数据！！**(思想转变)

![](./image/双向数据绑定.png)

> 总结: vue 源码内采用 MVVM 设计模式思想, 大大减少了 DOM 操作, 挺高开发效率

### 3.2_vue 指令-v-bind

> 目标: 给标签属性设置 vue 变量的值

**vue 指令, 实质上就是特殊的 html 标签属性, 特点: v- 开头**

每个指令, 都有独立的作用

- 语法：`v-bind:属性名="vue变量"`
- 简写：`:属性名="vue变量"`

```html
<!-- vue指令-v-bind属性动态赋值 -->
<a v-bind:href="url">我是a标签</a>
<img :src="imgSrc" />
```

> 完整示例代码：

```js

<template>
  <div>
    <img :src="imgurl" />
    <a v-bind:href="url">点击去百度</a>
    <div>{{ msg }}</div>
    <div>{{ obj.name }}</div>
    <div>{{ obj.age >= 18 ? "成年" : "非成年" }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      url: "http://www.baidu.com",

      imgurl:
        "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.jj20.com%2Fup%2Fallimg%2Ftp09%2F21031FKU44S6-0-lp.jpg&refer=http%3A%2F%2Fimg.jj20.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1665047088&t=8997943fb2f5b7382a9d7e217d9c726e",
      msg: "hello",
      obj: {
        name: "小vue",
        age: 5,
      },
    };
  },
};
</script>
```

> 总结: 把 vue 变量的值, 赋予给 dom 属性上, 影响标签显示效果

### 3.3_vue 指令-v-on

> 目标: 给标签绑定事件

- 语法
  - v-on:事件名="要执行的==少量代码=="
  - v-on:事件名="methods 中的函数"
  - v-on:事件名="methods 中的函数(实参)"
- 简写: @事件名="methods 中的函数"

```html
<!-- vue指令:   v-on事件绑定-->
<p>你要买商品的数量: {{count}}</p>
<button v-on:click="count = count + 1">增加1</button>
<button v-on:click="addFn">增加1个</button>
<button v-on:click="addCountFn(5)">一次加5件</button>

<button @click="subFn">减少</button>

<script>
  export default {
    // ...其他省略
    methods: {
      addFn() {
        // this代表export default后面的组件对象(下属有data里return出来的属性)
        this.count++;
      },
      addCountFn(num) {
        this.count += num;
      },
      subFn() {
        this.count--;
      },
    },
  };
</script>
```

> 总结: 常用@事件名, 给 dom 标签绑定事件, 以及=右侧事件处理函数

### 3.4_vue 指令-v-on 事件对象

> 目标: vue 事件处理函数中, 拿到事件对象

- 语法:
  - 无传参, 通过形参直接接收
  - 传参, 通过$event 指代事件对象传给事件处理函数

```vue
<template>
  <div>
    <a @click="one" href="http://www.baidu.com">阻止百度</a>
    <hr />
    <a @click="two(10, $event)" href="http://www.baidu.com">阻止去百度</a>
  </div>
</template>

<script>
export default {
  methods: {
    one(e) {
      e.preventDefault();
    },
    two(num, e) {
      e.preventDefault();
    },
  },
};
</script>
```

### 3.5_vue 指令-v-on 修饰符

> 目的: 在事件后面.修饰符名 - 给事件带来更强大的功能

- 语法:
  - @事件名.修饰符="methods 里函数"
    - .stop - 阻止事件冒泡
    - .prevent - 阻止默认行为
    - .once - 程序运行期间, 只触发一次事件处理函数

```html
<template>
  <div @click="fatherFn">
    <!-- vue对事件进行了修饰符设置, 在事件后面.修饰符名即可使用更多的功能 -->
    <button @click.stop="btn">.stop阻止事件冒泡</button>
    <a href="http://www.baidu.com" @click.prevent="btn">.prevent阻止默认行为</a>
    <button @click.once="btn">.once程序运行期间, 只触发一次事件处理函数</button>
  </div>
</template>

<script>
  export default {
    methods: {
      fatherFn() {
        console.log("father被触发");
      },
      btn() {
        console.log(1);
      },
    },
  };
</script>
```

> 总结: 修饰符给事件扩展额外功能

### 3.6_vue 指令-v-on 按键修饰符

> 目标: 给键盘事件, 添加修饰符, 增强能力

- 语法:
  - @keyup.enter - 监测回车按键
  - @keyup.esc - 监测返回按键

```html
<template>
  <div>
    <input type="text" @keydown.enter="enterFn" />
    <hr />
    <input type="text" @keydown.esc="escFn" />
  </div>
</template>

<script>
  export default {
    methods: {
      enterFn() {
        console.log("enter回车按键了");
      },
      escFn() {
        console.log("esc按键了");
      },
    },
  };
</script>
```

> 总结: 多使用事件修饰符, 可以提高开发效率, 少去自己判断过程

### 3.7_vue 指令 v-model

> 目标: 把 value 属性和 vue 数据变量, 双向绑定到一起

- 语法: v-model="vue 数据变量"
- 双向数据绑定
  - 数据变化 -> 视图自动同步
  - 视图变化 -> 数据自动同步
- 演示: 用户名绑定 - vue 内部是 MVVM 设计模式

```vue
<template>
  <div>
    <!-- 
    	v-model:是实现vuejs变量和表单标签value属性, 双向绑定的指令
    -->
    <div>
      <span>用户名:</span>
      <input type="text" v-model="username" />
    </div>
    <div>
      <span>密码:</span>
      <input type="password" v-model="pass" />
    </div>
    <div>
      <span>来自于: </span>
      <!-- 下拉菜单要绑定在select上 -->
      <select v-model="from">
        <option value="北京市">北京</option>
        <option value="南京市">南京</option>
        <option value="天津市">天津</option>
      </select>
    </div>
    <div>
      <!-- (重要)
      遇到复选框, v-model的变量值
      非数组 - 关联的是复选框的checked属性
      数组   - 关联的是复选框的value属性
       -->
      <span>爱好: </span>
      <input type="checkbox" v-model="hobby" value="抽烟" />抽烟
      <input type="checkbox" v-model="hobby" value="喝酒" />喝酒
      <input type="checkbox" v-model="hobby" value="写代码" />写代码
    </div>
    <div>
      <span>性别: </span>
      <input type="radio" value="男" name="sex" v-model="gender" />男
      <input type="radio" value="女" name="sex" v-model="gender" />女
    </div>
    <div>
      <span>自我介绍</span>
      <textarea v-model="intro"></textarea>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: "",
      pass: "",
      from: "",
      hobby: "", //变量为非数组，绑定的是checkout属性--true or false。
      hobby: [], //变量为数组，绑定的是value属性，收集勾选的值。
      sex: "",
      intro: "",
    };
    // 总结:
    // 特别注意: v-model, 在input[checkbox]的多选框状态
    // 变量为非数组, 则绑定的是checked的属性(true/false) - 常用于: 单个绑定使用
    // 变量为数组, 则绑定的是他们的value属性里的值 - 常用于: 收集勾选了哪些值
  },
};
</script>
```

> 总结: 本阶段 v-model 只能用在表单元素上

### 3.9_vue 指令 v-model 修饰符

> 目标: 让 v-model 拥有更强大的功能

- 语法:
  - v-model.修饰符="vue 数据变量"
    - .number 以 parseFloat 转成数字类型
    - .trim 去除首尾空白字符
    - .lazy 在 change 时触发而非 inupt 时

```vue
<template>
  <div>
    <div>
      <span>年龄:</span>
      <input type="text" v-model.number="age" />
    </div>
    <div>
      <span>人生格言:</span>
      <input type="text" v-model.trim="motto" />
    </div>
    <div>
      <span>自我介绍:</span>
      <textarea v-model.lazy="intro"></textarea>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      age: "",
      motto: "",
      intro: "",
    };
  },
};
</script>
```

> 总结: v-model 修饰符, 可以对值进行预处理, 非常高效好用

### 3.10_vue 指令 v-text 和 v-html

> 目的: 更新 DOM 对象的 innerText/innerHTML

- 语法:
  - v-text="vue 数据变量"
  - v-html="vue 数据变量"
- 注意: 会覆盖插值表达式

```vue
<template>
  <div>
    <p v-text="str">{{ msg }}</p>
    <p v-html="str">{{ msg }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      msg: "hello", //这个msg不会显示，因为v-text或者v-html会覆盖插值表达式。
      str: "<span>我是一个span标签</span>",
    };
  },
};
</script>
```

> 总结: v-text 把值当成普通字符串显示, v-html 把值当做 html 解析

### 3.11_vue 指令 v-show 和 v-if

> 目标: 控制标签的隐藏或出现

- 语法:
  - v-show="vue 变量"
  - v-if="vue 变量"
- 原理
  - v-show 用的 display:none 隐藏 (频繁切换使用)
  - v-if 直接从 DOM 树上移除
- 高级
  - v-else 使用

```html
<template>
  <div>
    <h1 v-show="isOk">v-show的盒子</h1>
    <h1 v-if="isOk">v-if的盒子</h1>

    <div>
      <p v-if="age > 18">我成年了</p>
      <p v-else>还得多吃饭</p>
      <!-- 它俩成对出现，判断后执行为true的语句 -->
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        isOk: true,
        age: 15,
      };
    },
  };
</script>
```

> 总结: 使用 v-show 和 v-if 以及 v-else 指令, 方便通过变量控制一套标签出现/隐藏

### 3.13_vue 指令-v-for

> 目标: 列表渲染, 所在标签结构, 按照数据数量, 循环生成

- 语法
  - v-for="(值, 索引) in 目标结构"
  - v-for="值 in 目标结构"
- 目标结构:

  - 可以遍历数组 / 对象 / 数字 / 字符串 (可遍历结构)

- 注意:

  v-for 的临时变量名不能用到 v-for 范围外

```vue
<template>
  <div id="app">
    <div id="app">
      <!-- v-for 把一组数据, 渲染成一组DOM -->
      <!-- 口诀: 让谁循环生成, v-for就写谁身上 -->
      <p>学生姓名</p>
      <ul>
        <li v-for="(item, index) in arr" :key="index">
          {{ index }} - {{ item }}
        </li>
      </ul>

      <p>学生详细信息</p>
      <ul>
        <li v-for="obj in stuArr" :key="obj.id">
          <span>{{ obj.name }}</span>
          <span>{{ obj.sex }}</span>
          <span>{{ obj.hobby }}</span>
        </li>
      </ul>

      <!-- v-for遍历对象(了解) -->
      <p>老师信息</p>
      <div v-for="(value, key) in tObj" :key="value">
        {{ key }} -- {{ value }}
      </div>

      <!-- v-for遍历整数(了解) - 从1开始 -->
      <p>序号</p>
      <div v-for="i in count" :key="i">{{ i }}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      arr: ["小明", "小欢欢", "大黄"],
      stuArr: [
        {
          id: 1001,
          name: "孙悟空",
          sex: "男",
          hobby: "吃桃子",
        },
        {
          id: 1002,
          name: "猪八戒",
          sex: "男",
          hobby: "背媳妇",
        },
      ],
      tObj: {
        name: "小黑",
        age: 18,
        class: "1期",
      },
      count: 10,
    };
  },
};
</script>
```

> 总结: vue 最常用指令, 铺设页面利器, 快速把数据赋予到相同的 dom 结构上循环生成

## 面试题

### 1. Vue 的最大优势是什么?

​ 简单易学, 轻量级整个源码 js 文件不大, 双向数据绑定, 数据驱动视图, 组件化, 数据和视图分离,

​ vue 负责关联视图和数据, 作者中国人(尤雨溪), 文档都是中文的, 入门教程非常多, 上手简单.

​ 相比传统网页, vue 是单页面可以只刷新某一部分

### 2. Vue 和 jQuery 区别是什么?

​ jQuery 应该算是一个插件, 里面封装了各种易用的方法, 方便你使用更少的代码来操作 dom 标签

​ Vue 是一套框架, 有自己的规则和体系与语法, 特别是设计思想 MVVM, 让数据和视频关联绑定, 省略了很多 DOM 操作. 然后指令还给标签注入了更多的功能

### 3. mvvm 和 mvc 区别是什么?

​ MVC: 也是一种设计模式, 组织代码的结构, 是 model 数据模型, view 视图, Controller 控制器, 在控制器这层里编写 js 代码, 来控制数据和视图关联

​ MVVM: 即 Model-View-ViewModel 的简写。即模型-视图-视图模型, VM 是这个设计模式的核心, 连接 v 和 m 的桥梁, 内部会监听 DOM 事件, 监听数据对象变化来影响对方. 我们称之为数据绑定

### 4. Vue 常用修饰符有哪些?

​ .prevent: 提交事件不再重载页面；

​ .stop: 阻止单击事件冒泡；

​ .once: 只执行一次这个事件

### 5. Vue2.x 兼容 IE 哪个版本以上

​ 不支持 ie8 及以下，部分兼容 ie9 ，完全兼容 10 以上， 因为 vue 的响应式原理是基于 es5 的 Object.defineProperty(),而这个方法不支持 ie8 及以下。

### 6. 对 Vue 渐进式的理解

​ 渐进式代表的含义是：主张最少, 自底向上, 增量开发, 组件集合, 便于复用

### 7. v-show 和 v-if 的区别

​ v-show 和 v-if 的区别? 分别说明其使用场景?

​ v-show 和 v-if 都是 true 的时候显示，false 的时候隐藏

​ 但是：false 的情况下，

​ v-show 是采用的 display:none

​ v-if 采用惰性加载

​ 如果需要频繁切换显示隐藏需要使用 v-show

### 8. 说出至少 4 个 Vue 指令及作用

​ v-for 根据数组的个数, 循环数组元素的同时还生成所在的标签

​ v-show 显示内容

​ v-if 显示与隐藏

​ v-else 必须和 v-if 连用 不能单独使用 否则报错

​ v-bind 动态绑定 作用： 及时对页面的数据进行更改, 可以简写成:分号

​ v-on 给标签绑定函数，可以缩写为@，例如绑定一个点击函数 函数必须写在 methods 里面

​ v-text 解析文本

​ v-html 解析 html 标签

### 9. 为什么避免 v-for 和 v-if 在一起使用

​ Vue 处理指令时，v-for 比 v-if 具有更高的优先级, 虽然用起来也没报错好使, 但是性能不高, 如果你有 5 个元素被 v-for 循环, v-if 也会分别执行 5 次.
