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

### 最简单的Store

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

**获取状态对象** - `store.state.count`

**触发状态改变** - `store.commit('increment')`

**注意** - 改变都是通过`commit`mutations里面的方法来实现的，目的，是为了使意图更加明显，能跟明确的追踪状态的变化，调试工具可以直观的看到状态的变化。

组件调用需要的状态只需要在计算属性里面返回。

触发改变也只需要在`methods`里面提交`mutation`。



### 五个核心概念

#### State - 单一状态树

作为一个**唯一数据源**存在

组件怎么访问store中的数据呢？有两种方式

**第一种**

引入，从实例中读取，计算属性返回

**第二种**

在vue根组件上注册store选项，该store实例会注册到根组件下的所有子组件

通过 `this.$store.state.count`获取数据



小程序因为每一个页面都是一个vue实例，所以需要使用第一种方式

vuex准备了一个辅助函数，帮助我们快速声明计算属性

#### `mapState`

用法如下

```javascript
computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
```

传一个对象给mapState，里面带有所有要声明的计算属性

也可以传一个字符串数组，当声明的计算属性的名称和state的子节点的名称相同时

**和局部的计算属性混用**使用扩展运算符

`mapState`返回的是一个对象

```javascript
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```



#### Getter - 可以认识是Store的计算属性

第一参数是state

第二个参数是其他的getters

可以通过属性访问`this.$store.getters.doneTodosCount`

也可以通过方法访问`store.getters.getTodoById(2)`让getter返回一个函数

通过属性访问的方式可以缓存到Vue的响应式系统

返回方法的这种方式，每次调用都会重新计算

也有辅助函数`mapGetters`用法同上



#### Mutation - 更改数据的唯一方法

这个东西类似于事件，每一个都有一个`type`和`handler`

这个handler是状态更改的地方，会接受state作为第一个参数，不能直接调用

要用响应的`type`	调用`store.commit`方法

**提交载荷payload** - 其实就是传参啦

第二个参数就是载荷了，可以是一个值，但是建议传一个对象，这样可以传多个值

还有一个对象风格的提交方式，就是把`type`也放在那个对象里面，键名就是`type`

handler不变，还是第一个参数是type，第二个是传入的参数对象

```javascript
store.commit({
  type: 'increment',
  amount: 10
})

mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

**mutation**需要遵守vue的相应规则

也就是说，最好提前声明好要用的属性

需要添加新属性的时候，使用`Vue.set(obj, 'newProp', 123)`或者使用新对象替代老对象`state.obj = { ...state.obj, newProp: 123 }`

**使用常量替代Mutation事件类型**

使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式。这样可以使 linter 之类的工具发挥作用，同时把这些常量放在**单独的文件中**可以让你的代码合作者对整个 app 包含的 mutation 一目了然。

```js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
```

```js
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

**mutation 必须是同步函数**

两种提交mutation的方式

1. 访问store的commit方法提交（小程序用这一种）

2. 在根组件上注册了store属性的，可以使用对象扩展的方式，使用`mapMutations`将组件中的methods映射为store.commit调用

   ```js
   import { mapMutations } from 'vuex'
   
   export default {
     // ...
     methods: {
       ...mapMutations([
         'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
   
         // `mapMutations` 也支持载荷：
         'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
       ]),
       ...mapMutations({
         add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
       })
     }
   }
   ```



#### Action - 异步函数都放这里哦

类似于mutation，但是有两点不同

它提交的是mutations，不是直接修改数据；可以包含任意异步操作

action函数接收的参数是一个与store实例具有相同方法和属性的**context**对象，但它不是store本身哦

可以使用**参数解构**来简化代码

```js
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

触发action使用`store.dispatch('increment')`

也支持载荷方式和对象方式

在组件中分发action也类似于mutation，两种方式一个直接调，一个是映射成`methods`

小程序的话直接调用

**组合多个action或者怎么知道异步什么时候结束呢**？

使用 [async / await](https://tc39.github.io/ecmascript-asyncawait/)

```js
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```



#### Module - 将store分割成多个模块

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
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

对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态对象**。

对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`
对于模块内部的 getter，根节点状态会作为第三个参数暴露出来

```js
getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
```

**命名空间**

默认情况下，模块内部的 action、mutation 和 getter 是注册在**全局命名空间**的——这样使得多个模块能够对同一 mutation 或 action 作出响应。

如果希望你的模块具有更高的封装度和复用性，你可以通过添加 `namespaced: true` 的方式使其成为带命名空间的模块。

**模块动态注册**

在 store 创建**之后**，你可以使用 `store.registerModule` 方法注册模块

**模块重用**

多个store使用同一个模块，类似于组件的data

```js
const MyReusableModule = {
  state () {
    return {
      foo: 'bar'
    }
  },
  // mutation, action 和 getter 等等...
}
```



目前就到此为止了







































