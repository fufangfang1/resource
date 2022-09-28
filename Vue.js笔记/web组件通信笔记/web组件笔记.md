## 1. vue 组件

### 1.1*vue 组件*概念

> 组件是可复用的 Vue 实例, 封装标签, 样式和 JS 代码

**组件化** ：封装的思想，把页面上 `可重用的部分` 封装为 `组件`，从而方便项目的 开发 和 维护

**一个页面， 可以拆分成一个个组件，一个组件就是一个整体, 每个组件可以有自己独立的 结构 样式 和 行为(html, css 和 js)**

![](./image/%E7%BB%84%E4%BB%B6.png)

### 1.2*vue 组件*基础使用

> 目标: 每个组件都是一个独立的个体, 代码里体现为一个独立的.vue 文件

口诀: 哪部分标签复用, 就把哪部分封装到组件内

==(重要): 组件内 template 只能有一个根标签==

==(重要): 组件内 data 必须是一个函数, 独立作用域==

步骤:

1. 创建组件 components/Pannel.vue

> 封装标签+样式+js - 组件都是独立的, 为了复用

2. 注册组件: 创建后需要注册后再使用

> ### 全局 - 注册使用

全局入口在 main.js, 在 new Vue 之上注册

语法:

```js
import Vue from "vue";
import 组件对象 from "vue文件路径";

Vue.component("组件名", 组件对象);
```

main.js - 演示

```js
// 目标: 全局注册 (一处定义到处使用)
// 1. 创建组件 - 文件名.vue
// 2. 引入组件
import Pannel from "./components/Pannel";
// 3. 全局 - 注册组件
/*
  语法: 
  Vue.component("组件名", 组件对象)
*/
Vue.component("PannelG", Pannel);
```

全局注册 PannelG 组件名后, 就可以当做标签在任意 Vue 文件中 template 里用

单双标签都可以或者小写加-形式, 运行后, 会把这个自定义标签当做组件解析, 使用==组件里封装的标签替换到这个位置==

```vue
<PannelG></PannelG>
<PannelG />
<pannel-g></pannel-g>
```

> ### 局部 - 注册使用

语法:

```js
import 组件对象 from "vue文件路径";

export default {
  components: {
    组件名: 组件对象,
  },
};
```

> 任意 vue 文件中中引入, 注册, 使用

```vue
<template>
  <div id="app">
    <h3>案例：折叠面板</h3>
    <!-- 4. 组件名当做标签使用 -->
    <!-- <组件名></组件名> -->
    <PannelG></PannelG>
    <PannelL></PannelL>
  </div>
</template>

<script>
// 目标: 局部注册 (用的多)
// 1. 创建组件 - 文件名.vue
// 2. 引入组件
import Pannel from "./components/Pannel_1";
export default {
  // 3. 局部 - 注册组件
  /*
    语法: 
    components: {
      "组件名": 组件对象
    }
  */
  components: {
    PannelL: Pannel,
  },
};
</script>
```

组件使用总结:

1. (创建)封装 html+css+vue 到独立的.vue 文件中
2. (引入注册)组件文件 => 得到组件配置对象
3. (使用)当前页面当做标签使用

### 1.3_vue 组件-scoped 作用

> 目的: 解决多个组件样式名相同, 冲突问题

需求: div 标签名选择器, 设置背景色

问题: 发现组件里的 div 和外面的 div 都生效了

解决: 给 Pannel.vue 组件里 style 标签上加 scoped 属性即可

```vue
<style scoped>
```

在 style 上加入 scoped 属性, 就会在此组件的标签上加上一个随机生成的 data-v 开头的属性

而且必须是当前组件的元素, 才会有这个自定义属性, 才会被这个样式作用到

> 总结: style 上加 scoped, 组件内的样式只在当前 vue 组件生效

## 2. vue 组件通信

> 父子组件

父: 使用其他组件的 vue 文件

子: 被引入的组件(嵌入)

例如: App.vue(父) MyProduct.vue(子)

### 2.0*vue 组件通信*父向子-props

> 目的: 从外面给组件内传值

需求: 封装一个商品组件 MyProduct.vue - 外部传入具体要显示的数据

步骤:

1. 创建组件 components/MyProduct.vue - 复制下面标签

2. 组件内在 props 定义变量, 用于接收外部传入的值

3. App.vue 中引入注册组件, 使用时, 传入具体数据给组件显示

components/MyProduct.vue - 准备标签

```vue
<template>
  <div class="my-product">
    <h3>标题: {{ title }}</h3>
    <p>价格: {{ price }}元</p>
    <p>{{ intro }}</p>
  </div>
</template>

<script>
export default {
  // 组件内在props定义变量, 用于接收外部传入的值
  props: ["title", "price", "intro"],
};
</script>

<style>
.my-product {
  width: 400px;
  padding: 20px;
  border: 2px solid #000;
  border-radius: 5px;
  margin: 10px;
}
</style>
```

App.vue 中使用并传入数据

```vue
<template>
  <div>
    <!-- 
      目标: 父(App.vue) -> 子(MyProduct.vue) 分别传值进入
      需求: 每次组件显示不同的数据信息
      步骤(口诀):
        1. 子组件 - props - 变量 (准备接收)
        2. 父组件 - 传值进去
     -->
    <Product
      title="好吃的口水鸡"
      price="50"
      intro="开业大酬宾, 全场8折"
    ></Product>
    <Product
      title="好可爱的可爱多"
      price="20"
      intro="老板不在家, 全场1折"
    ></Product>
    <Product title="好贵的北京烤鸭" price="290" :intro="str"></Product>
  </div>
</template>

<script>
// 1. 创建组件 (.vue文件)
// 2. 引入组件
import Product from "./components/MyProduct";
export default {
  data() {
    return {
      str: "好贵啊, 快来啊, 好吃",
    };
  },
  // 3. 注册组件
  components: {
    // Product: Product // key和value变量名同名 - 简写
    Product,
  },
};
</script>

<style></style>
```

> 总结: 组件封装复用的标签和样式, 而具体数据要靠外面传入

### 2.1*vue 组件通信*父向子-配合循环

> 目的: 把数据循环分别传入给组件内显示

```vue
<template>
  <div>
    <MyProduct
      v-for="obj in list"
      :key="obj.id"
      :title="obj.proname"
      :price="obj.proprice"
      :intro="obj.info"
    ></MyProduct>
  </div>
</template>

<script>
// 目标: 循环使用组件-分别传入数据
// 1. 创建组件
// 2. 引入组件
import MyProduct from "./components/MyProduct";
export default {
  data() {
    return {
      list: [
        {
          id: 1,
          proname: "超级好吃的棒棒糖",
          proprice: 18.8,
          info: "开业大酬宾, 全场8折",
        },
        {
          id: 2,
          proname: "超级好吃的大鸡腿",
          proprice: 34.2,
          info: "好吃不腻, 快来买啊",
        },
        {
          id: 3,
          proname: "超级无敌的冰激凌",
          proprice: 14.2,
          info: "炎热的夏天, 来个冰激凌了",
        },
      ],
    };
  },
  // 3. 注册组件
  components: {
    // MyProduct: MyProduct
    MyProduct,
  },
};
</script>

<style></style>
```

### 2.2*vue 组件通信*单向数据流

在 vue 中需要遵循单向数据流原则

1. 父组件的数据发生了改变，子组件会自动跟着变
2. 子组件不能直接修改父组件传递过来的 props props 是只读的

==父组件传给子组件的是一个对象，子组件修改对象的属性，是不会报错的，对象是引用类型, 互相更新==

![](./image/%E5%8D%95%E5%90%91%E6%95%B0%E6%8D%AE%E6%B5%81.png)

> 总结: props 的值不能重新赋值, 对象引用关系属性值改变, 互相影响

问题: 那我怎么才能修改子组件接收到的值呢? - 其实要影响父亲, 然后数据响应式来影响儿子们

### 2.3*vue 组件通信*子向父

> 目标: 从子组件把值传出来给外面使用

语法:

- 父: @自定义事件名="父 methods 函数"
- 子: this.$emit("自定义事件名", 传值) - 执行父 methods 里函数代码

components/MyProduct_sub.vue

```vue
<template>
  <div class="my-product">
    <h3>标题: {{ title }}</h3>
    <p>价格: {{ price }}元</p>
    <p>{{ intro }}</p>
    <button @click="subFn">宝刀-砍1元</button>
  </div>
</template>

<script>
import eventBus from "../EventBus";
export default {
  props: ["index", "title", "price", "intro"],
  methods: {
    subFn() {
      this.$emit("subprice", this.index, 1); // 子向父
      eventBus.$emit("send", this.index, 1); // 跨组件
    },
  },
};
</script>

<style>
.my-product {
  width: 400px;
  padding: 20px;
  border: 2px solid #000;
  border-radius: 5px;
  margin: 10px;
}
</style>
```

App.vue

```vue
<template>
  <div>
    <!-- 目标: 子传父 -->
    <!-- 1. 父组件, @自定义事件名="父methods函数" -->
    <MyProduct
      v-for="(obj, ind) in list"
      :key="obj.id"
      :title="obj.proname"
      :price="obj.proprice"
      :intro="obj.info"
      :index="ind"
      @subprice="fn"
    ></MyProduct>
  </div>
</template>

<script>
import MyProduct from "./components/MyProduct_sub";
export default {
  data() {
    return {
      list: [
        {
          id: 1,
          proname: "超级好吃的棒棒糖",
          proprice: 18.8,
          info: "开业大酬宾, 全场8折",
        },
        {
          id: 2,
          proname: "超级好吃的大鸡腿",
          proprice: 34.2,
          info: "好吃不腻, 快来买啊",
        },
        {
          id: 3,
          proname: "超级无敌的冰激凌",
          proprice: 14.2,
          info: "炎热的夏天, 来个冰激凌了",
        },
      ],
    };
  },
  components: {
    MyProduct,
  },
  methods: {
    fn(inde, price) {
      // 逻辑代码
      this.list[inde].proprice > 1 &&
        (this.list[inde].proprice = (this.list[inde].proprice - price).toFixed(
          2
        ));
    },
  },
};
</script>

<style></style>
```

> 总结: 父自定义事件和方法, 等待子组件触发事件给方法传值

### 2.5_vue 组件通信-EventBus

> 目标: 常用于跨组件通信时使用

两个组件的关系非常的复杂，通过父子组件通讯是非常麻烦的。这时候可以使用通用的组件通讯方案：事件总线（event-bus)

![](./image/%E5%85%84%E5%BC%9F%E7%BB%84%E4%BB%B6%E4%BC%A0%E9%80%92%E9%80%BB%E8%BE%91.png)

核心语法

EventBus/index.js- 定义事件总线 bus 对象

```js
import Vue from "vue";
// 导出空白vue对象
export default new Vue();
```

components/MyProduct_sub.vue

```vue
<template>
  <div class="my-product">
    <h3>标题: {{ title }}</h3>
    <p>价格: {{ price }}元</p>
    <p>{{ intro }}</p>
    <button @click="subFn">宝刀-砍1元</button>
  </div>
</template>

<script>
import eventBus from "../EventBus";
export default {
  props: ["index", "title", "price", "intro"],
  methods: {
    subFn() {
      this.$emit("subprice", this.index, 1); // 子向父
      eventBus.$emit("send", this.index, 1); // 跨组件
    },
  },
};
</script>

<style>
.my-product {
  width: 400px;
  padding: 20px;
  border: 2px solid #000;
  border-radius: 5px;
  margin: 10px;
}
</style>
```

List.vue 代码(==EventBus 接收方==)

```vue
<template>
  <ul class="my-product">
    <li v-for="(item, index) in arr" :key="index">
      <span>{{ item.proname }}</span>
      <span>{{ item.proprice }}</span>
    </li>
  </ul>
</template>

<script>
// 目标: 跨组件传值
// 1. 引入空白vue对象(EventBus)
// 2. 接收方 - $on监听事件
import eventBus from "../EventBus";
export default {
  props: ["arr"],
  // 3. 组件创建完毕, 监听send事件
  created() {
    eventBus.$on("send", (index, price) => {
      this.arr[index].proprice > 1 &&
        (this.arr[index].proprice = (this.arr[index].proprice - price).toFixed(
          2
        ));
    });
  },
};
</script>

<style>
.my-product {
  width: 400px;
  padding: 20px;
  border: 2px solid #000;
  border-radius: 5px;
  margin: 10px;
}
</style>
```

> 总结: 空的 Vue 对象, 只负责$on注册事件, $emit触发事件, 一定要确保$on 先执行

## 面试题

### 1. 请说下封装 vue 组件的过程

​ 首先，组件可以提升整个项目的开发效率。能够把页面抽象成多个相对独立的模块，解决了我们传统项目开发：效率低、难维护、复用性等问题。

- 分析需求：确定业务需求，把页面中可以复用的结构，样式以及功能，单独抽离成一个组件，实现复用

- 具体步骤：Vue.component 或者在 new Vue 配置项 components 中, 定义组件名, 可以在 props 中接受给组件传的参数和值，子组件修改好数据后，想把数据传递给父组件。可以采用$emit 方法。

### 2. Vue 组件如何进行传值的

父向子 -> props 定义变量 -> 父在使用组件用属性给 props 变量传值

子向父 -> $emit 触发父的事件 -> 父在使用组件用@自定义事件名=父的方法 (子把值带出来)

### 3. Vue 组件 data 为什么必须是函数

每个组件都是 Vue 的实例, 为了独立作用域, 不让变量污染别人的变量

### 4. 讲一下组件的命名规范

​ 给组件命名有两种方式(在 Vue.Component/components 时)，一种是使用链式命名"my-component"，一种是使用大驼峰命名"MyComponent"，

​ 因为要遵循 W3C 规范中的自定义组件名 (字母全小写且必须包含一个连字符)，避免和当前以及未来的 HTML 元素相冲突.
