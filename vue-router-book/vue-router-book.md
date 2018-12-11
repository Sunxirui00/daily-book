## 路由

单页应用是由组件组合而成

而路由就是将组件映射到路由

#### 用法

```html
<router-link to="/foo">Go to Foo</router-link>
```

```html
<!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
```

#### 引入

```js
 import Vue from './vue'
 import VueRouter from './vue-router'
 Vue.use(VueRouter)
```

定义路由组件

```js
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
```

定义路由

```js
const routes = [
    { path:'/foo', component: Foo},
    { path:'/bar', component: Bar}
]
```

创建路由实例

```js
const router = new VueRouter({
    routers
})
```

创建挂载根实例

```js
var app = new Vue({
    router
}).$mount('#app')
```



`this.$router`可以访问路由器

`this.$route`可以访问本页路由

要注意，当 `<router-link>` 对应的路由匹配成功，将自动设置 class 属性值 `.router-link-active`。

### 动态路由匹配



