## Vue-router 的一般使用

一般在我们的项目中我们在package.json中配置

```json
 "dependencies": {
        "vue": "^1.0.21",
        "vue-router": "^0.7.13",
        "vue-resource": "^1.0.1",
        "babel-runtime": "^6.0.0",
        "better-scroll": "^0.1.7"
    },
```

配置好我们要的版本号，然后运行`npm install`  然后就在我们的`node_modeles`里面就有我们下载好的模块了

然后在我们的`main.js`中去引入我们的模块

```javascript
import VueRouter from 'vue-router';//引入页面路由模块
import VueResource from 'vue-resource';//引入获取网络资源的模块，相当于ajax模块
import goods from 'components/goods/goods.vue';//引入我们要进行展示的模块
import ratings from 'components/ratings/ratings.vue';
import seller from 'components/seller/seller.vue';

//引用模块
Vue.use(VueRouter);
Vue.use(VueResource);
//得到我们的vue组件
//如果是es5的话别用let  用var
let app = Vue.extend(App);
//可以个性化我们的V-link的class名称
let router = new VueRouter({
  linkActiveClass: 'active'
});
//设置路由
router.map({
  '/goods': {
    component: goods
  }
});
//路由开始
router.start(app, '#app');
//刚开始打开带有路由页面时我们要展示的默认路由页面
router.go('/goods');

```

在我们的`app.vue`页面中要这样子使用

```html
<template>
  <div>
    <div class="tab border-1px">
      <div class="tab-item">
        <a v-link="{ path:'/goods' }">商品</a>
      </div>
      <div class="tab-item">
        <a v-link="{ path:'/ratings' }">评论</a>
      </div>
      <div class="tab-item">
        <a v-link="{ path:'/seller' }">商家</a>
      </div>
    </div>
    这儿是一定要这个内置组件的，这个就是要展示我们的路由页面
    <router-view></router-view>
  </div>
</template>
```

要被引入的模版页面是这个样子的

```	html
<template>
  <div>I am goods</div>
</template>

<script type="text/ecmascript-6">
  export default {};
</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>

```

