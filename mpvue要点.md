### 框架原理

1. 生命周期
   * vue的生命周期是在`onReady`之后
   * 有一些特殊的小程序的生命周期
     * 进入后台`onHide`、进入前台`onShow`
     * 下拉动作`onPullDownRefresh`、上拉触底`onReachBottom`
     * 右上角分享`onShareAppMessage`
     * 页面滚动`onPageScroll`
     * 点击tab`onTabItemTap`
2. 箭头函数
   * 不要在访问实例属性时、和传入回调函数时使用
3. query 用`this.$root.$mp.query`获取，记得要在`onLoad`生命周期之后使用

### 生命周期图示

![mpvue生命周期](lifecycle-mpvue.jpg)

### 语法的兼容性

#### 不支持

1. 小程序里面的DOM和BOM都不能用，不能用v-html【可以考虑使用可以用`<rich-text>`组件或者`wxParse`(<https://github.com/icindy/wxParse>)来实现。】

2. 模板语法不支持复杂的表达式，只支持

   ```javascript
   + - * % ? : ! ==  ===   > < [ ] . 
   ```

   建议写成计算属性

3. 不支持过滤器

4. 不支持在`template`里面使用`methods`中的函数【只有事件监听可以调】

5. Class和Style不支持`classObject` 和 `styleObject`的形式，直接绑定一个data里面的对象数据【考虑性能，还是使用计算属性生成字符串吧~~】

6. 不支持在组件上使用 Class 与 Style 绑定

7. 在 `input` 和 `textarea` 中 `change` 事件会被转为 `blur` 事件。其余的事件几乎都支持的

8. 事件修饰符：`.stop`的使用会阻止冒泡，但是同时绑定了一个非冒泡事件，会导致该元素上的 catchEventName ;`.capture`支持1.0.9;**别的都不支持**【不行了就查文档，这里**坑很多**】

9. 组件只支持`.vue`的形式

10. 路由只能用原生的！【**vue-router不能用了**】请使用`<a>`标签和小程序原生API `wx.navigateTo`等来做路由功能。

11. 请求后端数据，我们通常在Web开发中使用`axios`等ajax库来实现，但是在小程序开发中也是不能用的，也请使用小程序的原生API `wx.request`等来进行。

#### 注意点

1. 支持小程序的原生组件，但是组件上的事件写法要按照VUE的写法来写

   ```html
   bindchange="eventName" 
   <!-- 事件，需要写成 -->
   @change="eventName"
   ```

2. 事件取值的写法`event.mp.detail = {value: value}``e.detail.value`

3. 表单几乎都支持，还没有出现不支持的，但是推荐使用微信小程序原生的表单组件

4. **列表**渲染只是需要注意一点，**【注意】嵌套列表渲染，必须指定不同的索引！**

5. 还可以用 computed 方法生成 class 或者 style 字符

#### 最佳实践

1. 生产地址和开发地址的转换[教程](https://www.jianshu.com/p/2e98cc166dbd)

   那你在编写请求后端API的代码时，你就可以使用这个环境配置，像这样：

   ```javascript
   const baseURL = process.env.API_BASE_URL
   wx.request({
     url: `${baseURL}/products`
   })
   ```

2. 目录结构

   * components可复用的部分
   * pages存放小程序的页面
   * utils工具函数
   * main.js + App.vue这两个是入口文件

3. `App.mpType = 'app'`是啥意思

   这个值是为了与后面要讲的小程序页面组件所区分开来，因为小程序页面组件和这个`App.vue`组件的写法和引入方式是一致的，为了区分两者，需要设置`mpType`值。

4. 每一个小程序页面的结构（相同）和配置（可以不同）

5. 显式的去指定首页

   ```javascript
   pages: [
     '^pages/index/main'
   ]
   ```

6. Vue组件的编写原则之一

   > 避免向外部暴露过多的东西，只暴露必要的外部交互接口（组件属性、事件、方法等）。

#### mpvue与VUEX的结合使用

**用法**：直接在组件中通过`import`导入store所在的模块文件，然后调用该store上的相关方法和属性，比如`commit()`、`dispatch()`等方法来操作store中的内容。

**为什么这么用**：微信小程序是多实例，Web是单页，所以….

**global-store.js**

```javascript
import Vue from 'vue'
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment: (state) => {
      state.count += 1
    },
    decrement: (state) => {
      state.count -= 1
    }
  }
});
```

然后在想使用这个store的页面

```javascript
import globalStore from "../../stores/global-store";

export default {
  computed: {
    count() {
      return globalStore.state.count;//取值
    }
  },
  methods: {
    hanleIncrement() {
      globalStore.commit("increment");//改值
    }
  }
};
```

**也可以在`src/stores`目录下按需创建多个store模块，独立管理不同业务范围的数据，并按需导入页面组件使用。**























