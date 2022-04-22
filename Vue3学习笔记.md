### Vue3学习笔记
***

### 初始化
vue2是通过实例化
```js
new Vue({
    el: '#app',
    mixins: [mymixins]],
    store,
    router: router,
    render: h => h(App)
});
```
vue3通过Vue上的createApp渲染模板，并挂载到dom上
当然，一些插件要在挂载之前完成
```js
import { createApp } from "vue";
createApp(app).use(router).use(store).mount("#app")
```

### vue实例方法
* 挂载组件 `createApp(app).component("icon", Icon)`
* 使用插件 `createApp(app).use(plugin)`
* 自定义指令 `createApp(app).directive("focus", FocusDirective)`
* 挂载dom `createApp(app).mount("#app")` **mount方法返回的是根组件实例，而不是应用本身**

### provide和inject跨级传递数据
```js
// A.vue
export default {
    provide() {
        return {
            name: "tom"
        }
    }
}
// D.vue
export default {
    inject: ['name'],
    created() {
        console.log(this.name)
    }
}

```

### 内置组件
component
transition
transition-group
keep-alive
slot
teleport

### setup
setup是围绕beforeCreate和created运行的，因此created周期里的（data，this，methods等都不可用）
但是可以接受父组件传递的props和context，因为这些参数本来也要赋值到vue实例this上的
