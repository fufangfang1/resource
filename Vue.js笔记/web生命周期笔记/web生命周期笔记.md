## 1. vue 生命周期

### 1.1\_钩子函数

> 目标: **Vue** 框架内置函数，随着组件的生命周期阶段，自动执行

作用: 特定的时间点，执行特定的操作

场景: 组件创建完毕后，可以在 created 生命周期函数中发起 Ajax 请求，从而初始化 data 数据

分类: 4 大阶段 8 个方法

- 初始化
- 挂载
- 更新
- 销毁

| **阶段** | **方法名**    | **方法名** |
| -------- | ------------- | ---------- |
| 初始化   | beforeCreate  | created    |
| 挂载     | beforeMount   | mounted    |
| 更新     | beforeUpdate  | updated    |
| 销毁     | beforeDestroy | destroyed  |

[官网文档](https://cn.vuejs.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%BE%E7%A4%BA)

下图展示了实例的生命周期：

![生命周期](./image/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

### 1.2\_初始化阶段

> 含义讲解:

1.new Vue() – Vue 实例化(组件也是一个小的 Vue 实例)

2.Init Events & Lifecycle – 初始化事件和生命周期函数

3.beforeCreate – 生命周期钩子函数被执行

4.Init injections&reactivity – Vue 内部添加 data 和 methods 等

5.created – 生命周期钩子函数被执行, 实例创建

6.接下来是编译模板阶段 –开始分析

7.Has el option? – 是否有 el 选项 – 检查要挂到哪里

​ 没有. 调用$mount()方法

​ 有, 继续检查 template 选项

components/Life.vue - 创建一个文件

```html
<script>
  export default {
    data() {
      return {
        msg: "hello, Vue",
      };
    },
    // 一. 初始化
    // new Vue()以后, vue内部给实例对象添加了一些属性和方法, data和methods初始化"之前"
    beforeCreate() {
      console.log("beforeCreate -- 执行");
      console.log(this.msg); // undefined
    },
    // data和methods初始化以后
    // 场景: 网络请求, 注册全局事件
    created() {
      console.log("created -- 执行");
      console.log(this.msg); // hello, Vue

      this.timer = setInterval(() => {
        console.log("哈哈哈");
      }, 1000);
    },
  };
</script>
```

App.vue - 引入使用

```vue
<template>
  <div>
    <h1>1. 生命周期</h1>
    <Life></Life>
  </div>
</template>

<script>
import Life from "./components/Life";
export default {
  components: {
    Life,
  },
};
</script>
```

### 1.3\_挂载阶段

> 含义讲解:

1.template 选项检查

​ 有 - 编译 template 返回 render 渲染函数

​ 无 – 编译 el 选项对应标签作为 template(要渲染的模板)

2.虚拟 DOM 挂载成真实 DOM 之前

3.beforeMount – 生命周期钩子函数被执行

4.Create … – 把虚拟 DOM 和渲染的数据一并挂到真实 DOM 上

5.真实 DOM 挂载完毕

6.mounted – 生命周期钩子函数被执行

components/Life.vue - 创建一个文件

```html
<template>
  <div>
    <p>学习生命周期 - 看控制台打印</p>
    <p id="myP">{{ msg }}</p>
  </div>
</template>

<script>
  export default {
    // ...省略其他代码

    // 二. 挂载
    // 真实DOM挂载之前
    // 场景: 预处理data, 不会触发updated钩子函数
    beforeMount() {
      console.log("beforeMount -- 执行");
      console.log(document.getElementById("myP")); // null

      this.msg = "重新值";
    },
    // 真实DOM挂载以后
    // 场景: 挂载后真实DOM
    mounted() {
      console.log("mounted -- 执行");
      console.log(document.getElementById("myP")); // p
    },
  };
</script>
```

### 1.4\_更新阶段

> 含义讲解:

1.当 data 里数据改变, 更新 DOM 之前

2.beforeUpdate – 生命周期钩子函数被执行

3.Virtual DOM…… – 虚拟 DOM 重新渲染, 打补丁到真实 DOM

4.updated – 生命周期钩子函数被执行

5.当有 data 数据改变 – 重复这个循环

components/Life.vue - 创建一个文件

准备 ul+li 循环, 按钮添加元素, 触发 data 改变->导致更新周期开始

```html
<template>
  <div>
    <p>学习生命周期 - 看控制台打印</p>
    <p id="myP">{{ msg }}</p>
    <ul id="myUL">
      <li v-for="(val, index) in arr" :key="index">{{ val }}</li>
    </ul>
    <button @click="arr.push(1000)">点击末尾加值</button>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        msg: "hello, Vue",
        arr: [5, 8, 2, 1],
      };
    },
    // ...省略其他代码

    // 三. 更新
    // 前提: data数据改变才执行
    // 更新之前
    beforeUpdate() {
      console.log("beforeUpdate -- 执行");
      console.log(document.querySelectorAll("#myUL>li")[4]); // undefined
    },
    // 更新之后
    // 场景: 获取更新后的真实DOM
    updated() {
      console.log("updated -- 执行");
      console.log(document.querySelectorAll("#myUL>li")[4]); // li
    },
  };
</script>
```

### 1.5\_销毁阶段

> 含义讲解:

1.当$destroy()被调用 – 比如组件 DOM 被移除(例 v-if)

2.beforeDestroy – 生命周期钩子函数被执行

3.拆卸数据监视器、子组件和事件侦听器

4.实例销毁后, 最后触发一个钩子函数

5.destroyed – 生命周期钩子函数被执行

components/Life.vue - 准备生命周期方法(Life 组件即将要被删除)

```html
<script>
  export default {
    // ...省略其他代码

    // 四. 销毁
    // 前提: v-if="false" 销毁Vue实例
    // 场景: 移除全局事件, 移除当前组件, 计时器, 定时器, eventBus移除事件$off方法
    beforeDestroy() {
      // console.log('beforeDestroy -- 执行');
      clearInterval(this.timer);
    },
    destroyed() {
      // console.log("destroyed -- 执行");
    },
  };
</script>
```

主要: App.vue - 点击按钮让 Life 组件从 DOM 上移除 -> 导致 Life 组件进入销毁阶段

```vue
<Life v-if="show"></Life>
<button @click="show = false">销毁组件</button>

<script>
data(){
    return {
        show: true
    }
},
</script>
```

## 2. axios

### 2.0_axios 基本使用

> 特点

- 支持客户端发送 Ajax 请求
- 支持服务端 Node.js 发送请求
- 支持 Promise 相关用法
- 支持请求和响应的拦截器功能
- 自动转换 JSON 数据
- axios 底层还是原生 js 实现, 内部通过 Promise 封装的

> axios 的基本使用

```js
axios({
  method: "请求方式", // get post
  url: "请求地址",
  data: {
    // 拼接到请求体的参数,  post请求的参数
    xxx: xxx,
  },
  params: {
    // 拼接到请求行的参数, get请求的参数
    xxx: xxx,
  },
})
  .then((res) => {
    console.log(res.data); // 后台返回的结果
  })
  .catch((err) => {
    console.log(err); // 后台报错返回
  });
```

### 2.1_axios 基本使用-获取数据

> 目标: 调用文档最后\_获取所有图书信息接口

功能: 点击调用后台接口, 拿到所有数据 – 打印到控制台

接口: 参考预习资料.md – 接口文档

引入: 下载 axios, 引入后才能使用

例子如下:

components/UseAxios.vue

```vue
<template>
  <div>
    <p>1. 获取所有图书信息</p>
    <button @click="getAllFn">点击-查看控制台</button>
  </div>
</template>

<script>
// 目标1: 获取所有图书信息
// 1. 下载axios
// 2. 引入axios
// 3. 发起axios请求
import axios from "axios";
export default {
  methods: {
    getAllFn() {
      axios({
        url: "http://123.57.109.30:3006/api/getbooks",
        method: "GET", // 默认就是GET方式请求, 可以省略不写
      }).then((res) => {
        console.log(res);
      });
      // axios()-原地得到Promise对象
    },
  },
};
</script>
```

### 2.2_axios 基本使用-传参

> 目标: 调用接口-获取某本书籍信息

功能: 点击调用后台接口, 查询用户想要的书籍信息 – 打印到控制台

例子如下:

components/UseAxios.vue

```vue
<template>
  <div>
    <p>2. 查询某本书籍信息</p>
    <input type="text" placeholder="请输入要查询 的书名" v-model="bName" />
    <button @click="findFn">查询</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      bName: "",
    };
  },
  methods: {
    // ...省略了查询所有的代码
    findFn() {
      axios({
        url: "/api/getbooks",
        method: "GET",
        params: {
          // 都会axios最终拼接到url?后面
          bookname: this.bName,
        },
      }).then((res) => {
        console.log(res);
      });
    },
  },
};
</script>
```

### 2.3_axios 基本使用-发布书籍

> 目标: 完成发布书籍功能

功能: 点击新增按钮, 把用户输入的书籍信息, 传递给后台 – 把结果打印在控制台

例子如下:

components/UseAxios.vue

```vue
<template>
  <div>
    <p>3. 新增图书信息</p>
    <div>
      <input type="text" placeholder="书名" v-model="bookObj.bookname" />
    </div>
    <div>
      <input type="text" placeholder="作者" v-model="bookObj.author" />
    </div>
    <div>
      <input type="text" placeholder="出版社" v-model="bookObj.publisher" />
    </div>
    <button @click="sendFn">发布</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      bName: "",
      bookObj: {
        // 参数名提前和后台的参数名对上-发送请求就不用再次对接了
        bookname: "",
        author: "",
        publisher: "",
      },
    };
  },
  methods: {
    // ...省略了其他代码
    sendFn() {
      axios({
        url: "/api/addbook",
        method: "POST",
        data: {
          appkey: "7250d3eb-18e1-41bc-8bb2-11483665535a",
          ...this.bookObj,
          // 等同于下面
          // bookname: this.bookObj.bookname,
          // author: this.bookObj.author,
          // publisher: this.bookObj.publisher
        },
      });
    },
  },
};
</script>
```

### 2.4_axios 基本使用-全局配置

> 目标: 避免前缀基地址, 暴露在逻辑页面里, 统一设置

在 main.js 里面配置全局

```js
// 引入包
import axios from "axios";
// 设置根地址
axios.defaults.baseURL = "http://123.57.109.30:3006"



// 所有请求的url前置可以去掉, 请求时, axios会自动拼接baseURL的地址在前面
getAllFn() {
    axios({
        url: "/api/getbooks",
        method: "GET", // 默认就是GET方式请求, 可以省略不写
    }).then((res) => {
        console.log(res);
    });
    // axios()-原地得到Promise对象
},
```

## 3. $nextTick和$refs 知识

### 3.0 $refs-获取 DOM

> 目标: 利用 ref 和 $refs 可以用于获取 dom 元素

components/More.vue

```vue
<template>
  <div>
    <p>1. 获取原生DOM元素</p>
    <h1 id="h" ref="myH">我是一个孤独可怜又能吃的h1</h1>
  </div>
</template>

<script>
// 目标: 获取组件对象
// 1. 创建组件/引入组件/注册组件/使用组件
// 2. 组件起别名ref
// 3. 恰当时机, 获取组件对象
export default {
  mounted() {
    console.log(document.getElementById("h")); // h1
    console.log(this.$refs.myH); // h1
  },
};
</script>

<style></style>
```

> 总结: 通过 id / ref, 都可以获取原生 DOM 标签

### 3.1 $refs-获取组件对象

> 目标: 获取组件对象, 调用组件里方法

components/Child/Demo.vue

```vue
<template>
  <div>
    <p>我是Demo组件</p>
  </div>
</template>

<script>
export default {
  methods: {
    fn() {
      console.log("demo组件内的方法被调用了");
    },
  },
};
</script>
```

More.vue - 获取组件对象 - 调用组件方法

```vue
<template>
  <div>
    <p>2. 获取组件对象 - 可调用组件内一切</p>
    <Demo ref="de"></Demo>
  </div>
</template>

<script>
// 目标: 获取组件对象
// 1. 创建组件/引入组件/注册组件/使用组件
// 2. 组件起别名ref
// 3. 恰当时机, 获取组件对象
import Demo from "./Child/Demo";
export default {
  mounted() {
    let demoObj = this.$refs.de;
    demoObj.fn();
  },
  components: {
    Demo,
  },
};
</script>
```

> 总结: ref 定义值, 通过$refs.值 来获取组件对象, 就能继续调用组件内的变量

### 3.2 $nextTick 使用

> #### Vue 更新 DOM-异步的

> 目标: 点击 count++, 马上通过"原生 DOM"拿标签内容, 无法拿到新值

components/Move.vue - 继续新增第三套代码

```vue
<template>
  <div>
    <p>3. vue更新DOM是异步的</p>
    <p ref="myP">{{ count }}</p>
    <button @click="btn">点击count+1, 马上提取p标签内容</button>
  </div>
</template>

<script>
// 目标: 获取组件对象
// 1. 创建组件/引入组件/注册组件/使用组件
// 2. 组件起别名ref
// 3. 恰当时机, 获取组件对象
import Demo from "./Child/Demo";
export default {
  components: {
    Demo,
  },
  data() {
    return {
      count: 0,
    };
  },
  methods: {
    btn() {
      this.count++; // vue监测数据更新, 开启一个DOM更新队列(异步任务)
      console.log(this.$refs.myP.innerHTML); // 0

      // 原因: Vue更新DOM异步
      // 解决: this.$nextTick()
      // 过程: DOM更新完会挨个触发$nextTick里的函数体
      this.$nextTick(() => {
        console.log(this.$refs.myP.innerHTML); // 1
      });
    },
  },
};
</script>
```

> 总结: 因为 DOM 更新是异步的

### 3.3 $nextTick 使用场景

> 目标: 点击搜索按钮, 弹出聚焦的输入框, 按钮消失

components/Tick.vue

```vue
<template>
  <div>
    <input ref="myInp" type="text" placeholder="这是一个输入框" v-if="isShow" />
    <button v-else @click="btn">点击我进行搜索</button>
  </div>
</template>

<script>
// 目标: 点按钮(消失) - 输入框出现并聚焦
// 1. 获取到输入框
// 2. 输入框调用事件方法focus()达到聚焦行为
export default {
  data() {
    return {
      isShow: false,
    };
  },
  methods: {
    async btn() {
      this.isShow = true;
      // this.$refs.myInp.focus()
      // 原因: data变化更新DOM是异步的
      // 输入框还没有挂载到真实DOM上
      // 解决:
      // this.$nextTick(() => {
      //     this.$refs.myInp.focus()
      // })
      // 扩展: await取代回调函数
      // $nextTick()原地返回Promise对象
      await this.$nextTick();
      this.$refs.myInp.focus();
    },
  },
};
</script>
```

### 3.4 组件 name 属性使用

> 目标: 可以用组件的 name 属性值, 来注册组件名字

问题: 组件名不是可以随便写的?

答案: 我们封装的组件-可以自己定义 name 属性组件名-让使用者有个统一的前缀风格

components/Com.vue

```vue
<template>
  <div>
    <p>我是一个Com组件</p>
  </div>
</template>

<script>
export default {
  name: "ComNameHaHa", // 注册时可以定义自己的名字
};
</script>
```

App.vue - 注册和使用

```vue
<template>
  <div>
    <h1>5. 组件对象里name属性</h1>
    <ComNameHaHa></ComNameHaHa>
  </div>
</template>

<script>
import Com from "./components/Com";
export default {
  data() {
    return {
      show: true,
    };
  },
  components: {
    [Com.name]: Com, // 对象里的key是变量的话[]属性名表达式
    // "ComNameHaHa": Com
  },
};
</script>
```

## 面试题

### 1、Vue 的 nextTick 的原理是什么?

​ \1. 为什么需要 nextTick ，Vue 是异步修改 DOM 的并且不鼓励开发者直接接触 DOM，但有时候业务需要必须对数据更改--刷新后的 DOM 做相应的处理，这时候就可以使用 Vue.nextTick(callback)这个 api 了。

​ \2. 理解原理前的准备 首先需要知道事件循环中宏任务和微任务这两个概念,常见的宏任务有 script, setTimeout, setInterval, setImmediate, I/O, UI rendering 常见的微任务有 process.nextTick(Nodejs),Promise.then(), MutationObserver;

​ \3. 理解 nextTick 的原理正是 vue 通过异步队列控制 DOM 更新和 nextTick 回调函数先后执行的方式。如果大家看过这部分的源码，会发现其中做了很多 isNative()的判断，因为这里还存在兼容性优雅降级的问题。可见 Vue 开发团队的深思熟虑，对性能的良苦用心。

### 2、vue 生命周期总共分为几个阶段？（必会）

Vue 实例从创建到销毁的过程，就是生命周期。也就是从开始创建、初始化数据、编译模板、挂载 Dom→ 渲染、更新 → 渲染、卸载等一系列过程，我们称这是 Vue 的生命周期。

**1\*\***）beforeCreate\*\*

​ 在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

**2\*\***）created\*\*

​ 在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)， 属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，$el 属性目前不可见。

**3\*\***）beforeMount\*\*

​ 在挂载开始之前被调用：相关的 render 函数首次被调用。

**4\*\***）mounted\*\*

​ el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 mounted 被调用时 vm.$el 也在文档内。

**5\*\***）beforeUpdate\*\*

​ 数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行。

**6\*\***）updated\*\*

​ 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

**7\*\***）activated\*\*

​ keep-alive 组件激活时调用。该钩子在服务器端渲染期间不被调用。

**8\*\***）deactivated\*\*

​ keep-alive 组件停用时调用。该钩子在服务器端渲染期间不被调用。

**9\*\***）beforeDestroy\*\*

​ 实例销毁之前调用。在这一步，实例仍然完全可用。该钩子在服务器端渲染期间不被调用。

**10\*\***）destroyed\*\*

​ Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。

**11\*\***）errorCaptured（2.5.0+ 新增）\*\*

​ 当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 false 以阻止该错误继续向上传播。

### 3、第一次加载页面会触发哪几个钩子函数？（必会）

当页面第一次页面加载时会触发 beforeCreate, created, beforeMount, mounted 这几个钩子函数
