---
title: Vuex
date: 2023-03-11 12:03:00
tags: 
    - Vue 
    - Vuex
categories: 
    - 前端开发
    - (四)前端工程化
    - 组件化开发框架
    - Vue
    - 状态管理 - Vuex
cover: https://pic.imgdb.cn/item/640c26e9f144a01007c8dca2.jpg
---
# VueX

> 参考链接：[手把手教你使用Vuex，猴子都能看懂的教程 - 掘金 (juejin.cn)](https://juejin.cn/post/6928468842377117709#heading-0)
>
> 官网链接：[Vuex 是什么？ | Vuex (vuejs.org)](https://v3.vuex.vuejs.org/zh/)  (Vue2 匹配的 Vuex3)
>
> ​				    [Vuex 是什么？ | Vuex (vuejs.org)](https://vuex.vuejs.org/zh/) (Vue3 匹配的 Vuex4)

## 一、了解Vuex

### 1、场景

当项目里有很多页面（组件/视图），页面之间存在多级的嵌套关系，此时，这些页面假如都需要共享一个状态的时候，此时就会产生以下两个问题：

- 多个视图依赖同一个状态
- 来自不同视图的行为需要变更同一个状态

###  2、解决方法

- 对于第一个问题，假如是多级嵌套关系，可以使用父子组件传参进行解决，虽有些麻烦，但好在可以解决；对于兄弟组件或者关系更复杂组件之间，就很难办了，虽然可以通过各种各样的办法解决，可实在很不优雅，而且等项目做大了，代码就会变成一座山，实在令人心烦。
- 对于第二个问题，可以通过父子组件直接引用，或者通过事件来变更或者同步状态的多份拷贝，这种模式很脆弱，往往使得代码难以维护，而且同样会让代码便会堆积如山。

### 3、换个思路

- 把各个组件都需要依赖的同一个状态抽取出来，在全局使用单例模式进行管理。
- 在这种模式下，任何组件都可以直接访问到这个状态，或者当状态发生改变时，所有的组件都获得更新。

### 4、vuex诞生

Vuex 背后的基本思想，借鉴了 Flux、Redux。与其他模式不同的是，Vuex 是专门为 Vue 设计的状态管理库，以利用 Vue.js 的**细粒度数据响应机制**来进行高效的状态更新。

### 5、vuex使用周期图

![vuex使用周期图](https://pic.imgdb.cn/item/640bfe3ff144a010077a624e.jpg)

### 6、什么时候应该用vuex

- 如果项目中不需要开发大型的单页应用，此时完全没有必要使用vuex，比如页面就两三个，使用vuex后增加的文件比现在的页面还要多，那就没这个必要了。

- 假如你的项目达到了**中大型**应用的规模，此时您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。

## 二、安装

### 1、安装

进入项目，在命令行中输入安装指令，回车

```
npm install vuex --save
```

### 2、配置

- 配置 vuex：在src路径下创建store文件夹，然后创建index.js文件，文件内容如下：

```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    // 定义一个name，以供全局使用
    name: '张三',
    // 定义一个number，以供全局使用
    number: 0,
    // 定义一个list，以供全局使用
    list: [
      { id: 1, name: '111' },
      { id: 2, name: '222' },
      { id: 3, name: '333' },
    ],
  },
});

export default store;
```

- 修改main.js：

```js
import Vue from 'vue';
import App from './App';
import router from './router';
import store from './store'; // 引入我们前面导出的store对象

Vue.config.productionTip = false;

new Vue({
  el: '#app',
  router,
  store, // 把store对象添加到vue实例上
  components: { App },
  template: '<App/>',
});
```

- 最后修改App.vue：

```html
<template>
  <div></div>
</template>

<script>
export default {
  mounted() {
    // 使用this.$store.state.XXX可以直接访问到仓库中的状态
    console.log(this.$store.state.name);
  },
};
</script>
```

### 3、运行

此时，启动项目`npm run dev`，即可在控制台输出定义在store中的name的值。

```js
//输出
张三
```

### 4、{mapState}

- **1、this.$store.state.XXX最好放在计算属性中**

```js
export default {
  mounted() {
    console.log(this.getName);
  },
  computed: {
    getName() {
      return this.$store.state.name;
    },
  },
};
```

- 2、**{mapState}**

```html
<script>
import { mapState } from 'vuex'; // 从vuex中导入mapState
export default {
  mounted() {
    console.log(this.name);
  },
  computed: {
    ...mapState(['name']), // 经过解构后，自动就添加到了计算属性中，此时就可以直接像访问计算属性一样访问它
  },
};
</script>
```

- 解构时，可以赋别名

```js
...mapState({ aliasName: 'name' }),  // 赋别名的话，这里接收对象，而不是数组
```

## 三、修饰器：Getter

首先，在store对象中增加getters属性

```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    name: '张三',
    number: 0,
    list: [
      { id: 1, name: '111' },
      { id: 2, name: '222' },
      { id: 3, name: '333' },
    ],
  },
  // 在store对象中增加getters属性
  getters: {
    getMessage(state) {
      // 获取修饰后的name，第一个参数state为必要参数，必须写在形参上
      return `hello${state.name}`;
    },
  },
});

export default store;
```

在组件中使用：

```js
export default {
  mounted() {
    // 注意不是$store.state了，而是$store.getters
    console.log(this.$store.state.name);
    console.log(this.$store.getters.getMessage);
  },
};
复制代码
```

然后查看控制台：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c3e26092fc444753a0904e527af8ba47~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### {mapGetters}

- **{mapGetters}**

```html
<script>
import { mapState, mapGetters } from 'vuex';
export default {
  mounted() {
    console.log(this.name);
    console.log(this.getMessage);
  },
  computed: {
    ...mapState(['name']),
    ...mapGetters(['getMessage']),
  },
};
</script>
```

- 赋别名

```js
...mapGetters({ aliasName: 'getMessage' }),  // 赋别名的话，这里接收对象，而不是数组
```

## 四、如何修改值：Mutation

- 修改store/index.js

```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    name: '张三',
    number: 0,
  },
  mutations: {
    // 增加Mutations属性
    setNumber(state) {
      // 增加一个mutations的方法，方法的作用是让num从0变成5，state是必须默认参数
      state.number = 5;
    },
  },
});

export default store;
```

- 修改App.vue

```html
<script>
export default {
  mounted() {
    console.log(`旧值：${this.$store.state.number}`);
    this.$store.commit('setNumber');
    console.log(`新值：${this.$store.state.number}`);
  },
};
</script>
```

- 运行项目，查看控制台：

```js
//输出
旧值：0
新值：5
```

### 传不固定参数

- 修改store/index.js

```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    name: '张三',
    number: 0,
  },
  mutations: {
    setNumber(state) {
      state.number = 5;
    },
    setNumberIsWhat(state, number) {
      // 增加一个带参数的mutations方法
      state.number = number;
    },
  },
});

export default store;
```

- 修改App.vue

```html
<script>
export default {
  mounted() {
    console.log(`旧值：${this.$store.state.number}`);
    this.$store.commit('setNumberIsWhat', 666);
    console.log(`新值：${this.$store.state.number}`);
  },
};
</script>
```

- 运行项目，查看控制台：

```js
//输出
旧值：0
新值：666
```

### 带参：payload； 解构： {mapMutations}

**1、官方建议传递一个对象进去，这样看起来更美观，对象的名字你可以随意命名，但我们一般命名为payload，代码如下：**

- 修改store/index.js

```js
mutations: {
    setNumber(state) {
      state.number = 5;
    },
    setNumberIsWhat(state, payload) {
      // 增加一个带参数的mutations方法，并且官方建议payload为一个对象
      state.number = payload.number;
    },
  },
```

- 修改App.vue

```html
<script>
export default {
  mounted() {
    console.log(`旧值：${this.$store.state.number}`);
    this.$store.commit('setNumberIsWhat', { number: 666 }); // 调用的时候也需要传递一个对象
    console.log(`新值：${this.$store.state.number}`);
  },
};
</script>
```

- 此时可以得到和之前一样的效果，并且代码更加美观！

- **这里说一条重要原则：Mutations里面的函数必须是同步操作，不能包含异步操作！**

**2、就像最开始的mapState和mapGetters一样，我们在组件中可以使用mapMutations以代替this.$store.commit('XXX')，是不是很方便呢？**

```html
<script>
import { mapMutations } from 'vuex';
export default {
  mounted() {
    this.setNumberIsWhat({ number: 999 });
  },
  methods: {
    // 注意，mapMutations是解构到methods里面的，而不是计算属性了
    ...mapMutations(['setNumberIsWhat']),
  },
};
</script>
```

- 赋别名

```js
methods: {
  ...mapMutations({ setNumberIsAlias: 'setNumberIsWhat' }), // 赋别名的话，这里接收对象，而不是数组
},
```

## 五、异步操作：Actions

修改store/index.js

```js
const store = new Vuex.Store({
  state: {
    name: '张三',
    number: 0,
  },
  mutations: {
    setNumberIsWhat(state, payload) {
      state.number = payload.number;
    },
  },
  actions: {
    // 增加actions属性
    setNum(content) {
      // 增加setNum方法，默认第一个参数是content，其值是复制的一份store
      return new Promise(resolve => {
        // 我们模拟一个异步操作，1秒后修改number为888
        setTimeout(() => {
          content.commit('setNumberIsWhat', { number: 888 });
          resolve();
        }, 1000);
      });
    },
  },
});
```

修改App.vue

```js
async mounted() {
  console.log(`旧值：${this.$store.state.number}`);
  await this.$store.dispatch('setNum');
  console.log(`新值：${this.$store.state.number}`);
},
```

> action就是去提交mutation的，什么异步操作都在action中消化了，最后再去提交mutation的。
> 也可以模仿mutation进行传参，就像下面这样：

修改store/index.js

```js
actions: {
  setNum(content, payload) {
    // 增加payload参数
    return new Promise(resolve => {
      setTimeout(() => {
        content.commit('setNumberIsWhat', { number: payload.number });
        resolve();
      }, 1000);
    });
  },
},
```

修改App.vue

```js
async mounted() {
  console.log(`旧值：${this.$store.state.number}`);
  await this.$store.dispatch('setNum', { number: 611 });
  console.log(`新值：${this.$store.state.number}`);
},
```

### {mapActions}

**1、你如果不想一直使用this.$store.dispatch('XXX')这样的写法调用action，你可以采用mapActions的方式，把相关的actions解构到methods中，用this直接调用：**

```js
<script>
import { mapActions } from 'vuex';
export default {
  methods: {
    ...mapActions(['setNum']), // 就像这样，解构到methods中
  },
  async mounted() {
    await this.setNum({ number: 123 }); // 直接这样调用即可
  },
};
</script>
```

赋别名

```js
...mapActions({ setNumAlias: 'setNum' }),   // 赋别名的话，这里接收对象，而不是数组
```

**2、在store/index.js中的actions里面，方法的形参可以直接将commit解构出来，这样可以方便后续操作：**

```js
actions: {
  setNum({ commit }) {
    // 直接将content结构掉，解构出commit，下面就可以直接调用了
    return new Promise(resolve => {
      setTimeout(() => {
        commit('XXXX'); // 直接调用
        resolve();
      }, 1000);
    });
  },
},
```

**注**：不要将action和mutation混为一谈，action其实就是mutation的上一级，在action这里处理完异步的一些操作后，后面的修改state就交给mutation去做。

## 六、按属性进行拆分

新建四个文件，分别是state.js getters.js mutations.js actions.js：

### 1、拆出来state放到state.js中：

```js
// state.js

export const state = {
  name: '张三',
  number: 0,
  list: [
    { id: 1, name: '111' },
    { id: 2, name: '222' },
    { id: 3, name: '333' },
  ],
};
```

### 2、拆出来getters放到getters.js中：

```js
// getters.js

export const getters = {
  getMessage(state) {
    return `hello${state.name}`;
  },
};
```

### 3、拆出来mutations放到mutations.js中：

```js
// mutations.js

export const mutations = {
  setNumber(state) {
    state.number = 5;
  },
};
```

### 4、拆出来actions放到actions.js中：

```js
// actions.js

export const actions = {
  setNum(content) {
    return new Promise(resolve => {
      setTimeout(() => {
        content.commit('setNumberIsWhat', { number: 888 });
        resolve();
      }, 1000);
    });
  },
};
```

### 5、组装到主文件里面：

```js
import Vue from 'vue';
import Vuex from 'vuex';
import { state } from './state'; // 引入 state
import { getters } from './getters'; // 引入 getters
import { mutations } from './mutations'; // 引入 mutations
import { actions } from './actions'; // 引入 actions

Vue.use(Vuex);

const store = new Vuex.Store({
  state: state,
  getters: getters,
  mutations: mutations,
  actions: actions,
});

// 可以简写成下面这样：

// const store = new Vuex.Store({ state, getters, mutations, actions });

export default store;
```

## 七、按功能进行拆分 - Module

### 1、官方文档

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

### 2、代码实现

- 1、我们在之前的store上，增加一个新的仓库store2，主要代码如下：

```js
// store2.js

const store2 = {
  state: {
    name: '我是store2',
  },
  mutations: {},
  getters: {},
  actions: {},
};

export default store2;
```

- 2、然后在store中引入我们新创建的store2模块：

```js
import Vue from 'vue';
import Vuex from 'vuex';
import { state } from './state';
import { getters } from './getters';
import { mutations } from './mutations';
import { actions } from './actions';
import store2 from './store2'; // 引入store2模块

Vue.use(Vuex);

const store = new Vuex.Store({
  modules: { store2 }, // 把store2模块挂载到store里面
  state: state,
  getters: getters,
  mutations: mutations,
  actions: actions,
});

export default store;
```

- 3、访问state - 我们在App.vue测试访问store2模块中的state中的name，结果如下：

```vue
<template>
  <div></div>
</template>
```

```vue
<script>
export default {
  mounted() {
    console.log(this.$store.state.store2.name); // 访问store2里面的name属性
  },
};
</script>
```


我们通过下面的代码可以了解到在不同的属性里是怎么访问 模块内的状态 或者 根状态：

```js
mutations: {
  changeName(state, payload) {
    // state 局部状态
    console.log(state);
    console.log(payload.where);
  },
},
getters: {
  testGetters(state, getters, rootState) {
    // state 局部状态
    console.log(state);
    // 局部 getters,
    console.log(getters);
    // rootState 根节点状态
    console.log(rootState);
  },
},
actions: {
  increment({ state, commit, rootState }) {
    // state 局部状态
    console.log(state);
    // rootState 根节点状态
    console.log(rootState);
  },
},
```

以上是对module的简单介绍，其实这里就是一种思想，分而治之，将复杂的进行拆分，可以更有效的管理。

> 其实以上并不是module的全部，还有一些比如命名空间、模块注册全局 action、带命名空间的绑定函数、模块动态注册、模块重用等方法这里就没介绍，如果你在项目中使用到了，再进行查阅即可，有时候不需要完全理解，知道有这个东西就行，知道出了问题的时候该去哪查资料就够啦。

## 八、总结

- 安装
- 配置
- 读取state的值
- store 的计算属性(Getter)
- 修改值(Mutation)
- 异步操作(Action)

基本上会上述内容，那在项目中就可以用到vuex了。



