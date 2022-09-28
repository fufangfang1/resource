## 1. 组件进阶

### 1.0 组件进阶 - 动态组件

> 目标: 多个组件使用同一个挂载点，并动态切换，这就是动态组件

需求: 完成一个注册功能页面, 2 个按钮切换, 一个填写注册信息, 一个填写用户简介信息

效果如下:

![动态组件](./image/动态组件.gif)

```vue
<template>
  <div>
    <button @click="comName = 'UserName'">账号密码填写</button>
    <button @click="comName = 'UserInfo'">个人信息填写</button>

    <p>下面显示注册组件-动态切换:</p>
    <div style="border: 1px solid red;">
      <component :is="comName"></component>
    </div>
  </div>
</template>

<script>
// 目标: 动态组件 - 切换组件显示
// 场景: 同一个挂载点要切换 不同组件 显示
// 1. 创建要被切换的组件 - 标签+样式
// 2. 引入到要展示的vue文件内, 注册
// 3. 变量-承载要显示的组件名
// 4. 设置挂载点<component :is="变量"></component>
// 5. 点击按钮-切换comName的值为要显示的组件名

import UserName from "../components/01/UserName";
import UserInfo from "../components/01/UserInfo";
export default {
  data() {
    return {
      comName: "UserName",
    };
  },
  components: {
    UserName,
    UserInfo,
  },
};
</script>
```

> 总结: vue 内置 component 组件, 配合 is 属性, 设置要显示的组件名字

### 1.1 组件进阶 - 组件缓存

组件切换会导致组件被频繁销毁和重新创建, 性能不高,
使用 Vue 内置的 keep-alive 组件, 可以让包裹的组件保存在内存中不被销毁

> 语法:

​ Vue 内置的 keep-alive 组件 包起来要频繁切换的组件

```vue
<div style="border: 1px solid red;">
    <!-- Vue内置keep-alive组件, 把包起来的组件缓存起来 -->
    <keep-alive>
        <component :is="comName"></component>
    </keep-alive>
</div>
```

补充生命周期:

- activated - 激活
- deactivated - 失去激活状态

> 总结: keep-alive 可以提高组件的性能, 内部包裹的标签不会被销毁和重新创建, 触发激活和非激活的生命周期方法。

### 1.2 组件进阶 - 组件插槽

> 目标: 用于实现组件的内容分发, 通过 slot 标签, 可以接收到写在组件标签内的内容

vue 提供组件插槽能力, 允许开发者在封装组件时，把不确定的部分定义为插槽

语法口诀:

1. 组件内用<slot></slot>占位
2. 使用组件时<Pannel></Pannel>夹着的地方, 传入标签替换 slot

03/Pannel.vue - 组件

```vue
<template>
  <div>
    <!-- 按钮标题 -->
    <div class="title">
      <h4>芙蓉楼送辛渐</h4>
      <span class="btn" @click="isShow = !isShow">
        {{ isShow ? "收起" : "展开" }}
      </span>
    </div>
    <!-- 下拉内容 -->
    <div class="container" v-show="isShow">
      <!-- 组件内 <slot></slot> 占位 -->
      <slot>默认显示的内容</slot>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isShow: false,
    };
  },
};
</script>

<style scoped>
h3 {
  text-align: center;
}

.title {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #ccc;
  padding: 0 1em;
}

.title h4 {
  line-height: 2;
  margin: 0;
}

.container {
  border: 1px solid #ccc;
  padding: 0 1em;
}

.btn {
  /* 鼠标改成手的形状 */
  cursor: pointer;
}

img {
  width: 50%;
}
</style>
```

views/03_UseSlot.vue - 组件插槽使用

```vue
<template>
  <div id="container">
    <div id="app">
      <h3>案例：折叠面板</h3>
      <Pannel>
        <img src="../assets/mm.gif" alt="" />
        <span>我是内容</span>
      </Pannel>
      <Pannel>
        <p>寒雨连江夜入吴,</p>
        <p>平明送客楚山孤。</p>
        <p>洛阳亲友如相问，</p>
        <p>一片冰心在玉壶。</p>
      </Pannel>
      <Pannel></Pannel>
    </div>
  </div>
</template>

<script>
import Pannel from "../components/03/Pannel";
export default {
  components: {
    Pannel,
  },
};
</script>
```

> 总结

组件内容分发技术, slot 占位, 使用组件时传入替换 slot 位置的标签,slot 夹着内容默认显示内容, 如果不给插槽 slot 传东西, 则使用 slot 夹着的内容在原地显示。

### 1.3 组件进阶 - 具名插槽

当一个组件内有 2 处以上需要外部传入标签的地方,传入的标签可以分别派发给不同的 slot 位置

v-slot 一般用跟 template 标签搭配使用 (template 是 html5 新出标签内容模板元素, 不会渲染到页面上, 一般被 vue 解析内部标签)

components/04/Pannel.vue - 留下具名 slot

```vue
<template>
  <div>
    <!-- 按钮标题 -->
    <div class="title">
      <slot name="title"></slot>
      <span class="btn" @click="isShow = !isShow">
        {{ isShow ? "收起" : "展开" }}
      </span>
    </div>
    <!-- 下拉内容 -->
    <div class="container" v-show="isShow">
      <slot name="content"></slot>
    </div>
  </div>
</template>
```

views/04_UseSlot.vue 使用

```vue
<template>
  <div id="container">
    <div id="app">
      <h3>案例：折叠面板</h3>
      <Pannel>
        <template v-slot:title>
          <h4>芙蓉楼送辛渐</h4>
        </template>
        <template v-slot:content>
          <img src="../assets/mm.gif" alt="" />
          <span>我是内容</span>
        </template>
      </Pannel>
      <!-- 第二种写法 -->
      <Pannel>
        <template #title>
          <span style="color: red;">我是标题</span>
        </template>
        <template #content>
          <p>寒雨连江夜入吴,</p>
          <p>平明送客楚山孤。</p>
          <p>洛阳亲友如相问，</p>
          <p>一片冰心在玉壶。</p>
        </template>
      </Pannel>
    </div>
  </div>
</template>

<script>
import Pannel from "../components/04/Pannel";
export default {
  components: {
    Pannel,
  },
};
</script>
```

> v-bind 可以省略成 ‘:’， v-on: 可以省略成 ‘@’， v-slot: 可以简化成#

> 总结: slot 的 name 属性起插槽名, 使用组件时, template 配合#插槽名传入具体标签

### 1.4 组件进阶 - 作用域插槽

子组件里值, 在给插槽赋值时在父组件环境下使用

或者说：

默认内容在子组件中, 但是父亲在给插槽传值, 想要改变插槽显示的默认内容

口诀:

1. 子组件, 在 slot 上绑定属性和子组件内的值
2. 使用组件, 传入自定义标签, 用 template 和 v-slot="自定义变量名"
3. scope 变量名自动绑定 slot 上所有属性和值

components/05/Pannel.vue - 定义组件, 和具名插槽, 给 slot 绑定属性和值

```vue
<template>
  <div>
    <!-- 按钮标题 -->
    <div class="title">
      <h4>芙蓉楼送辛渐</h4>
      <span class="btn" @click="isShow = !isShow">
        {{ isShow ? "收起" : "展开" }}
      </span>
    </div>
    <!-- 下拉内容 -->
    <div class="container" v-show="isShow">
      <slot :row="defaultObj">{{ defaultObj.defaultOne }}</slot>
    </div>
  </div>
</template>

<script>
// 1. slot标签, 自定义属性和内变量关联
// 2. 使用组件, template配合v-slot="变量名"
// 变量名会收集slot身上属性和值形成对象
export default {
  data() {
    return {
      isShow: false,
      defaultObj: {
        defaultOne: "无名氏",
        defaultTwo: "小传同学",
      },
    };
  },
};
</script>
```

views/05_UseSlot.vue

```vue
<template>
  <div id="container">
    <div id="app">
      <h3>案例：折叠面板</h3>
      <Pannel>
        <!-- 需求: 插槽时, 使用组件内变量 -->
        <!-- scope变量: {row: defaultObj} -->
        <template v-slot="scope">
          <p>{{ scope.row.defaultTwo }}</p>
        </template>
      </Pannel>
    </div>
  </div>
</template>

<script>
import Pannel from "../components/05/Pannel";
export default {
  components: {
    Pannel,
  },
};
</script>
```

> 总结

组件内变量绑定在 slot 上, 然后使用组件 v-slot="变量" 变量上就会绑定 slot 身上属性和值

插槽可以自定义标签, 作用域插槽可以把组件内的值取出来自定义内容

v-slot:default=“scope”的两种简写形式：

1.  将 v-slot：简写为“#”，=>#default='scope'
2.  将：和 default 省略,=>v-slot='scope'

## 2. 自定义指令

[自定义指令文档](https://www.vue3js.cn/docs/zh/guide/custom-directive.html)

除了核心功能默认内置的指令 (`v-model` 和 `v-show`)，Vue 也允许注册自定义指令。 `v-xxx`

如果对普通 DOM 元素进行底层操作，这时候就会用到自定义指令

### 2.0 自定义指令-注册

> 目标: 获取标签, 扩展额外的功能

> ### 局部注册和使用

07_UseDirective.vue - 只能在当前组件.vue 文件中使用

```vue
<template>
  <div>
    <!-- <input type="text" v-gfocus> -->
    <input type="text" v-focus />
  </div>
</template>

<script>
// 目标: 创建 "自定义指令", 让输入框自动聚焦
// 1. 创建自定义指令
// 全局 / 局部
// 2. 在标签上使用自定义指令  v-指令名
// 注意:
// inserted方法 - 指令所在标签, 被插入到网页上触发(一次)
// update方法 - 指令对应数据/标签更新时, 此方法执行
export default {
  data() {
    return {
      colorStr: "red",
    };
  },
  directives: {
    focus: {
      inserted(el) {
        el.focus();
      },
    },
  },
};
</script>

<style></style>
```

> ### 全局注册

在 main.js 用 Vue.directive()方法来进行注册, 以后随便哪个.vue 文件里都可以直接用 v-gfocus 指令

```js
// 全局指令 - 到处"直接"使用
Vue.directive("gfocus", {
  inserted(el) {
    el.focus(); // 触发标签的事件方法
  },
});
```

> 总结: 全局注册自定义指令, 哪里都能用, 局部注册, 只能在当前 vue 文件里用

### 2.1 自定义指令-传值

> 目标: 使用自定义指令, 传入一个值

需求: 定义 color 指令-传入一个颜色, 给标签设置文字颜色

main.js 定义处修改一下

```js
// 目标: 自定义指令传值
Vue.directive("color", {
  inserted(el, binding) {
    el.style.color = binding.value;
  },
  update(el, binding) {
    el.style.color = binding.value;
  },
});
```

Direct.vue 处更改一下

```vue
<p v-color="colorStr" @click="changeColor">修改文字颜色</p>

<script>
data() {
  return {
    theColor: "red",
  };
},
methods: {
  changeColor() {
    this.theColor = 'blue';
  },
},
</script>
```

> 总结: v-xxx, 自定义指令, 获取原生 DOM, 自定义操作

## 面试题

### 1. vue 中 solt 的使用方式，以及 solt 作用域插槽的用法

使用方式：当组件当做标签进行使用的时候，用 slot 可以用来接受组件标签包裹的内容，当给 solt 标签添加 name 属性的 时候，可以调换响应的位置
(高级用法) 插槽作用域： 当传递的不是单一的标签, 例如需要循环时, 把要循环的标签传入, 组件内使用 v-for 在 slot 标签上, 内部可以 v-bind:把值传出来, 再外面把值赋予进去, 看示例

```js
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>

// current-user组件, user属性和值, 绑定给slotProps上
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

> 扩展阅读: https://cn.vuejs.org/v2/guide/components-slots.html (了解即可, 一般用不上)

### 2. 跟 keep-alive 有关的生命周期是哪些？

​ **1\*\***）前言：\*\*在开发 Vue 项目的时候，大部分组件是没必要多次渲染的，所以 Vue 提供了一个内置组件 keep-alive 来缓存组件内部状态，避免重新渲染，在开发 Vue 项目的时候，大部分组件是没必要多次渲染的，所以 Vue 提供了一个内置组件 keep-alive 来缓存组件内部状态，避免重新渲染

​ **2\*\***）生命周期函数：\*\*在被 keep-alive 包含的组件/路由中，会多出两个生命周期的钩子:activated 与 deactivated。

​ **1\*\***、activated 钩子：\*\*在在组件第一次渲染时会被调用，之后在每次缓存组件被激活时调用。

​ **2\*\***、Activated 钩子调用时机：\*\* 第一次进入缓存路由/组件，在 mounted 后面，beforeRouteEnter 守卫传给 next 的回调函数之前调用，并且给因为组件被缓存了，再次进入缓存路由、组件时，不会触发这些钩子函数，beforeCreate created beforeMount mounted 都不会触发

​ **1\*\***、deactivated 钩子：\*\*组件被停用（离开路由）时调用。

​ **2\*\***、deactivated 钩子调用时机\*\*：使用 keep-alive 就不会调用 beforeDestroy(组件销毁前钩子)和 destroyed(组件销毁)，因为组件没被销毁，被缓存起来了，这个钩子可以看作 beforeDestroy 的替代，如果你缓存了组件，要在组件销毁的的时候做一些事情，可以放在这个钩子里，组件内的离开当前路由钩子 beforeRouteLeave => 路由前置守卫 beforeEach =>全局后置钩子 afterEach => deactivated 离开缓存组件 => activated 进入缓存组件(如果你进入的也是缓存路由)

### 3. 自定义指令(v-check、v-focus)的方法有哪些?它有哪些钩子函数?还有哪些钩子函数参数?

​ 全局定义指令：在 vue 对象的 directive 方法里面有两个参数，一个是指令名称，另外一个是函数。组件内定义指令：directives

​ 钩子函数：bind(绑定事件触发)、inserted(节点插入的时候触发)、update(组件内相关更新)

​ 钩子函数参数：el、binding

### 4. is 这个特性你有用过吗？主要用在哪些方面？

** 1\*\***）动态组件\*\*

​ <component :is="componentName"></component>， componentName 可以是在本页面已经注册的局部组件名和全局组件名,也可以是一个组件的选项对象。 当控制 componentName 改变时就可以动态切换选择组件。

** 2\*\***）is 的用法\*\*

​ 有些 HTML 元素，诸如 ul、ol、table 和 select，对于哪些元素可以出现在其内部是有严格限制的。

​ 而有些 HTML 元素，诸如 li、tr 和 option，只能出现在其它某些特定的元素内部。

```js
​ <ul>

​ <card-list></card-list>

​ </ul>
```

​ 所以上面 card-list 会被作为无效的内容提升到外部，并导致最终渲染结果出错。应该这么写：

```js
​ <ul>

​ <li is="cardList"></li>

​ </ul>
```
