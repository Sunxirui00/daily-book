### 是什么？

#### Vuex

给vue.js用的**状态管理模式**，集中式储存，有一套自己的规则，保证状态的变化可预测

#### 状态管理模式

包括三部分：

1. `state`:数据源
2. `view`:以声明的方式将state映射到view
3. `actions`:响应变化（用户输入导致的）

### 有什么用？

用一个全局单例模式管理——共享状态，任何组件都可以方便的获取状态和触发行为。

但是带来了一些问题，对于小项目来说，繁琐。可以考虑`store`模式

###最简单的Store

#### 创建

```javascript
Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

